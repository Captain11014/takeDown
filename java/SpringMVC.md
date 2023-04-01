# SpringMVC

####  一、SpringMVC 简介

##### 1.1、MVC

MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分 

M：Model，模型层，指工程中的JavaBean，作用是处理数据

JavaBean分为两类：

- 一类称为实体类Bean：专门存储业务数据的，如 Student、User 等 
- 一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。

V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据 

C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器 

**MVC的工作流程：** 用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller 调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果 找到相应的View视图，渲染数据后最终响应给浏览器

##### 1.2、SpringMVC

SpringMVC是Spring的一个后续产品，是Spring的一个子项目 

SpringMVC 是 Spring 为表述层开发提供的一整套完备的解决方案。在表述层框架历经 Strust、 WebWork、Strust2 等诸多产品的历代更迭之后，目前业界普遍选择了 SpringMVC 作为 Java EE 项目 表述层开发的首选方案。

**注：三层架构分为表述层（或表示层）、业务逻辑层、数据访问层，表述层表示前台页面和后台 servlet**

##### 1.3、SpringMVC的特点

- Spring 家族原生产品，与 IOC 容器等基础设施无缝对接 
- 基于原生的Servlet，通过了功能强大的前端控制器DispatcherServlet，对请求和响应进行统一 处理 
- 表述层各细分领域需要解决的问题全方位覆盖，提供全面解决方案 代码清新简洁，大幅度提升开发效率 
- 内部组件化程度高，可插拔式组件即插即用，想要什么功能配置相应组件即可 
- 性能卓著，尤其适合现代大型、超大型互联网项目要求

#### 二、入门案例

##### 2.1、创建maven工程

###### （1）添加web模块

IDEA 方式

file --> project structure --> Modules -->点击相应工程模块下得Web -->点击+号添加web.xml文件 ---> D:\studyCode\java\SSM\ssm\src\main\webapp\WEB-INF\web.xml(注意：默认路径没有src\main\webapp,需要手动输入)

###### （2）打包方式：war

pom.xml文件

```xml
<packaging>war</packaging>
```

###### （3）引入依赖

```xml
<dependencies>
        <!-- SpringMVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.1</version>
        </dependency>
        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
        <!-- ServletAPI -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!-- Spring5和Thymeleaf整合包 -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.12.RELEASE</version>
        </dependency>
</dependencies>
```

##### 2.2、配置web.xml文件

注册SpringMVC的前端控制器DispatcherServlet

###### （1）默认配置方式

此配置作用下，SpringMVC的配置文件默认位于WEB-INF下，默认名称为- servlet.xml，例如，以下配置所对应SpringMVC的配置文件位于WEB-INF下，文件名为springMVCservlet.xml

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servletclass>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
    设置springMVC的核心控制器所能处理的请求的请求路径
    /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
    但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>

```

###### （2）扩展配置方式

可通过init-param标签设置SpringMVC配置文件的位置和名称，通过load-on-startup标签设置 SpringMVC前端控制器DispatcherServlet的初始化时间

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servletclass>
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <init-param>
        <!-- contextConfigLocation为固定值 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 使用classpath:表示从类路径查找配置文件，例如maven工程中的
        src/main/resources -->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--
    作为框架的核心组件，在启动过程中有大量的初始化操作要做
    而这些操作放在第一次请求时才执行会严重影响访问速度
    因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
    -->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
    设置springMVC的核心控制器所能处理的请求的请求路径
    /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
    但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>

<!--
<url-pattern>标签中使用/和/*的区别：
/所匹配的请求可以是/login或.html或.js或.css方式的请求路径，但是/不能匹配.jsp请求路径的请
求
因此就可以避免在访问jsp页面时，该请求被DispatcherServlet处理，从而找不到相应的页面
/*则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用/*的写
法
-->
```

##### 2.3、创建请求控制器

由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要 创建处理具体请求的类，即请求控制器 

请求控制器中每一个处理请求的方法成为控制器方法 

因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识 为一个控制层组件，交给Spring的IoC容器管理，此时SpringMVC才能够识别控制器的存在

```java
@Controller
public class HelloController {
}
```

##### 2.4、创建SpringMVC的配置文件

```xml
<!-- 自动扫描包 -->
<context:component-scan base-package="com.atguigu.mvc.controller"/>
<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver"
    class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
    <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
    <property name="templateResolver">
        <bean
        class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
        <!-- 视图前缀 -->
        <property name="prefix" value="/WEB-INF/templates/"/>
        <!-- 视图后缀 -->
        <property name="suffix" value=".html"/>
        <property name="templateMode" value="HTML5"/>
        <property name="characterEncoding" value="UTF-8" />
        </bean>
        </property>
        </bean>
	</property>
</bean>
<!--
处理静态资源，例如html、js、css、jpg
若只设置该标签，则只能访问静态资源，其他请求则无法访问
此时必须设置<mvc:annotation-driven/>解决问题
-->
<mvc:default-servlet-handler/>
<!-- 开启mvc注解驱动 -->
<mvc:annotation-driven>
    <mvc:message-converters>
    <!-- 处理响应中文内容乱码 -->
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="defaultCharset" value="UTF-8" />
            <property name="supportedMediaTypes">
            <list>
            <value>text/html</value>
            <value>application/json</value>
            </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

##### 2.5、测试

###### （1）实现对首页的访问

在请求控制器中创建处理请求的方法

```java
// @RequestMapping注解：处理请求和控制器方法之间的映射关系
// @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径
// localhost:8080/springMVC/
@RequestMapping("/")
public String index() {
    //设置视图名称(前端页面文件名)
    return "index";
}
```

###### （2）通过超链接跳转到指定页面 

在主页index.html中设置超链接

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>首页</title>
</head>
<body>
<h1>首页</h1>
<a th:href="@{/hello}">HelloWorld</a><br/>
</body>
</html>
```

在请求控制器中创建处理请求的方法

```java
@RequestMapping("/hello")
public String HelloWorld() {
	return "target";	
}
```

##### 2.6、总结

浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器 DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器， 将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的 控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会 被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视 图所对应页面

#### 三、@RequestMapping注解

##### 3.1、@RequestMapping注解的功能

@RequestMapping注解的作用就是将请求和处理请求的控制器方法关联 起来，建立映射关系。 SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息 

@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

```java
@Controller
@RequestMapping("/test")
public class RequestMappingController {
    //此时请求映射所映射的请求的请求路径为：/test/testRequestMapping
    @RequestMapping("/testRequestMapping")
    public String testRequestMapping(){
    return "success";
    }
}
```

##### 3.2、@RequestMapping注解的value属性

@RequestMapping注解的value属性通过请求的请求地址匹配请求映射 

@RequestMapping注解的value属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址 所对应的请求 @RequestMapping注解的value属性必须设置，至少通过请求地址匹配请求映射

```java
@RequestMapping(
value = {"/testRequestMapping", "/test"},
method = {RequestMethod.GET, RequestMethod.POST}
)
public String testRequestMapping(){
	return "success";
}
```

注： 1、对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解 

处理get请求的映射-->@GetMapping 

处理post请求的映射-->@PostMapping 

处理put请求的映射-->@PutMapping 

处理delete请求的映射-->@DeleteMapping 

2、常用的请求方式有get，post，put，delete 但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符 串（put或delete），则按照默认的请求方式get处理 若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter，在 RESTful部分会讲到

##### 3.3、SpringMVC支持ant风格的路径

```
？：表示任意的单个字符
*：表示任意的0个或多个字符
**：表示任意层数的任意目录
注意：在使用**时，只能使用/**/xxx的方式
```

##### 3.4、SpringMVC支持路径中的占位符（重点）

原始方式：/deleteUser?id=1 

rest方式：/user/delete/1

通过@PathVariable注解，将占位符所表示的数据赋值给控制器方法的形参

```java
//例如：请求路径：/testRest/1/admin
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") int id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
//最终输出的内容为-->id:1,username:admin
```

#### 四、SpringMVC获取请求参数

##### 4.1、通过ServletAPI获取

将HttpServletRequest作为控制器方法的形参，此时HttpServletRequest类型的参数表示封装了当前请 求的请求报文的对象

```java
@RequestMapping("/testParam")
public String testParam(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println("username:"+username+",password:"+password);
    return "success";
}
```

##### 4.2、通过控制器方法的形参获取请求参数

在控制器方法的形参位置，设置和请求参数同名的形参，当浏览器发送请求，匹配到请求映射时，在 DispatcherServlet中就会将请求参数赋值给相应的形参

```html
<a th:href="@{/testParam(username='admin',password=123456)}">测试获取请求参数--
>/testParam</a><br>
```

```java
@RequestMapping("/testParam")
public String testParam(String username, String password){
    System.out.println("username:"+username+",password:"+password);
    return "success";
}
```

注： 若请求所传输的请求参数中有多个同名的请求参数，此时可以在控制器方法的形参中设置字符串 数组或者字符串类型的形参接收此请求参数 若使用字符串数组类型的形参，此参数的数组中包含了每一个数据 若使用字符串类型的形参，此参数的值为每个数据中间使用逗号拼接的结果

##### 4.3、@RequestParam

@RequestParam是将请求参数和控制器方法的形参创建映射关系 

@RequestParam注解一共有三个属性： 

value：指定为形参赋值的请求参数的参数名 

required：设置是否必须传输此请求参数，默认值为true 

若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置 defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为 false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为 null defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值 为""时，则使用默认值为形参赋值

##### 4.4、@RequestHeader 

@RequestHeader是将请求头信息和控制器方法的形参创建映射关系 

@RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

##### 4.5、@CookieValue 

@CookieValue是将cookie数据和控制器方法的形参创建映射关系 

@CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam



```java
 @RequestMapping("/param")
    public String getParam(
            @RequestParam(value = "userName", required = true, defaultValue = "hello") String username,
            String password,
            @RequestHeader("referer") String referer,
            @CookieValue("JSESSIONID") String jsessionId
    )
    {
        System.out.println("jsessionId:"+jsessionId);
        System.out.println("referer:"+referer);
        System.out.println("username:"+username+",password:"+password);
        return "success";
    }
```

##### 4.6、通过POJO获取请求参数

可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实 体类中的属性名一致，那么请求参数就会为此属性赋值

```java
@RequestMapping("/testpojo")
public String testPOJO(User user){
    System.out.println(user);
    return "success";
}
```

##### 4.7、解决获取请求参数的乱码问题

解决获取请求参数的乱码问题，可以使用SpringMVC提供的编码过滤器CharacterEncodingFilter，但是 必须在web.xml中进行注册

注：SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效

```xml
<!--配置springMVC的编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filterclass>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

#### 五、域对象共享数据

##### 5.1、使用ServletAPI向request域对象共享数据

```java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
    request.setAttribute("testScope", "hello,servletAPI");
    return "success";
}
```

##### 5.2、使用ModelAndView向request域对象共享数据

```java
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
    /**
    * ModelAndView有Model和View的功能
    * Model主要用于向请求域共享数据
    * View主要用于设置视图，实现页面跳转
    */
    ModelAndView mav = new ModelAndView();
    //向请求域共享数据
    mav.addObject("testScope", "hello,ModelAndView");
    //设置视图，实现页面跳转
    mav.setViewName("success");
    return mav;
}
```

##### 5.3、使用Model向request域对象共享数据

```java
@RequestMapping("/testModel")
public String testModel(Model model){
    model.addAttribute("testScope", "hello,Model");
    return "success";
}
```

##### 5.4、使用map向request域对象共享数据

```java
@RequestMapping("/testMap")
public String testMap(Map<String, Object> map){
    map.put("testScope", "hello,Map");
    return "success";
}
```

##### 5.5、使用ModelMap向request域对象共享数据

```java
@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap){
    modelMap.addAttribute("testScope", "hello,ModelMap");
    return "success";
}
```

##### 5.6、Model、ModelMap、Map的关系

Model、ModelMap、Map类型的参数其实本质上都是 BindingAwareModelMap 类型的

##### 5.7、向session域共享数据

```java
@RequestMapping("/testSession")
public String testSession(HttpSession session){
    session.setAttribute("testSessionScope", "hello,session");
    return "success";
}
```

##### 5.8、向application域共享数据

```java
@RequestMapping("/testApplication")
public String testApplication(HttpSession session){
    ServletContext application = session.getServletContext();
    application.setAttribute("testApplicationScope", "hello,application");
    return "success";
}
```

#### 六、SpringMVC视图

##### 6.1、ThymeleafView

当控制器方法中所设置的视图名称没有任何前缀时，此时的视图名称会被SpringMVC配置文件中所配置 的视图解析器解析，视图名称拼接视图前缀和视图 后缀所得到的最终路径，会通过转发的方式实现跳转

```java
@RequestMapping("/testHello")
public String testHello(){
    return "hello";
}
```

##### 6.2、转发视图

SpringMVC中默认的转发视图是InternalResourceView 

SpringMVC中创建转发视图的情况： 

当控制器方法中所设置的视图名称以"forward:"为前缀时，创建InternalResourceView视图，此时的视 图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"forward:"去掉，剩余部 分作为最终路径通过转发的方式实现跳转

例如"forward:/"，"forward:/employee"

```java
@RequestMapping("/testForward")
public String testForward(){
	return "forward:/testHello";
}
```

##### 6.3、重定向视图

SpringMVC中默认的重定向视图是RedirectView

当控制器方法中所设置的视图名称以"redirect:"为前缀时，创建RedirectView视图，此时的视图名称不 会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"redirect:"去掉，剩余部分作为最 终路径通过重定向的方式实现跳转

例如"redirect:/"，"redirect:/employee"

注：重定向视图在解析时，会先将redirect:前缀去掉，然后会判断剩余部分是否以/开头，若是则会自 动拼接上下文路径

```java
@RequestMapping("/testRedirect")
public String testRedirect(){
	return "redirect:/testHello";
}
```

##### 6.4、视图控制器view-controller

当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用viewcontroller标签进行表示

注： 当SpringMVC中设置任何一个view-controller时，其他控制器中的请求映射将全部失效，此时需 要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签：<mvc:annotation-driven />

```xml
<mvc:annotation-driven />
<!--
path：设置处理的请求地址
view-name：设置请求地址所对应的视图名称
-->
<mvc:view-controller path="/testView" view-name="success"></mvc:view-controller>
```

#### 七、SpringMVC 处理Ajax请求

##### 7.1、@RequestBody

@RequestBody可以获取请求体信息，使用@RequestBody注解标识控制器方法的形参，当前请求的请 求体就会为当前注解所标识的形参赋值

##### 7.2、@RequestBody获取json格式的请求参数

在使用了axios发送ajax请求之后，浏览器发送到服务器的请求参数有两种格式：

1、name=value&name=value...，此时的请求参数可以通过request.getParameter()获取，对应 SpringMVC中，可以直接通过控制器方法的形参获取此类请求参数

2、{key:value,key:value,...}，此时无法通过request.getParameter()获取，之前我们使用操作 json的相关jar包gson或jackson处理此类请求参数，可以将其转换为指定的实体类对象或map集 合。在SpringMVC中，直接使用@RequestBody注解标识控制器方法的形参即可将此类请求参数 转换为java对象

使用@RequestBody获取json格式的请求参数的条件：

（1）导入jackson的依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.1</version>
</dependency>
```

（2）SpringMVC的配置文件中设置开启mvc的注解驱动

```xml
<!--开启mvc的注解驱动-->
<mvc:annotation-driven />
```

（3）在控制器方法的形参位置，设置json格式的请求参数要转换成的java类型（实体类或map）的参 数，并使用@RequestBody注解标识

```html
<input type="button" value="测试@RequestBody获取json格式的请求参数"
@click="testRequestBody()"><br>
<script type="text/javascript" th:src="@{/js/vue.js}"></script>
<script type="text/javascript" th:src="@{/js/axios.min.js}"></script>
<script type="text/javascript">
    var vue = new Vue({
        el:"#app",
        methods:{
            testRequestBody(){
                axios.post(
                "/SpringMVC/test/RequestBody/json",
                {username:"admin",password:"123456"}
                ).then(response=>{
                    console.log(response.data);
                });
            }
        }
    });
</script>
```

```java
//将json格式的数据转换为map集合
@RequestMapping("/test/RequestBody/json")
public void testRequestBody(@RequestBody Map<String, Object> map,
HttpServletResponse response) throws IOException {
    System.out.println(map);
    //{username=admin, password=123456}
    response.getWriter().print("hello,axios");
}
//将json格式的数据转换为实体类对象
@RequestMapping("/test/RequestBody/json")
public void testRequestBody(@RequestBody User user, HttpServletResponse response) throws IOException {
    System.out.println(user);
    //User{id=null, username='admin', password='123456', age=null,
    gender='null'}
    response.getWriter().print("hello,axios");
}
```

##### 7.3、@ResponseBody

@ResponseBody用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到 浏览器

```java
@RequestMapping("/testResponseBody")
public String testResponseBody(){
    //此时会跳转到逻辑视图success所对应的页面
    return "success";
}

@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    //此时响应浏览器数据success
    return "success";
}
```

##### 7.4、@ResponseBody响应浏览器json数据

服务器处理ajax请求之后，大多数情况都需要向浏览器响应一个java对象，此时必须将java对象转换为 json字符串才可以响应到浏览器，之前我们使用操作json数据的jar包gson或jackson将java对象转换为 json字符串。在SpringMVC中，我们可以直接使用@ResponseBody注解实现此功能

使用@ResponseBody注解标识控制器方法，在方法中，将需要转换为json字符串并响应到浏览器 的java对象作为控制器方法的返回值，此时SpringMVC就可以将此对象直接转换为json字符串并响应到 浏览器

```java
//响应浏览器list集合
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public List<User> testResponseBody(){
    User user1 = new User(1001,"admin1","123456",23,"男");
    User user2 = new User(1002,"admin2","123456",23,"男");
    User user3 = new User(1003,"admin3","123456",23,"男");
    List<User> list = Arrays.asList(user1, user2, user3);
    return list;
}
//响应浏览器map集合
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public Map<String, Object> testResponseBody(){
    User user1 = new User(1001,"admin1","123456",23,"男");
    User user2 = new User(1002,"admin2","123456",23,"男");
    User user3 = new User(1003,"admin3","123456",23,"男");
    Map<String, Object> map = new HashMap<>();
    map.put("1001", user1);
    map.put("1002", user2);
    map.put("1003", user3);
    return map;
}
//响应浏览器实体类对象
@RequestMapping("/test/ResponseBody/json")
@ResponseBody
public User testResponseBody(){
	return user;	
}
```

##### 7.5、@RestController注解

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了 

@Controller注解，并且为其中的每个方法添加了@ResponseBody注解

#### 八、文件上传和下载

##### 8.1、文件下载

ResponseEntity用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文使用ResponseEntity实现下载文件的功能

```java
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
    //获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //获取服务器中文件的真实路径
    String realPath = servletContext.getRealPath("/static/img/1.jpg");
    //创建输入流
    InputStream is = new FileInputStream(realPath);
    //创建字节数组
    byte[] bytes = new byte[is.available()];
    //将流读到字节数组中
    is.read(bytes);
    //创建HttpHeaders对象设置响应头信息
    MultiValueMap<String, String> headers = new HttpHeaders();
    //设置要下载方式以及下载文件的名字
    headers.add("Content-Disposition", "attachment;filename=1.jpg");
    //设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    //创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers,
    statusCode);
    //关闭输入流
    is.close();
    return responseEntity;
}
```

##### 8.2、文件上传

文件上传要求form表单的请求方式必须为post，并且添加属性enctype="multipart/form-data" SpringMVC中将上传的文件封装到MultipartFile对象中，通过此对象可以获取文件相关信息

①添加依赖：

```xml
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

②在SpringMVC的配置文件中添加配置：

```xml
<!--必须通过文件解析器的解析才能将文件转换为MultipartFile对象-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
</bean>
```

③控制器方法：

```java
@RequestMapping("/testUp")
public String testUp(MultipartFile photo, HttpSession session) throws
IOException {
    //获取上传的文件的文件名
    String fileName = photo.getOriginalFilename();
    //处理文件重名问题
    String hzName = fileName.substring(fileName.lastIndexOf("."));
    fileName = UUID.randomUUID().toString() + hzName;
    //获取服务器中photo目录的路径
    ServletContext servletContext = session.getServletContext();
    String photoPath = servletContext.getRealPath("photo");
    File file = new File(photoPath);
    if(!file.exists()){
    file.mkdir();
    }
    String finalPath = photoPath + File.separator + fileName;
    //实现上传功能
    photo.transferTo(new File(finalPath));
    return "success";
}
```

#### 九、拦截器

##### 9.1、拦截器的配置

SpringMVC中的拦截器用于拦截控制器方法的执行 

SpringMVC中的拦截器需要实现HandlerInterceptor 

SpringMVC的拦截器必须在SpringMVC的配置文件中进行配置：

```xml
<bean class="com.atguigu.interceptor.FirstInterceptor"></bean>
<ref bean="firstInterceptor"></ref>
<!-- 以上两种配置方式都是对DispatcherServlet所处理的所有的请求进行拦截 -->
<mvc:interceptor>
    <mvc:mapping path="/**"/>
    <mvc:exclude-mapping path="/testRequestEntity"/>
    <ref bean="firstInterceptor"></ref>
</mvc:interceptor>
<!--
以上配置方式可以通过ref或bean标签设置拦截器，通过mvc:mapping设置需要拦截的请求，通过
mvc:exclude-mapping设置需要排除的请求，即不需要拦截的请求
-->
```

##### 9.2、拦截器的三个抽象方法

SpringMVC中的拦截器有三个抽象方法： 

preHandle：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返 回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法 

postHandle：控制器方法执行之后执行postHandle() 

afterCompletion：处理完视图和模型数据，渲染视图完毕之后执行afterCompletion()

##### 9.3、多个拦截器的执行顺序

①若每个拦截器的preHandle()都返回true 此时多个拦截器的执行顺序和拦截器在SpringMVC的配置文件的配置顺序有关： preHandle()会按照配置的顺序执行，而postHandle()和afterCompletion()会按照配置的反序执行 

②若某个拦截器的preHandle()返回了false preHandle()返回false和它之前的拦截器的preHandle()都会执行，postHandle()都不执行，返回false 的拦截器之前的拦截器的afterCompletion()会执行

#### 10、异常处理器

##### 10.1、基于配置的异常处理

SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：HandlerExceptionResolver HandlerExceptionResolver接口的实现类有：DefaultHandlerExceptionResolver和 SimpleMappingExceptionResolver SpringMVC提供了自定义的异常处理器SimpleMappingExceptionResolver，使用方式：

```xml
<bean
class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
<property name="exceptionMappings">
<props>
<!--
properties的键表示处理器方法执行过程中出现的异常
properties的值表示若出现指定异常时，设置一个新的视图名称，跳转到指定页面
-->
<prop key="java.lang.ArithmeticException">error</prop>
</props>
</property>
<!--
exceptionAttribute属性设置一个属性名，将出现的异常信息在请求域中进行共享
-->
<property name="exceptionAttribute" value="ex"></property>
</bean>
```

##### 10.2、基于注解的异常处理

```java
//@ControllerAdvice将当前类标识为异常处理的组件
@ControllerAdvice
public class ExceptionController {
    //@ExceptionHandler用于设置所标识方法处理的异常
    @ExceptionHandler(ArithmeticException.class)
    //ex表示当前请求处理中出现的异常对象
    public String handleArithmeticException(Exception ex, Model model){
        model.addAttribute("ex", ex);
        return "error";
    }
}
```

#### 十一、注解配置SpringMVC

使用配置类和注解代替web.xml和SpringMVC配置文件的功能

##### 11.1、创建初始化类，代替web.xml

在Servlet3.0环境中，容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类， 如果找到的话就用它来配置Servlet容器。 Spring提供了这个接口的实现，名为 SpringServletContainerInitializer，这个类反过来又会查找实现WebApplicationInitializer的类并将配 置的任务交给它们来完成。Spring3.2引入了一个便利的WebApplicationInitializer基础实现，名为 AbstractAnnotationConfigDispatcherServletInitializer，当我们的类扩展了 AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自 动发现它，并用它来配置Servlet上下文。

```java
public class WebInit extends
AbstractAnnotationConfigDispatcherServletInitializer {
    /**
    * 指定spring的配置类
    * @return
    */
    @Override
    protected Class<?>[] getRootConfigClasses() {
    	return new Class[]{SpringConfig.class};
    }
    /**
    * 指定SpringMVC的配置类
    * @return
    */
    @Override
    protected Class<?>[] getServletConfigClasses() {
    	return new Class[]{WebConfig.class};
    }
    /**
    * 指定DispatcherServlet的映射规则，即url-pattern
    * @return
    */
    @Override
    protected String[] getServletMappings() {
    	return new String[]{"/"};
    }
    /**
    * 添加过滤器
    * @return
    */
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
        encodingFilter.setEncoding("UTF-8");
        encodingFilter.setForceRequestEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new
        HiddenHttpMethodFilter();
        return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
    }
}

```

##### 11.2、创建SpringConfig配置类，代替spring的配置文件

```java
@Configuration
public class SpringConfig {
//ssm整合之后，spring的配置信息写在此类中
}
```

11.3、创建WebConfig配置类，代替SpringMVC的配置文件

```java
@Configuration
//扫描组件
@ComponentScan("com.atguigu.mvc.controller")
//开启MVC注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    //使用默认的servlet处理静态资源
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer
    configurer) {
    	configurer.enable();
    }
    //配置文件上传解析器
    @Bean
    public CommonsMultipartResolver multipartResolver(){
    	return new CommonsMultipartResolver();
    }
    //配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        FirstInterceptor firstInterceptor = new FirstInterceptor();
        registry.addInterceptor(firstInterceptor).addPathPatterns("/**");
    }
    //配置视图控制
    /*@Override
    public void addViewControllers(ViewControllerRegistry registry) {
    registry.addViewController("/").setViewName("index");
    }*/
    //配置异常映射
    /*@Override
    public void
    configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
    SimpleMappingExceptionResolver exceptionResolver = new
    SimpleMappingExceptionResolver();
    Properties prop = new Properties();
    prop.setProperty("java.lang.ArithmeticException", "error");
    //设置异常映射
    exceptionResolver.setExceptionMappings(prop);
    //设置共享异常信息的键
    exceptionResolver.setExceptionAttribute("ex");
    resolvers.add(exceptionResolver);
    }*/
    //配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext =
        ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过
        WebApplicationContext 的方法获得
        ServletContextTemplateResolver templateResolver = new
        ServletContextTemplateResolver(
        webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }
    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver
    templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }
    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
}
```

#### 十二、SpringMVC执行流程

##### 12.1、SpringMVC常用组件

- DispatcherServlet：前端控制器，不需要工程师开发，由框架提供  

  作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求 

- HandlerMapping：处理器映射器，不需要工程师开发，由框架提供 

  作用：根据请求的url、method等信息查找Handler，即控制器方法

- Handler：处理器，需要工程师开发 

  作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理 

- HandlerAdapter：处理器适配器，不需要工程师开发，由框架提供 

  作用：通过HandlerAdapter对处理器（控制器方法）进行执行 

- ViewResolver：视图解析器，不需要工程师开发，由框架提供 

  作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、 RedirectView 

- View：视图 

  作用：将模型数据通过页面展示给用户

##### 12.2、DispatcherServlet初始化过程

DispatcherServlet 本质上是一个 Servlet，所以天然的遵循 Servlet 的生命周期。所以宏观上是 Servlet 生命周期来进行调度。

①初始化WebApplicationContext

所在类：org.springframework.web.servlet.FrameworkServlet

②创建WebApplicationContext

所在类：org.springframework.web.servlet.FrameworkServlet

③DispatcherServlet初始化策略

FrameworkServlet创建WebApplicationContext后，刷新容器，调用onRefresh(wac)，此方法在 DispatcherServlet中进行了重写，调用了initStrategies(context)方法，初始化策略，即初始化 DispatcherServlet的各个组件

所在类：org.springframework.web.servlet.DispatcherServlet

##### 12.3、DispatcherServlet调用组件处理请求

①processRequest()

FrameworkServlet重写HttpServlet中的service()和doXxx()，这些方法中调用了 processRequest(request, response) 

所在类：org.springframework.web.servlet.FrameworkServlet

②doService()

所在类：org.springframework.web.servlet.DispatcherServlet

③doDispatch()

所在类：org.springframework.web.servlet.DispatcherServlet

④processDispatchResult()

##### 12.4、SpringMVC的执行流程

1) 用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。

2) DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射： 

   a) 不存在 

   i. 再判断是否配置了mvc:default-servlet-handler 

   ii. 如果没配置，则控制台报映射查找不到，客户端展示404错误

​	   iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404 错误

​	   b) 存在则执行下面的流程

3) 根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及 Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。 

4) DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。 
5) 如果成功获得HandlerAdapter，此时将开始执行拦截器的preHandler(…)方法【正向】 
6) 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。 在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作： a) HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定 的响应信息 b) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等 c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等 d) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中 
7) Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。 
8) 此时将开始执行拦截器的postHandle(...)方法【逆向】。 
9) 根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行 HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model 和View，来渲染视图。
10) 渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。 
11) 将渲染结果返回给客户端。

#### 十三、ssm整合

##### 13.1、ContextLoaderListener

Spring提供了监听器ContextLoaderListener，实现ServletContextListener接口，可监听 ServletContext的状态，在web服务器的启动，读取Spring的配置文件，创建Spring的IOC容器。web 应用中必须在web.xml中配置

```xml
<listener>
    <!--
    配置Spring的监听器，在服务器启动时加载Spring的配置文件
    Spring配置文件默认位置和名称：/WEB-INF/applicationContext.xml
    可通过上下文参数自定义Spring配置文件的位置和名称
    -->
    <listenerclass>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--自定义Spring配置文件的位置和名称-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
</context-param>
```

##### 13.2、准备工作

###### （1）创建maven工程

###### （2）导入依赖

```xml
<packaging>war</packaging>
<properties>
	<spring.version>5.3.1</spring.version>
</properties>

 <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!--springmvc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- Mybatis核心 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
        <!--mybatis和spring的整合包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>
        <!-- 连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.9</version>
        </dependency>
        <!-- junit测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
        <!-- log4j日志 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.2.0</version>
        </dependency>
        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
        <!-- ServletAPI -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.12.1</version>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
        <!-- Spring5和Thymeleaf整合包 -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
            <version>3.0.12.RELEASE</version>
        </dependency>
    </dependencies>
```

##### 13.3、配置web.xml

```xml
<!--配置Spring的编码过滤器-->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--配置处理请求方式的过滤器-->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--配置SpringMVC的前端控制器DispatcherServlet-->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--设置SpringMVC配置文件自定义的位置和名称-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--将DispatcherServlet的初始化时间提前到服务器启动时-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--配置Spring的监听器，在服务器启动时加载Spring的配置文件-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--设置Spring配置文件自定义的位置和名称-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>
```

##### 13.4、创建SpringMVC的配置文件并配置

```xml
  <!--扫描控制层组件-->
    <context:component-scan base-package="com.soft.ssm.controller"></context:component-scan>

    <!--配置视图解析器-->
    <bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8" />
                    </bean>
                </property>
            </bean>
        </property>
    </bean>

    <!--配置默认的servlet处理静态资源-->
    <mvc:default-servlet-handler />

    <!--开启mvc的注解驱动-->
    <mvc:annotation-driven />

    <!--配置视图控制器-->
    <mvc:view-controller path="/" view-name="index"></mvc:view-controller>

    <!--配置文件上传解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```

##### 13.5、搭建MyBatis环境

###### ①创建属性文件jdbc.properties

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_study?serverTimezone=UTC
jdbc.username=root
jdbc.password=123456
```

###### ②创建MyBatis的核心配置文件mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--
        MyBatis核心配置文件中的标签必须要按照指定的顺序配置：
        properties?,settings?,typeAliases?,typeHandlers?,
        objectFactory?,objectWrapperFactory?,reflectorFactory?,
        plugins?,environments?,databaseIdProvider?,mappers?
    -->

    <settings>
        <!--将下划线映射为驼峰-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>

    <plugins>
        <!--配置分页插件-->
        <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
    </plugins>

</configuration>
```

###### ③创建Mapper接口和映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">

</mapper>
```

###### ④创建日志文件log4j.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">

<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">

    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m  (%F:%L) \n" />
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug" />
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info" />
    </logger>
    <root>
        <level value="debug" />
        <appender-ref ref="STDOUT" />
    </root>
</log4j:configuration>
```

##### 13.6、创建Spring的配置文件并配置

```xml
<!--扫描组件（除控制层）-->
    <context:component-scan base-package="com.soft.ssm">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--引入jdbc.properties-->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>

    <!--配置数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>

    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--
        开启事务的注解驱动
        将使用注解@Transactional标识的方法或类中所有的方法进行事务管理
    -->
    <tx:annotation-driven transaction-manager="transactionManager" />

    <!--配置SqlSessionFactoryBean，可以直接在Spring的IOC中获取SqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--设置MyBatis的核心配置文件的路径-->
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
        <!--设置数据源-->
        <property name="dataSource" ref="dataSource"></property>
        <!--设置类型别名所对应的包-->
        <property name="typeAliasesPackage" value="com.soft.ssm.model"></property>
        <!--设置映射文件的路径，只有映射文件的包和mapper接口的包不一致时需要设置-->
        <!--<property name="mapperLocations" value="classpath:mappers/*.xml"></property>-->
    </bean>

    <!--
        配置mapper接口的扫描，可以将指定包下所有的mapper接口
        通过SqlSession创建代理实现类对象，并将这些对象交给IOC容器管理
    -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.soft.ssm.mapper"></property>
    </bean>
```

