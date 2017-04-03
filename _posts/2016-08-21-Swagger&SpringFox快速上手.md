---
layout: post
title: Swagger&SpringFox快速上手
categories: Java
description: Swagger是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。
---

# 简介：
    Swagger是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。
    总体目标是使客户端和文件系统作为服务器以同样的速度来更新。
    文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。
    Springfox 项目地址 https://github.com/springfox/springfox
    Springfox 官方文档 https://springfox.github.io/springfox/docs/current/

# Gradle依赖：
    //springfox
    "io.springfox:springfox-swagger2:2.5.0",
    "io.springfox:springfox-swagger-ui:2.5.0",

# JavaConfig方式配置：
---
``` Java

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableWebMvc //NOTE: Only needed in a non-springboot application
@EnableSwagger2
@ComponentScan("com.github.izhangzhihao.SpringMVCSeedProject.Controller")
public class SwaggerConfig {
    @Bean
    public Docket customImplementation(){
        return new Docket(DocumentationType.SWAGGER_2)
                .select()  // 选择那些路径和api会生成document
                .apis(RequestHandlerSelectors.any()) // 对所有api进行监控
                .paths(PathSelectors.any()) // 对所有路径进行监控
                .build();
    }
}

```
---

# 快速上手结束！

## 访问json API页面 http://localhost:8080/v2/api-docs

## 访问html API页面 http://localhost:8080/swagger-ui.html

# 效果：

![image](https://cloud.githubusercontent.com/assets/12044174/17836283/6c64eb84-67bf-11e6-8d22-83a7e6c60456.png)

# SpringMVC Controller 配置：

---
``` Java

@Controller
@RequestMapping("/User")
@Api(value = "/User", tags = "UserAPI", description = "用户信息接口")
public class UserController {
    @Autowired
    private UserService userService;

    @RequestMapping(value = "/UserList", method = RequestMethod.GET)
    @ResponseBody
    @ApiOperation(value = "获取所有用户", response = User.class, responseContainer = "List")
    public List<User> getAllUsers() throws Exception {
        return userService.getAll();
    }


    @RequestMapping(value = "/getUser/{userName}", method = RequestMethod.GET)
    @ResponseBody
    @ApiOperation(
            value = "根据id获取用户信息,不包含密码",
            response = User.class
    )
    @ApiResponses(value = {
            @ApiResponse(code = 404, message = "指定id的用户不存在")
    })
    public ResponseEntity<User> getUser(@PathVariable String userName)
            throws Exception {
        User user = userService.getById(userName);
        if (user == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(user, HttpStatus.OK);
    }

    @RequestMapping(value = "/createUser/userName/{userName}/passWord/{passWord}", method = RequestMethod.POST)
    @ResponseBody
    @ApiOperation(
            value = "创建用户",
            response = User.class
    )
    @ApiResponses(value = {
            @ApiResponse(code = 409, message = "指定id的用户已存在，冲突"),
            @ApiResponse(code = 201, message = "创建成功")
    })
    public ResponseEntity<Void> createUser(@PathVariable String userName,
                                           @PathVariable String passWord)
            throws Exception {
        User byId = userService.getById(userName);
        if (byId != null) {
            return new ResponseEntity<>(HttpStatus.CONFLICT);
        } else {
            userService.save(new User(userName, passWord));
            return new ResponseEntity<>(HttpStatus.CREATED);
        }
    }

    @RequestMapping(value = "/updateUser/userName/{userName}/passWord/{passWord}", method = RequestMethod.PUT)
    @ResponseBody
    @ApiOperation(
            value = "更新用户",
            response = User.class
    )
    @ApiResponses(value = {
            @ApiResponse(code = 404, message = "指定id的用户不存在"),
            @ApiResponse(code = 200, message = "更新成功")
    })
    public ResponseEntity<Void> updateUser(@PathVariable String userName,
                                           @PathVariable String passWord)
            throws Exception {
        User byId = userService.getById(userName);
        if (byId == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        } else {
            userService.saveOrUpdate(new User(userName, passWord));
            return new ResponseEntity<>(HttpStatus.OK);
        }
    }

    @RequestMapping(value = "/deleteUser/{id}", method = RequestMethod.DELETE)
    @ResponseBody
    @ApiOperation(
            value = "删除用户",
            response = User.class
    )
    @ApiResponses(value = {
            @ApiResponse(code = 404, message = "指定id的用户不存在"),
            @ApiResponse(code = 200, message = "删除成功")
    })
    public ResponseEntity<Void> deleteUser(@PathVariable String id)
            throws Exception {
        User byId = userService.getById(id);
        if (byId == null) {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        } else {
            userService.delete(byId);
            return new ResponseEntity<>(HttpStatus.OK);
        }
    }
}

```
---
