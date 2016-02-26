---
layout: post
title:  "JSF2 ViewScope with Spring"
date:   2015-06-03 21:00:00 +0800
categories: [java, jsf, viewScope, spring]
---
Sipring Framework目前不支持JSF ViewScope, 必須使用Spring Bean自定義之Scope來代理JSF 2的ViewScope, 使其成為一個Spring Bean的Scope。  
以下為實現方式與步驟:

1.定義ViewScope實現類:

~~~ java

package com.cy.commons.web.scopes;

import java.util.Map;
import javax.faces.context.FacesContext;
import org.omnifaces.util.Faces;
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.beans.factory.config.Scope;
import org.springframework.web.context.request.FacesRequestAttributes;

/**
 * <p>
 * 依賴此jar後, 需使用Spring custom ViewScope需設置如下兩項設定:
 * </p>
 *
 * <p>
 * 1.於web project之Spring javaConfig上import commons-web的javaConfig:
 * </p>
 *
 * <p>
 * @Configuration
 * <br />@Import({com.cy.commons.web.config.AppConfig.class})
 * <br />...
 * <br />public class AppConfig {
 * <br />...
 * <br />}
 * </p>
 *
 * <p>
 * 2.於web project之faces-config.xml上註冊自定義ViewMapListener:
 * </p>
 *
 * <p>
 * <faces-config <br />xmlns="http://java.sun.com/xml/ns/javaee"
 * <br />xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 * <br />xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
 * <br />http://java.sun.com/xml/ns/javaee/web-facesconfig_2_1.xsd"
 * <br />version="2.1">
 * <br />...
 * <br /><application>
 * <br /><el-resolver>org.springframework.web.jsf.el.SpringBeanFacesELResolver</el-resolver>
 * <br /><system-event-listener>
 * <br /><source-class>javax.faces.component.UIViewRoot</source-class>
 * <br /><system-event-listener-class>com.cy.yourproject.web.scopes.listeners.ViewScopeCallbackRegistrar</system-event-listener-class>
 * <br /><system-event-class>javax.faces.event.PostConstructViewMapEvent</system-event-class>
 * <br /></system-event-listener>
 * <br /><system-event-listener>
 * <br /><source-class>javax.faces.component.UIViewRoot</source-class>
 * <br /><system-event-listener-class>com.cy.yourproject.web.scopes.listeners.ViewScopeCallbackRegistrar</system-event-listener-class>
 * <br /><system-event-class>javax.faces.event.PreDestroyViewMapEvent</system-event-class>
 * <br /></system-event-listener>
 * <br /></application>
 * <br />...
 * <br /></faces-config>
 * </p>
 *
 * <p>
 * 如何使用:
 * </p>
 *
 * <p>
 * <br />@Scope("view")
 * <br />@Controller public class Adm02Bean implements Serializable {
 * <br /><br />
 * @Autowired private PaymentAccountSetManager manager;
 * <br />@Autowired private OrgManager orgManager;
 * <br />@Autowired private AccountManager accountManager;
 * <br /><br />
 * ...
 * <br /><br />
 * }
 * </p>
 *
 * @see
 * <a href="http://confluence.bb.com:8090/x/noB2AQ">http://confluence.bb.com:8090/x/noB2AQ</a>
 *
 * @author Eden Liu <eden90267@gmail.com>
 */
public class ViewScope implements Scope {

    public static final String VIEW_SCOPE_CALLBACKS = "viewScope.callbacks";

    @Override
    public Object get(String name, ObjectFactory<?> objectFactory) {
        Map<String, Object> viewMap = getViewMap();
        Object instance = viewMap.get(name);
        if (instance == null) {
            instance = objectFactory.getObject();
            synchronized (viewMap) {
                viewMap.put(name, instance);
            }
        }
        return instance;
    }

    @SuppressWarnings("unchecked")
    @Override
    public Object remove(String name) {
        Object instance = getViewMap().remove(name);
        if (instance != null) {
            Map<String, Runnable> callbacks = (Map<String, Runnable>) getViewMap().get(VIEW_SCOPE_CALLBACKS);
            if (callbacks != null) {
                callbacks.remove(name);
            }
        }
        return instance;
    }

    @Override
    public void registerDestructionCallback(String name, Runnable runnable) {
        Map<String, Runnable> callbacks = (Map<String, Runnable>) getViewMap().get(VIEW_SCOPE_CALLBACKS);
        if (callbacks != null) {
            callbacks.put(name, runnable);
        }
    }

    @Override
    public Object resolveContextualObject(String name) {
        FacesContext fc = Faces.getContext();
        FacesRequestAttributes facesRequestAttributes = new FacesRequestAttributes(fc);
        return facesRequestAttributes.resolveReference(name);
    }

    @Override
    public String getConversationId() {
        FacesContext fc = Faces.getContext();
        FacesRequestAttributes facesRequestAttributes = new FacesRequestAttributes(fc);
        return facesRequestAttributes.getSessionId() + "-" + Faces.getViewId();
    }

    private Map<String, Object> getViewMap() {
        return Faces.getViewMap();
    }

}

~~~

2.定義ViewMapListener實現類:

~~~ java

package com.cy.commons.web.listeners;

import com.cy.commons.web.scopes.ViewScope;
import java.util.HashMap;
import java.util.Map;
import javax.faces.component.UIViewRoot;
import javax.faces.event.AbortProcessingException;
import javax.faces.event.PostConstructViewMapEvent;
import javax.faces.event.PreDestroyViewMapEvent;
import javax.faces.event.SystemEvent;
import javax.faces.event.ViewMapListener;

/**
 *
 * @author Eden Liu <eden90267@gmail.com>
 */
public class ViewScopeCallbackRegistrar implements ViewMapListener {

    @Override
    public void processEvent(SystemEvent event) throws AbortProcessingException {
        if (event instanceof PostConstructViewMapEvent) {
            PostConstructViewMapEvent viewMapEvent = (PostConstructViewMapEvent) event;
            UIViewRoot viewRoot = (UIViewRoot) viewMapEvent.getComponent();
            viewRoot.getViewMap().put(ViewScope.VIEW_SCOPE_CALLBACKS, new HashMap<String, Runnable>());
        } else if (event instanceof PreDestroyViewMapEvent) {
            PreDestroyViewMapEvent viewMapEvent = (PreDestroyViewMapEvent) event;
            UIViewRoot viewRoot = (UIViewRoot) viewMapEvent.getComponent();
            Map<String, Runnable> callbacks = (Map<String, Runnable>) viewRoot.getViewMap().get(ViewScope.VIEW_SCOPE_CALLBACKS);
            if (callbacks != null) {
                for (Runnable c : callbacks.values()) {
                    c.run();
                }
                callbacks.clear();
            }
        }
    }

    @Override
    public boolean isListenerForSource(Object source) {
        return source instanceof UIViewRoot;
    }

}

~~~

3.註冊自定義之Scope:

~~~ java

@Configuration
...
public class AppConfig {
  
    ...

    @Bean
    public static CustomScopeConfigurer customScopeConfigurer() {
        CustomScopeConfigurer customScopeConfigurer = new CustomScopeConfigurer();
        Map<String, Object> map = Maps.newHashMap();
        map.put("view", new ViewScope());
        customScopeConfigurer.setScopes(map);
        return customScopeConfigurer;
    }

    ...
}

~~~

4.註冊自定義ViewMapListener(於faces-config.xml上):

```xml

<faces-config
    xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-facesconfig_2_1.xsd"
    version="2.1">

    ...

    <application>
        <el-resolver>org.springframework.web.jsf.el.SpringBeanFacesELResolver</el-resolver>
        <system-event-listener>
            <source-class>javax.faces.component.UIViewRoot</source-class>
            <system-event-listener-class>com.cy.yourproject.web.scopes.listeners.ViewScopeCallbackRegistrar</system-event-listener-class>
            <system-event-class>javax.faces.event.PostConstructViewMapEvent</system-event-class>
        </system-event-listener>
        <system-event-listener>
            <source-class>javax.faces.component.UIViewRoot</source-class>
            <system-event-listener-class>com.cy.yourproject.web.scopes.listeners.ViewScopeCallbackRegistrar</system-event-listener-class>
            <system-event-class>javax.faces.event.PreDestroyViewMapEvent</system-event-class>
        </system-event-listener>
    </application>

    ...

</faces-config>

```

5.如何使用?

~~~ java

@Scope("view")
@Controller
public class Adm02Bean implements Serializable {

    @Autowired
    private PaymentAccountSetManager manager;
    @Autowired
    private OrgManager orgManager;
    @Autowired
    private AccountManager accountManager;

    ...

}

~~~

結語:  
讓Spring提供JSF可以使用的ViewScope的backing beans, 可享受到Spring容器提供的好處, 同時也可以使用這個很方便的scope。對於JSF2中的FlashScope也可以以同樣方式來實現。
