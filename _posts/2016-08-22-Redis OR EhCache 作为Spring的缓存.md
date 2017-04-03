---
layout: post
title: Redis OR EhCache 作为Spring的缓存
categories: Java
description: Spring支持多种缓存，原来一直使用EhCache作为缓存的实现，今天试试Redis
---

# EhCache作为缓存配置 ：

## 依赖
    "org.ehcache:ehcache:3.1.0",

## JavaConfig配置
---
``` java

package com.github.izhangzhihao.SpringMVCSeedProject.Config;

import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.ehcache.EhCacheCacheManager;
import org.springframework.cache.ehcache.EhCacheManagerFactoryBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;

/**
 * 将SpringMVC缓存至EhCache中就取消注释该文件，注释掉
 * @see RedisCacheConfig
 */
@EnableCaching
@Configuration
public class EhCacheConfig {

    @Bean
    public EhCacheManagerFactoryBean getEhcache() {
        EhCacheManagerFactoryBean ehCacheManagerFactoryBean = new EhCacheManagerFactoryBean();
        ehCacheManagerFactoryBean.setShared(true);
        ehCacheManagerFactoryBean.setAcceptExisting(true);
        Resource resource = new ClassPathResource("ehcache.xml");
        ehCacheManagerFactoryBean.setConfigLocation(resource);
        return ehCacheManagerFactoryBean;
    }

    @Bean
    public EhCacheCacheManager getEhCacheCacheManager() {
        EhCacheCacheManager ehCacheCacheManager = new EhCacheCacheManager();
        ehCacheCacheManager.setCacheManager(getEhcache().getObject());
        return ehCacheCacheManager;
    }

}


```
---

## XML配置

---
``` xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:cache="http://www.springframework.org/schema/cache" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                            http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/cache
                            http://www.springframework.org/schema/cache/spring-cache.xsd">

    <!--配置ehcache-->
    <bean id="ehcache" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean"
          p:shared="true"
          p:acceptExisting="true"
          p:configLocation="classpath:ehcache.xml"/>

    <!--配置缓存管理器-->
    <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager"
          p:cacheManager-ref="ehcache"/>

    <!--启用缓存注解 注解方式配置@EnableCaching-->
    <cache:annotation-driven cache-manager="cacheManager"/>

</beans>


```
---

# Redis作为缓存配置 ：

## 依赖

    "org.springframework.data:spring-data-redis:1.7.2.RELEASE",
    "redis.clients:jedis:2.9.0",
    "org.apache.commons:commons-pool2:2.4.2",


## JavaConfig配置


### RedisConfig

---
``` java

@Configuration
@EnableRedisHttpSession
@PropertySource("classpath:redis.properties") //导入资源文件
public class RedisConfig {

    @Value("${redis.host}")
    String host;
    @Value("${redis.port}")
    int port;
    @Value("${redis.timeout}")
    int timeout;
    @Value("${redis.database}")
    int database;
    @Value("${redis.maxTotal}")
    int maxTotal;
    @Value("${redis.maxIdle}")
    int maxIdle;
    @Value("${redis.maxWaitMillis}")
    int maxWaitMillis;
    @Value("${redis.testOnBorrow}")
    boolean testOnBorrow;

    @Bean
    public JedisPoolConfig getJedisPoolConfig() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(maxTotal);
        jedisPoolConfig.setMaxIdle(maxIdle);
        jedisPoolConfig.setMaxWaitMillis(maxWaitMillis);
        jedisPoolConfig.setTestOnBorrow(testOnBorrow);
        return jedisPoolConfig;
    }

    @Bean
    public JedisConnectionFactory getConnectionFactory() {
        JedisConnectionFactory connection = new JedisConnectionFactory();
        connection.setHostName(host);
        connection.setPort(port);
        connection.setTimeout(timeout);
        connection.setDatabase(database);
        connection.setPoolConfig(getJedisPoolConfig());
        return connection;
    }

    @Bean
    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, String> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        return redisTemplate;
    }
}

```
---

### RedisCacheConfig

---
``` java

@Configuration
@EnableCaching
public class RedisCacheConfig extends CachingConfigurerSupport {

    /**
     * 缓存管理器
     */
    @Bean
    public CacheManager cacheManager(RedisTemplate redisTemplate) {
        RedisCacheManager cacheManager = new RedisCacheManager(redisTemplate);
        cacheManager.setDefaultExpiration(300);// 设置缓存过期时间单位秒，默认不过期
        return cacheManager;
    }

    /**
     * @return 自定义策略生成的key
     * 自定义的缓存key的生成策略</br>
     * 若想使用这个key</br>
     * 只需要讲注解上keyGenerator的值设置为customKeyGenerator即可</br>
     */
    @Bean
    public KeyGenerator customKeyGenerator() {
        return (o, method, objects) -> {
            StringBuilder sb = new StringBuilder();
            sb.append(o.getClass().getName());
            sb.append(method.getName());
            for (Object obj : objects) {
				sb.append("-");
                sb.append(obj.toString());
            }
            return sb.toString();
        };
    }
}

```
---

## XML配置

### Spring-Redis.xml

---
``` xml

<?xml version="1.0" encoding="UTF-8"?>
<!--suppress SpringPlaceholdersInspection -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:redis="http://www.springframework.org/schema/redis"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/redis
		http://www.springframework.org/schema/redis/spring-redis.xsd ">

    <description>Redis配置</description>
    <!-- 导入资源文件 -->
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath:redis.properties</value>
            </list>
        </property>
        <property name="ignoreUnresolvablePlaceholders" value="true" />
    </bean>

    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig"
          p:maxIdle="${redis.maxIdle}"
          p:maxTotal="${redis.maxTotal}"
          p:maxWaitMillis="${redis.maxWaitMillis}"
          p:testOnBorrow="${redis.testOnBorrow}"/>

    <bean id="connectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"
          p:hostName="${redis.host}"
          p:port="${redis.port}"
          p:password="${redis.passWord}"
          p:timeout="${redis.timeout}"
          p:database="${redis.database}"
          p:poolConfig-ref="poolConfig"/>

    <bean id="redisTemplate" class="org.springframework.data.redis.core.StringRedisTemplate">
        <property name="connectionFactory" ref="connectionFactory"/>
        <property name="valueSerializer" ref="redisSerializer"/>
        <property name="keySerializer" ref="stringSerializer" />
        <!--<property name="enableTransactionSupport" value="true" />-->
    </bean>

    <bean id="stringSerializer" class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
    <bean id="redisSerializer" class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>

</beans>

```
---

### Spring-RedisCache.xml

---
``` xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:cache="http://www.springframework.org/schema/cache"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                            http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/cache
                            http://www.springframework.org/schema/cache/spring-cache.xsd">

    <!--配置缓存管理器-->
    <bean id="cacheManager" class="org.springframework.data.redis.cache.RedisCacheManager">
        <constructor-arg name="redisOperations" ref="redisTemplate"/>
        <property name="defaultExpiration" value="300"/><!--设置缓存过期时间单位秒，默认不过期-->
    </bean>

    <!--启用缓存注解 注解方式配置@EnableCaching-->
    <cache:annotation-driven cache-manager="cacheManager"/>

</beans>

```
---

# 缓存指定方法的执行结果

    设置好缓存配置之后我们就可以使用 @Cacheable 注解来缓存方法执行的结果了

---
``` java

@RestController
@RequestMapping("/Log")
public class LogController {
    /**
     * 日志分页查询
     *
     * @param pageNumber 页码
     * @param pageSize   每页大小
     * @return json数据
     */
    @GetMapping("/getLogByPage/pageNumber/{pageNumber}/pageSize/{pageSize}")
    @Cacheable("getLogByPage")
    public PageResults<Log> getLogByPage(@PathVariable int pageNumber,
                                         @PathVariable int pageSize) throws Exception {
        return logService.getListByPage(pageNumber, pageSize);
    }
}

```
---

# 缓存一致性保证

    CRUD (Create 创建，Read 读取，Update 更新，Delete 删除) 操作中，除了 R 具备幂等性，其他三个都可能会造成缓存结果和数据库不一致。
    为了保证缓存数据的一致性，在进行 CUD 操作的时候我们需要对可能影响到的缓存进行更新或者清除。
    使用 @CacheEvict 清除缓存。如果 CUD 相关方法能返回实体，也可以使用 @CachePut 更新缓存策略。
    @CacheEvict会将所有相关方法的缓存都清理掉，所以能用@CachePut的话最好。

# 自定义缓存 key 生成策略

    对于使用 @Cacheable 注解的方法，每个缓存的 key 生成策略默认使用的是参数名+参数值，
    这个方法的缓存将保存于 key 为例如 getLogByPage~keys 的缓存下，但是@Cacheable(value="XXX"),如果"XXX"重复的话，
    两个方法之间的缓存就会互相覆盖，出现问题。

    解决办法是使用自定义缓存策略:见上面的customKeyGenerator()方法。
    使用方式@Cacheable(value = "getLogByPage", keyGenerator = "customKeyGenerator")。
    这样生成的key就变成了com.github.izhangzhihao.SpringMVCSeedProject.Controller.LogControllergetLogByPage110这样的

# 注意：

    要缓存的 Java 对象必须实现 Serializable 接口
