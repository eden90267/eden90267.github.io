---
layout: post
title:  "Used Google Guava Cache to Spring"
date:   2016-02-24 11:21:00 +0800
categories: [java, spring, guava, cache]
---
因工作需求有用到快取機制, 但可能因Multiple Cache Container相互衝突關係(與EhCache), 造成無法使用Google Guava Cache整合Spring, 所以記錄在此方便以後使用:

以下為Java Config方式:

1.Add dependency to Maven pom.xml file:

```xml

<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>18.0</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>4.1.7.RELEASE</version>
</dependency>

```

2.Create CacheConfig file:

~~~ java

@EnableCaching
@Configuration
public class CacheConfig implements CachingConfigurer {

    @Bean
    public CacheManager cacheManager() {
        SimpleCacheManager cacheManager = new SimpleCacheManager();
        GuavaCache findAllTypeCache = new GuavaCache(CacheConstant.FIND_ALL_TYPE, getDefaultCacheBuilder().build());
        GuavaCache findOneTypeCache = new GuavaCache(CacheConstant.FIND_ONE_TYPE, getDefaultCacheBuilder().build());
        cacheManager.setCaches(Arrays.asList(findAllTypeCache, findOneTypeCache));
        return cacheManager;
    }

    @Override
    public KeyGenerator keyGenerator() {
        return new SimpleKeyGenerator();
    }

    public CacheBuilder getDefaultCacheBuilder() {
        return CacheBuilder.newBuilder()
                .maximumSize(10000L)
                .expireAfterWrite(30L, TimeUnit.MINUTES);
    }

}

~~~

3.Add @Cacheable, @CacheEvict annotation to method to be cached:

~~~ java

@Service
public class TypeManager extends AbstractManager<Type, Long> {

    private final Comparator<Type> comparator = (t1, t2) -> t1.getSort().compareTo(t2.getSort());

    @Autowired
    private TypeRepository repository;

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, key = "#type.sid"),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public Type save(Type type) {
        return super.save(type);
    }

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, key = "#type.sid"),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public Type saveAndFlush(Type type) {
        return super.saveAndFlush(type);
    }

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, allEntries = true),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public List<Type> save(List<Type> tList) {
        return super.save(tList);
    }

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, key = "#type.sid"),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public Type save(Type type, User loginUser) {
        return super.save(type, loginUser);
    }

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, key = "#type.sid"),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public Type saveAndFlush(Type type, User loginUser) {
        return super.saveAndFlush(type, loginUser);
    }

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, allEntries = true),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public List<Type> save(List<Type> types, User loginUser) {
        return super.save(types, loginUser);
    }

    @Caching(evict = {
            @CacheEvict(value = CacheConstant.FIND_ONE_TYPE, key = "#sid"),
            @CacheEvict(value = CacheConstant.FIND_ALL_TYPE, allEntries = true)
    })
    @Override
    public void cacheEvict(Long sid) {
        super.cacheEvict(sid);
    }

    @Cacheable(CacheConstant.FIND_ONE_TYPE)
    @Override
    public Type getBySid(Long sid) {
        return super.getBySid(sid);
    }

    @Cacheable(CacheConstant.FIND_ALL_TYPE)
    @Override
    public List<Type> getList() {
        System.err.println("get read data!");
        return super.getList();
    }
    ...
}

~~~

4.enjoy~~~

----
Reference:

* [spring-4-guava-cache-integration-example-with-guavacachemanager-and-guavacache](http://www.concretepage.com/spring-4/spring-4-guava-cache-integration-example-with-guavacachemanager-and-guavacache)
* [add-guava-cache-to-spring-boot-to-cache](http://codedevstuff.blogspot.tw/2015/07/add-guava-cache-to-spring-boot-to-cache.html)
* [spring-31-caching-and-0](https://dzone.com/articles/spring-31-caching-and-0)
* [CacheEvict example](http://www.programcreek.com/java-api-examples/index.php?api=org.springframework.cache.annotation.CacheEvict)