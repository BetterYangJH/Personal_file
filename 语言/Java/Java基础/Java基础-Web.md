*   JavaSE
    *   适合小网站
*   JavaEE(J2EE)
    *   对JavaSE的拓展
    *   相当于JavaSE+jar包(javax开头)
    *   适合企业

*   Servlet
    *   处理Http请求
*   Servlet Container
    *   相当于Python的WSGI
    *   常用：Apache，Tomcat，JBoss，Jetty等
*   EJB
    *   同Servlet
    *   企业级
*   Spring
    *   SpringMVC+SpringBoot+SpringCloud
*   大数据：Hadoop，mapreduce
*   web：Sping，Spark

# 常用

JDK+Tomcat+MySEclipse

## Tomcat服务器

*   /bin：启动停止等命令

*   /conf：配置文件

    *   conf/server.xml

*   /lib：所需的jar文件

*   /logs：日志

*   /temp：临时文件

*   /webapps：web应用文件发布于此

*   Web_INF：web应用的安全目录，客户端不能访问

    *   web.xml：项目部署文件

        ```xml
        <welcome_file_list> // 默认打开页面
        	<welcome_file>...jsp</welcome_file> // 可以有多个，打开最前的
        </welcome_file_list>
        ```

    *   classes：.class文件

    *   lib：jar包

*   顺序

    *   Java基础 - web - Demo(Java,JDBC,JSP) - SSM(Spring，springMVC，Mybatis)迭代项目，整体配合git使用，心得总结

    *   基础：重点 - 异常处理，数组，常用类，容器

    *   运行：

        *   硬盘（代码） - load - 内存（代码main开始，编译执行）
    *   内存管理
            *   stack：局部变量
            *   heap：new的对象
            *   datasegment：静态变量，字符串常量
            *   codesegment：代码
    
*   java.net包

*   |      | 字节         | 字符串 |
    | ---- | ------------ | ------ |
    | 输入 | InputStream  | Reader |
    | 输出 | OutputStream | Writer |

*   Servlet组件，JSD动态文档，JDBC

*   框架SSM(Spring，springMVC，Mybatis)

    *   Mybatis持久层框架
    *   Sping
    *   SpringMVC避免多个Servlet
    *   request-DispatcherServlet-Handler-View，Model

```java
packet Myweb;
import java.io.IOException;
import java.util.Date;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyServlet extends HttpServlet{
    private static final long serialversionID = 484851891859498;
    public void doGet(HttpServletRequest request,HttpServletResponse response)
        throws ServletException,IOException{
        	response.getWriter.println('')
    }
}

web.xml
<?xml version='1.0' encoding='UTF-8'>
<web-app>
    <servlet>
    	<servlet-name>xxx</servlet-name>
    	<servlet-class>Myweb.MyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
    	<servlet-name>xxx</servlet-name>
    	<url-pattern>welcome</url-pattern>
    </servlet-mapping>  
</web-app>
            
https://localhost:8080/MyServlet/welcome
```

变量修饰词考虑：权限 - 静态 - 是不是最后 - 类型

方法修饰词考虑：权限 - 返回值

