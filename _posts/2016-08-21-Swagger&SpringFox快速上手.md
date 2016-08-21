---
layout: post
title: Swagger&SpringFox快速上手
description: Swagger是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。
---

# 简介：
    Swagger是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。
    总体目标是使客户端和文件系统作为服务器以同样的速度来更新。
    文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。

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

