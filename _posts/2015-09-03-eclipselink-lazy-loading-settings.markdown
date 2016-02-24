---
layout: post
title:  "EclipseLink lazy loading settings"
date:   2015-09-03 11:00:00 +0800
categories: [java, eclipseLink, spring, lazy loading]
-----------------------------------------------------
EclipseLink預設啟用lazy, 但須透由LoadTimeWeaver lib來代理集成才有其效用, 本篇是透由Container的loadTimeWeaver進行配置, 以下為啟用範例(spring-data-jpa + eclipseLink):

1.spring configuration, 必須透由標注啟用LoadTimeWeaving, 並實作LoadTimeWeavingConfigurer interfaces來配置container的loadTimeWeaver:

~~~ java

@Configuration
...
@EnableLoadTimeWeaving
public class AppConfig implements LoadTimeWeavingConfigurer {
    ...
    /** 此Container環境是glassfish, 若為tomcat可用TomcatLoadTimeWeaver */
    @Override
    public LoadTimeWeaver getLoadTimeWeaver() {
        return new GlassFishLoadTimeWeaver();
    }

~~~

2.spring configuration增加loadTimeWeaver Bean, 並將LocalContainerEntityManagerFactoryBean設定loadTimeWeaver屬性: 

~~~ java

...
public class AppConfig implements LoadTimeWeavingConfigurer {
    ...
    @Bean
    public GlassFishLoadTimeWeaver loadTimeWeaver() {
        return new GlassFishLoadTimeWeaver();
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() throws NamingException {
        LocalContainerEntityManagerFactoryBean entityManagerFactoryBean = new LocalContainerEntityManagerFactoryBean();
        entityManagerFactoryBean.setJtaDataSource(dataSource());
        entityManagerFactoryBean.setJpaVendorAdapter(jpaVendorAdapter());
        entityManagerFactoryBean.setLoadTimeWeaver(loadTimeWeaver());
        ...
    } 

~~~

3.大功告成, enjoy~