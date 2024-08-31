---
layout: post
title: 解决循环依赖
categories: Java
description: 解决循环依赖
---

今天使用JavaConfig配置Shiro的时候出现了
# Error creating bean with name 'getShiroFilter': Requested bean is currently in creation: Is there an unresolvable circular reference?

在xml配置的时候一切正常，苦思冥想后找到原因：

## ShiroRealm代码片段

---
``` java

public class ShiroRealm extends AuthorizingRealm {

    @Autowired
    private UserService userService;

    ...
}

```
---

## 改成这样：

---
``` java

public class ShiroRealm extends AuthorizingRealm {

    private UserService userService;

    public UserService getUserService() {
        return userService;
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    ...
}

```
---

## RootConfig 这样写：

---
``` java

public class ShiroRealm extends AuthorizingRealm {

    ...

    public ShiroRealm getShiroRealm() {
        //return new ShiroRealm();
        ShiroRealm shiroRealm=new ShiroRealm();
        shiroRealm.setUserService(new UserService());
        return shiroRealm;
    }

    ...
}

```
---
