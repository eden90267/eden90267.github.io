---
layout: post
title:  "JSF: Use RequestScope Need to Keep Page Value"
date:   2016-03-17 17:32:00 +0800
categories: [JSF, FlashScope]
---

今日遇到使用@RequestScope對Primefaces DateTable page無法正確rendered的問題(資料已分頁化), 恐怕要調整回@ViewScope....

以下是針對頁面keep需要欄位的example(使用FlashScope):

view:

    <c:if test="#{not empty noticeListPersonalController.history}">
    <c:set target="#{flash}" property="history"
           value="#{noticeListPersonalController.history}"/>
    </c:if>
    <c:if test="#{not empty flash.history}">
    <c:set target="#{noticeListPersonalController}" property="history"
           value="#{flash.history}"/>
    </c:if>
    <p:tabMenu activeIndex="#{(empty param.history or param.history eq 'N') ? 0 : 1}">
        <p:menuitem value="待審核單據" outcome="list_personal">
            <f:param name="history" value="N"/>
        </p:menuitem>
        <p:menuitem value="歷史紀錄查詢" outcome="list_personal">
            <f:param name="history" value="Y"/>
        </p:menuitem>
    </p:tabMenu>

controller:

~~~ java

...

@PostConstruct
private void init() {
    if (Faces.getFlash().containsKey("history")) {
        if (Faces.getRequestParameter("history") != null)
            history = Faces.getRequestParameter("history").equals("Y");
        else history = (boolean) Faces.getFlash().get("history");
    } else {
        if (Faces.getRequestParameter("history") != null)
            history = Faces.getRequestParameter("history").equals("Y");
        else history = false;
        Faces.getFlash().put("history", history);
    }
}

...

~~~

結語:  
Primefaces對@RequestScope還是很不友善...