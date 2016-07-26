# 今天遇到了 /home/travis/build.sh: line 45: ./gradlew: Permission denied 的问题
# 解决方案如下：
在.travis.yml中增加 

---
``` 

before_install:
- chmod +x gradlew

```
---

