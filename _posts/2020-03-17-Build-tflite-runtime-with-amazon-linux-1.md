---
layout: post
title: Build tflite runtime with amazon linux 1
categories: tensorflow
description: Build tflite runtime with amazon linux 1
keywords: TensorFlow
---

# 在AWS Lambda上面跑TensorFlow Lite的时候遇到这个问题

```
No module named '_interpreter_wrapper'
```

因为在本地是好的，所以觉得很奇怪。故Google之，发现这个：https://github.com/tensorflow/tensorflow/issues/31369#issuecomment-527217042
想着也不确定是不是对的，所以打算在本地重现一下。

## 第一步，查找Lambda的运行环境

经查文档，发现python 3.7的运行时是这个docker image `amazonlinux:1`。

所以就先拉一下镜像：`docker run -it amazonlinux:1 /bin/sh`,然后 `ldd --version`看到GLIBC的版本是2.17。

## 第二步，复现问题

搞一个带有python环境来重现问题：

```Dockerfile
FROM amazonlinux:1
RUN yum update -y
RUN yum install -y \
    gcc \
    openssl-devel \
    zlib-devel \
    libffi-devel \
    wget && \
    yum -y clean all

WORKDIR usr/src
# Install Python 3.7
RUN wget https://www.python.org/ftp/python/3.7.4/Python-3.7.4.tgz
RUN tar xzf Python-3.7.4.tgz
RUN cd Python-3.7.4 ; ./configure --enable-optimizations; make altinstall
RUN rm Python-3.7.4.tgz
RUN rm -rf Python-3.7.4
RUN python3.7 -V
# Install pip
RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python3.7 get-pip.py
RUN rm get-pip.py
RUN pip -V
```


接着：

```bash
pip install tflite
pip install https://dl.google.com/coral/python/tflite_runtime-2.1.0.post1-cp37-cp37m-linux_x86_64.whl
```

然后

```bash
python3.7
Python 3.7.4 (default, Mar 17 2020, 08:59:34)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from tflite_runtime.interpreter import Interpreter
Traceback (most recent call last):
  File "/usr/local/lib/python3.7/site-packages/tflite_runtime/interpreter_wrapper.py", line 14, in swig_import_helper
    return importlib.import_module(mname)
  File "/usr/local/lib/python3.7/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1006, in _gcd_import
  File "<frozen importlib._bootstrap>", line 983, in _find_and_load
  File "<frozen importlib._bootstrap>", line 967, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 670, in _load_unlocked
  File "<frozen importlib._bootstrap>", line 583, in module_from_spec
  File "<frozen importlib._bootstrap_external>", line 1043, in create_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
ImportError: /lib64/libm.so.6: version `GLIBC_2.27' not found (required by /usr/local/lib/python3.7/site-packages/tflite_runtime/_interpreter_wrapper.so)

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.7/site-packages/tflite_runtime/interpreter.py", line 46, in <module>
    from tflite_runtime import interpreter_wrapper as _interpreter_wrapper
  File "/usr/local/lib/python3.7/site-packages/tflite_runtime/interpreter_wrapper.py", line 17, in <module>
    _interpreter_wrapper = swig_import_helper()
  File "/usr/local/lib/python3.7/site-packages/tflite_runtime/interpreter_wrapper.py", line 16, in swig_import_helper
    return importlib.import_module('_interpreter_wrapper')
  File "/usr/local/lib/python3.7/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
ModuleNotFoundError: No module named '_interpreter_wrapper'
```

问题复现！

## 都走到这一步了，就得自己编译TF-lite啦

```bash
yum install git unzip gcc-c++ -y
git clone https://github.com/tensorflow/tensorflow.git
ln /usr/local/bin/python3.7 /usr/local/bin/python3
cd tensorflow
git checkout r2.1
./tensorflow/lite/tools/make/download_dependencies.sh
./tensorflow/lite/tools/pip_package/build_pip_package.sh
pip install --upgrade /tmp/tflite_pip/python3/dist/tflite_runtime-2.1.0-cp37-cp37m-linux_x86_64.whl
```

```bash
sh-4.2# python3
Python 3.7.4 (default, Mar 17 2020, 08:59:34)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from tflite_runtime.interpreter import Interpreter
>>>
```

YES!