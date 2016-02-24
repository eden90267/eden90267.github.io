---
layout: post
title:  "Spring cache @Cacheable method ignored when called from within the same class"
date:   2016-02-24 22:46:00 +0800
categories: [java, spring, cache, ehcache]
---
因工作需要使用Spring Cache整合EhCache時, 意外發現標注@Cacheable annotation的method在same class呼叫發揮不了效用

以下為範例Code:

~~~ java

@Cacheable(value = CacheConstants.FIND_ALL_TYPE)
@Override
public List<Type> getList() {
    return super.getList();
}
    
public List<Type> getListByOrgMappingAndStatusOrderBySortAsc(OrgMapping orgMapping, Activation status) {
    return getList().stream()
            .filter(type -> type.getOrgMapping().equals(orgMapping))
            .filter(type -> type.getStatus().equals(Activation.ACTIVE))
            .sorted((t1, t2) -> t1.getSort().compareTo(t2.getSort()))
            .collect(Collectors.toList());
}

~~~

折騰了很久, 也發現到網路很多類似的問題：  
[Spring cache @Cacheable method ignored when called from within the same class](http://stackoverflow.com/questions/12115996/spring-cache-cacheable-method-ignored-when-called-from-within-the-same-class)  
[Spring Cache @Cacheable - not working while calling from another method of the same bean](http://stackoverflow.com/questions/16899604/spring-cache-cacheable-not-working-while-calling-from-another-method-of-the-s)  
[@Cacheable method ignored when called from within the same class](http://forum.spring.io/forum/spring-projects/container/120387-cacheable-method-ignored-when-called-from-within-the-same-class)

其中讓我看到了一段文字恍然大悟！！！

Only external method calls coming in through the proxy are intercepted. This means that self-invocation, in effect, a method within the target object calling another method of the target object, will not lead to an actual cache interception at runtime even if the invoked method is marked with @Cacheable.

[Wiki: Using Cacheable](https://code.google.com/p/ehcache-spring-annotations/wiki/UsingCacheable)

---

原來same class不會觸發Spring AOP代理機制, 必須用AspectJ取代Spring AOP, 以下為解決方式：

~~~ java

public List<Type> getListByOrgMappingOrderBySortAsc(OrgMapping orgMapping) {
    return getProxyManager().getList().stream()
            .filter(type -> type.getOrgMapping().equals(orgMapping))
            .sorted(comparator)
            .collect(Collectors.toList());
}
    
private TypeManager getProxyManager() {
    return (TypeManager) AopContext.currentProxy();
}

~~~

強制透過代理物件做呼叫, 這樣就會啟用了getList的Cache了

---
以下為AopContext.currentProxy()可使用的步驟：

1.Add dependency to Maven pom.xml file:

```xml

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.7</version>
</dependency>

```

2.Add app-config.xml file(目前java config標注式寫法未能啟用expose-proxy)

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <aop:config expose-proxy="true"/>
</beans>

```

3.import進AppConfig:

~~~ java

@ImportResource("classpath*:/app-config.xml")
@EnableCaching
@Configuration
public class CacheConfig {

    @Bean
    @Autowired
    public CacheManager cacheManager(final net.sf.ehcache.CacheManager cacheManager) {
        cacheManager.addCache(new Cache(getDefaultConfig(CacheConstants.FIND_ALL_TYPE)));
        cacheManager.addCache(new Cache(getDefaultConfig(CacheConstants.FIND_ONE_TYPE)));
        return new EhCacheCacheManager(cacheManager);
    }

    private CacheConfiguration getDefaultConfig(String name) {
        return new CacheConfiguration(name, CacheConstants.maxEntriesLocalHeap)
                .memoryStoreEvictionPolicy(CacheConstants.memoryStoreEvictionPolicy)
                .eternal(CacheConstants.eternal)
                .timeToIdleSeconds(CacheConstants.timeToIdleSeconds)
                .timeToLiveSeconds(CacheConstants.timeToLiveSeconds)
                .maxBytesLocalDisk(CacheConstants.maxBytesLocalDisk, MemoryUnit.MEGABYTES);
    }

}

~~~

---
結語：  
呼, 整個幾乎花近半天的時間處理它...