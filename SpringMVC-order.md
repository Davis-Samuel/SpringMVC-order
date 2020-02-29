# 1.父项目SpringMVC

- 依赖：

  ```xml
  <dependencies>
      
  <dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.2.3.RELEASE</version>
  </dependency>
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.12</version>
      </dependency>
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>servlet-api</artifactId>
              <version>2.5</version>
          </dependency>
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>servlet-api</artifactId>
              <version>2.5</version>
          </dependency>
      
      <!--jackson工具依赖-->
      <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.10.1</version>
      </dependency>
      
  </dependencies>
  ```


# 2.HelloMVC

## *<u>模块springmvc-02-hellomvc</u>*

- 步骤：【https://blog.kuangstudy.com/index.php/archives/318/】
- 注意错误：如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！
- ![image-20200227233103384](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200227233103384.png)


# 3.使用注解开发

## *<u>模块springmvc-annotation</u>*

- 建立普通maven项目，再右键添加模块，右键添加web框架，再在pom.xml中添加资源预留代码，再设置里添加lib目录添加依赖，操作如上图。再模块的pom.xml中添加资源预留代码

- 配置web.xml：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <!--1.注册servlet-->
      <servlet>
          <servlet-name>SpringMVC</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc.xml</param-value>
          </init-param>
          <!-- 启动顺序，数字越小，启动越早 -->
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <!--所有请求都会被springmvc拦截 -->
      <servlet-mapping>
          <servlet-name>SpringMVC</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
      
      <!--    spring的编码过滤器-->
      <filter>
          <filter-name>encoding</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
              <param-name>encoding</param-name>
              <param-value>utf-8</param-value>
          </init-param>
      </filter>
      <filter-mapping>
          <filter-name>encoding</filter-name>
          <url-pattern>/</url-pattern>
      </filter-mapping>
  
  </web-app>
  ```

- 在resource中创建springmvc.xml文件对应web.xml中的classpath关联路径：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
  
      <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
      <context:component-scan base-package="com.it.controller"/>
      
      <!--    json乱码解决-->
      <mvc:annotation-driven>
          <mvc:message-converters register-defaults="true">
              <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                  <constructor-arg value="UTF-8"/>
              </bean>
              <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                  <property name="objectMapper">
                      <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                          <property name="failOnEmptyBeans" value="false"/>
                      </bean>
                  </property>
              </bean>
          </mvc:message-converters>
      </mvc:annotation-driven>
      
      <!-- 让Spring MVC不处理静态资源 -->
      <mvc:default-servlet-handler />
      <!--
      支持mvc注解驱动
          在spring中一般采用@RequestMapping注解来完成映射关系
          要想使@RequestMapping注解生效
          必须向上下文中注册DefaultAnnotationHandlerMapping
          和一个AnnotationMethodHandlerAdapter实例
          这两个实例分别在类级别和方法级别处理。
          而annotation-driven配置帮助我们自动完成上述两个实例的注入。
       -->
      <mvc:annotation-driven />
  
      <!-- 视图解析器 -->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
            id="internalResourceViewResolver">
          <!-- 前缀 -->
          <property name="prefix" value="/WEB-INF/jsp/" />
          <!-- 后缀 -->
          <property name="suffix" value=".jsp" />
      </bean>
  
  </beans>
  ```

- 根据springmvc.xml中的前后缀和自动扫描包，依次创建创建。

- 在WEB-INF下创建jsp文件夹，创建hello.jsp：

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>Title</title>
  </head>
  <body>
  
  ${msg}
  
  </body>
  </html>
  ```

  

- 创建com.it.controller，创建HelloController.java：

  ```java
  package com.it.controller;
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.RequestMapping;
  
  @Controller
  public class HelloController {
  
      @RequestMapping("/Hello")  //url请求路径，(tomcat地址)后填入
      public String hello(Model model){
  
          model.addAttribute("mag","hhhhhhkkksdsdadadasdasdas");
  
          return "hello";  //被视图解析器处理，对应要跳转的jsp的名字
      }
     //可以写多个@RequestMapping("")以及方法，代表多个servlet。
  }
  ```

## RestFul风格

- 地址栏中编写：

  ```
  http://localhost:8080/test/1/"2"
  ```

- HelloController.java：

  ```java
  package com.it.controller;
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RequestMapping;
  
  @Controller
  public class HelloController2 {
  //使用 @PathVariable 注解，让方法参数的值对应绑定到一个URI模板变量上。
      @RequestMapping("/test/{a}/{b}")
      public String test(@PathVariable int a, @PathVariable String b, Model model){
          String re = a + b;
          model.addAttribute("msg",re);
          return "hello";
      }
  }
  ```

  ```java
  //通过下方不同的请求方式，达到不同的结果和效果
  //@GetMapping("/test/{a}/{b}")
  //@PostMapping("/test/{a}/{b}")
  //@PutMapping("/test/{a}/{b}")
  //@DeleteMapping("/test/{a}/{b}")
  //@PatchMapping("/test/{a}/{b}")
  ```

# 4.转发重定向

- ![image-20200228215230646](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200228215230646.png)

- 右侧的路径代表web文件夹下的.jsp。web文件夹相当于没有，web文件下的东西与Java文件等都是同级的。唯独WEB-INF不能直接访问，需要写全路径。

## 关于转发?(有视图解析器)

- 默认是转发。要根据**视图解析器**的开始和结尾路径进行**拼接**转发，**加/和不加/，都看与视图解析器的前后路径的拼接**，如下图，如果转发超出了WEB-INF那就转发不到，需要重定向。

- ```java
  return "forward:a" //默认在/WEB-INF/jsp/ 下;
  return "forward:/a" //默认在/WEB-INF/jsp 下;
  return "forward:/a.jsp" //不在/WEB-INF/jsp 下
  ```

  ![image-20200228221427192](C:\Users\91566\AppData\Roaming\Typora\typora-user-images\image-20200228221427192.png)

## 关于重定向(有视图解析器)

- ```java
  return "redirect:/a.jsp"; //写重定向的全路径(/.jsp)，在WEB-INF下就写上/WEB-INF......
  ```

## 无视图解析器

- **写重定向的全路径(/.jsp)**

# 5.向前端传递参数

- UserController.java：

  ```java
  package com.it.controller;
  import com.it.pojo.User;
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RequestParam;
  
  @Controller
  @RequestMapping("/user")
  public class UserController {
  
      //@RequestParam("name"),必须用name去接受参数， 必写 ！！！
      @GetMapping("/t4")
      public String test4(@RequestParam("name") String name, Model model){
  //        接受前端参数
          System.out.println(name);
  //        将结果返回给前端
          model.addAttribute("msg",name);
  //        视图转发
          return "hello";
      }
  
  //    向前端传递对象(字段名一致)：localhost:8080/user/t5？name= & id= & age=
      @GetMapping("/t5")
      public String test5(User user,Model model){
          return "hello";
      }
  }
  ```


# 6.解决乱码

- tomcat配置文件添加编码serve.xml:

  ```xml
  <Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
             connectionTimeout="20000"
             redirectPort="8443" />
  ```

- 先试一下spring的filter过滤器，web.xml：

  ```xml
  <!--    spring的编码过滤器-->
      <filter>
          <filter-name>encoding</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
              <param-name>encoding</param-name>
              <param-value>utf-8</param-value>
          </init-param>
      </filter>
      <filter-mapping>
          <filter-name>encoding</filter-name>
          <url-pattern>/</url-pattern>
      </filter-mapping>
  ```

- spring的过滤器都不行，tomcat的编码修改也不行的极端情况使用自定义过滤：

  com.it.filter.EcodingFilter.java：

  ```java
  package com.it.filter;
  
  import javax.servlet.*;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletRequestWrapper;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  import java.io.UnsupportedEncodingException;
  import java.util.Map;
  
  public class EncodingFilter implements Filter {
      public void destroy() {
      }
  
      public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
          //处理response的字符编码
          HttpServletResponse myResponse=(HttpServletResponse) response;
          myResponse.setContentType("text/html;charset=UTF-8");
  
          // 转型为与协议相关对象
          HttpServletRequest httpServletRequest = (HttpServletRequest) request;
          // 对request包装增强
          HttpServletRequest myrequest = new MyRequest(httpServletRequest);
          chain.doFilter(myrequest, response);
      }
  
      public void init(FilterConfig filterConfig) throws ServletException {
      }
  
  }
  
  //自定义request对象，HttpServletRequest的包装类
  class MyRequest extends HttpServletRequestWrapper {
  
      private HttpServletRequest request;
      //是否编码的标记
      private boolean hasEncode;
      //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
      public MyRequest(HttpServletRequest request) {
          super(request);// super必须写
          this.request = request;
      }
  
      // 对需要增强方法 进行覆盖
      @Override
      public Map getParameterMap() {
          // 先获得请求方式
          String method = request.getMethod();
          if (method.equalsIgnoreCase("post")) {
              // post请求
              try {
                  // 处理post乱码
                  request.setCharacterEncoding("utf-8");
                  return request.getParameterMap();
              } catch (UnsupportedEncodingException e) {
                  e.printStackTrace();
              }
          } else if (method.equalsIgnoreCase("get")) {
              // get请求
              Map<String, String[]> parameterMap = request.getParameterMap();
              if (!hasEncode) { // 确保get手动编码逻辑只运行一次
                  for (String parameterName : parameterMap.keySet()) {
                      String[] values = parameterMap.get(parameterName);
                      if (values != null) {
                          for (int i = 0; i < values.length; i++) {
                              try {
                                  // 处理get乱码
                                  values[i] = new String(values[i]
                                          .getBytes("ISO-8859-1"), "utf-8");
                              } catch (UnsupportedEncodingException e) {
                                  e.printStackTrace();
                              }
                          }
                      }
                  }
                  hasEncode = true;
              }
              return parameterMap;
          }
          return super.getParameterMap();
      }
  
      //取一个值
      @Override
      public String getParameter(String name) {
          Map<String, String[]> parameterMap = getParameterMap();
          String[] values = parameterMap.get(name);
          if (values == null) {
              return null;
          }
          return values[0]; // 取回参数的第一个值
      }
  
      //取所有值
      @Override
      public String[] getParameterValues(String name) {
          Map<String, String[]> parameterMap = getParameterMap();
          String[] values = parameterMap.get(name);
          return values;
      }
  }
  ```

  web.xml：

  ```xml
  <filter>
      <filter-name>filter</filter-name>
      <filter-class>com.it.filter.EncodingFilter</filter-class>
  </filter>
      <filter-mapping>
          <filter-name>filter</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
  ```

# 7.JSON

## *<u>模块springmvc-json</u>*

- 配置lib目录，tomcat

- 引入依赖：

  ```xml
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.10.1</version>
  </dependency>
  ```

- pojo.User.java：

  ```java
  package com.it.pojo;
  
  import lombok.AllArgsConstructor;
  import lombok.Data;
  import lombok.NoArgsConstructor;
  
  @Data
  @AllArgsConstructor
  @NoArgsConstructor
  public class User {
  
      private int id;
      private String name;
      private int age;
  }
  ```

- springmvc.xml：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
  
      <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
      <context:component-scan base-package="com.it.controller"/>
  
      <!-- 视图解析器 -->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
            id="internalResourceViewResolver">
          <!-- 前缀 -->
          <property name="prefix" value="/WEB-INF/jsp/" />
          <!-- 后缀 -->
          <property name="suffix" value=".jsp" />
      </bean>
  
  </beans>
  ```

- web.xml：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <servlet>
          <servlet-name>SpringMVC</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc.xml</param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <servlet-mapping>
          <servlet-name>SpringMVC</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  
  
      <filter>
          <filter-name>encoding</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
              <param-name>encoding</param-name>
              <param-value>utf-8</param-value>
          </init-param>
      </filter>
      <filter-mapping>
          <filter-name>encoding</filter-name>
          <url-pattern>/</url-pattern>
      </filter-mapping>
  
  </web-app>
  ```

- JSON工具类含有时间，JSONUtils.java：

  ```java
  package com.it.jsonutils;
  
  import com.fasterxml.jackson.core.JsonProcessingException;
  import com.fasterxml.jackson.databind.ObjectMapper;
  import com.fasterxml.jackson.databind.SerializationFeature;
  
  import java.text.SimpleDateFormat;
  
  public class JSONUtils {
  
      public static String getJSON(Object obj) throws JsonProcessingException {
          return getJSON(obj,"yyyy-MM-dd HH:mm:ss");
      }
  
      public static String getJSON(Object obj,String dateFormat){
          ObjectMapper mapper = new ObjectMapper();
          //不使用时间差的方式
          mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
          //自定义日期格式对象
          SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
          //指定日期格式
          mapper.setDateFormat(sdf);
          try {
              return mapper.writeValueAsString(obj);
          } catch (JsonProcessingException e) {
              e.printStackTrace();
          }
          return null;
      }
  }
  ```

- UserConterller.java：

  ```java
  package com.it.controller;
  import com.fasterxml.jackson.core.JsonProcessingException;
  import com.fasterxml.jackson.databind.ObjectMapper;
  import com.it.jsonutils.JSONUtils;
  import com.it.pojo.User;
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.ResponseBody;
  import org.springframework.web.bind.annotation.RestController;
  
  import java.util.ArrayList;
  import java.util.Date;
  import java.util.List;
  
  @Controller //也可以使用	@RestController里面所有的方法都只会返回 json 字符串了
  public class UserController {
  
      @RequestMapping("/j")
      @ResponseBody //有了它就不走视图解析器了
      public String test() throws JsonProcessingException {
          User user = new User(123, "lisi",456);
          ObjectMapper mapper = new ObjectMapper();
          //将对象转化为JSON字符串
          String s = mapper.writeValueAsString(user);
          return s;
      }
  
      @RequestMapping("/jj")
      @ResponseBody
      //有了它就不走视图解析器了
      public String test2() throws JsonProcessingException {
          User user3 = new User(12, "lii",46);
          User user1 = new User(13, "isi",56);
          User user2 = new User(23, "si",45);
          List<User> user = new ArrayList<User>();
          user.add(user1);
          user.add(user2);
          user.add(user2);
          return JSONUtils.getJSON(user);
      }
  
      @RequestMapping("/jjj")
      @ResponseBody
      public String test3() throws JsonProcessingException {
          Date date = new Date();
          return JSONUtils.getJSON(date);
      }
  }
  ```

- json乱码解决，springmvc.xml：

  ```xml
  <!--    json乱码解决-->
      <mvc:annotation-driven>
          <mvc:message-converters register-defaults="true">
              <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                  <constructor-arg value="UTF-8"/>
              </bean>
              <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                  <property name="objectMapper">
                      <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                          <property name="failOnEmptyBeans" value="false"/>
                      </bean>
                  </property>
              </bean>
          </mvc:message-converters>
      </mvc:annotation-driven>
  ```

  