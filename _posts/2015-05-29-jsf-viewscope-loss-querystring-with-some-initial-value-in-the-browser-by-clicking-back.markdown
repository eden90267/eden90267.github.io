---
layout: post
title:  "JSF ViewScope: Loss QueryString with Some Initial Value in The Browser by Clicking Back"
date:   2015-05-29 21:00:00 +0800
categories: [java, jsf, viewScope, queryString]
---
瀏覽器針對JSF ViewScope返回上一頁自身遇到的案例是QueryString會無法取得, 造成參數後的初始欄位出現問題, 解決方法為掛Clear cache Filter, 如下:

~~~ java

@@WebFilter("*.xhtml")
 public class ClearCacheFilter implements Filter {
 
     @Override
     public void init(FilterConfig filterConfig) throws ServletException {
     }
 
     @Override
     public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
         HttpServletRequest req = (HttpServletRequest) request;
         HttpServletResponse res = (HttpServletResponse) response;
 
         if (!req.getRequestURI().startsWith(req.getContextPath() + ResourceHandler.RESOURCE_IDENTIFIER)) { // Skip JSF resources (CSS/JS/Images/etc)
             res.setHeader("Cache-Control", "no-cache, no-store, must-revalidate"); // HTTP 1.1.
             res.setHeader("Pragma", "no-cache"); // HTTP 1.0.
             res.setDateHeader("Expires", 0); // Proxies.
         }
         chain.doFilter(request, response);
     }
 
     @Override
     public void destroy() {
     }
 
 }

~~~
