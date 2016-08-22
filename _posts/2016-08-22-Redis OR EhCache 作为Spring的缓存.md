---
layout: post
title: Redis OR EhCache 作为Spring的缓存
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

#Redis作为缓存配置 ：

##依赖
    "org.springframework.data:spring-data-redis:1.7.2.RELEASE",
    "redis.clients:jedis:2.9.0",
    "org.apache.commons:commons-pool2:2.4.2",

##JavaConfig配置

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
                sb.append(obj.toString());
            }
            return sb.toString();
        };
    }
}

```
---