## Spring Jersey

### web.xml config
```xml
 <servlet>  
        <servlet-name>service</servlet-name>  
        <servlet-class>com.sun.jersey.spi.spring.container.servlet.SpringServlet</servlet-class>  
        <init-param>  
          <param-name>com.sun.jersey.config.property.packages</param-name>  
          <param-value>com.stone.webservice</param-value>  
        </init-param>  
        <load-on-startup>1</load-on-startup>  
      </servlet>  
      <servlet-mapping>  
        <servlet-name>service</servlet-name>  
        <url-pattern>/*</url-pattern>  
      </servlet-mapping>  

```

### wadl URL
访问地址：http://ip:port/xxx-app/service/application.wadl


