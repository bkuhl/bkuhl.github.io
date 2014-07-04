---
title: TinyMCE Spellcheck Implmentation with Spring MVC
layout: post
date: 2014-02-17 16:55:16
categories: java
---


I recently added a Java implementation of [TinyMCE Spellchecker plugin](http://www.tinymce.com/wiki.php/Plugin:spellchecker) to a web application which utilitizes Spring MVC.  I used [Andrey’s guide](http://achorniy.wordpress.com/2009/08/11/tinymce-spellchecker-in-java/) to make this happen.  His instructions worked great until Spring was thrown into the mix.  Spring requires the use of [ServletWrappingController](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/web/servlet/mvc/ServletWrappingController.html) which will take a standard java controller and provide it with Spring’s functionality.  There were 2 key steps I needed to do in addition to what his guide suggests.

1) Ensure the URL being used by the plugin is passed to the dispatcher in **web.xml**. In this example we’re using __*.json__.

```
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>*.html</url-pattern>
    <url-pattern>*.json</url-pattern>
</servlet-mapping>
```

2) Since *.json is being forwarded to the dispatcher, now the dispatcher needs to tell Spring what URL to map the spellchecker controller to, and wrap it with the ServletWrappingController.

```
<bean id="urlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="mappings">
        <props>
            <prop key="/spellchecker/google-spellchecker.json">googleSpellChekerWrappingController</prop>
        </props>
    </property>
</bean>

<bean id="googleSpellChekerWrappingController" class="org.springframework.web.servlet.mvc.ServletWrappingController">
    <property name="servletClass">
        <value>org.tinymce.spellchecker.GoogleSpellChekerServlet</value>
    </property>
</bean>
```