

# Spring

#### 1、Spring简介

##### 1.1、Spring Framework特性

- 非侵入式：使用 Spring Framework 开发应用程序时，Spring 对应用程序本身的结构影响非常 小。对领域模型可以做到零污染；对功能性组件也只需要使用几个简单的注解进行标记，完全不会 破坏原有结构，反而能将组件结构进一步简化。这就使得基于 Spring Framework 开发应用程序 时结构清晰、简洁优雅。
- 控制反转：IOC——Inversion of Control，翻转资源获取方向。把自己创建资源、向环境索取资源 变成环境将资源准备好，我们享受资源注入。
- 面向切面编程：AOP——Aspect Oriented Programming，在不修改源代码的基础上增强代码功 能。
- 容器：Spring IOC 是一个容器，因为它包含并且管理组件对象的生命周期。组件享受到了容器化 的管理，替程序员屏蔽了组件创建过程中的大量细节，极大的降低了使用门槛，大幅度提高了开发 效率。
- 组件化：Spring 实现了使用简单的组件配置组合成一个复杂的应用。在 Spring 中可以使用 XML 和 Java 注解组合这些对象。这使得我们可以基于一个个功能明确、边界清晰的组件有条不紊的搭 建超大型复杂应用系统。
- 声明式：很多以前需要编写代码才能实现的功能，现在只需要声明需求即可由框架代为实现。
- 一站式：在 IOC 和 AOP 的基础上可以整合各种企业应用的开源框架和优秀的第三方类库。而且 Spring 旗下的项目已经覆盖了广泛领域，很多方面的功能性需求可以在 Spring Framework 的基 础上全部使用 Spring 来实现。

##### 1.2、Spring Framework五大功能模块

| 功能模块                | 功能介绍                                                    |
| ----------------------- | ----------------------------------------------------------- |
| Core Container          | 核心容器，在 Spring 环境下使用任何功能都必须基于 IOC 容器。 |
| AOP&Aspects             | 面向切面编程                                                |
| Testing                 | 提供了对 junit 或 TestNG 测试框架的整合。                   |
| Data Access/Integration | 提供了对数据访问/集成的功能。                               |
| Spring MVC              | 提供了面向Web应用程序的集成功能。                           |

#### 2、IOC

##### 2.1、IOC思想

IOC：Inversion of Control，翻译过来是反转控制。

1. 获取资源的传统方式

   在应用程序中的组件需要获取资源时，传统的方式是组件主动的从容器中获取所需要的资源，在这样的 模式下开发人员往往需要知道在具体容器中特定资源的获取方式，增加了学习成本，同时降低了开发效 率。

1. 反转控制方式获取资源

   反转控制的思想完全颠覆了应用程序组件获取资源的传统方式：反转了资源的获取方向——改由容器主 动的将资源推送给需要的组件，开发人员不需要知道容器是如何创建资源对象的，只需要提供接收资源 的方式即可，极大的降低了学习成本，提高了开发的效率。这种行为也称为查找的被动形式。

1. DI：Dependency Injection，翻译过来是依赖注入。 

   DI 是 IOC 的另一种表述方式：即组件以一些预先定义好的方式（例如：setter 方法）接受来自于容器 的资源注入。相对于IOC而言，这种表述更直接。 所以结论是：IOC 就是一种反转控制的思想， 而 DI 是对 IOC 的一种具体实现。

##### 2.2、基于注解管理bean

**@Component：将类标识为普通组件** 

**@Controller：将类标识为控制层组件** 

**@Service：将类标 识为业务层组件** 

**@Repository：将类标识为持久层组件**

通过查看源码我们得知，@Controller、@Service、@Repository这三个注解只是在@Component注解 的基础上起了三个新的名字。 对于Spring使用IOC容器管理这些组件来说没有区别。所以@Controller、@Service、@Repository这 三个注解只是给开发人员看的，让我们能够便于分辨组件的作用。 注意：虽然它们本质上一样，但是为了代码的可读性，为了程序结构严谨我们肯定不能随便胡乱标记。

###### 2.2.1扫描组件

情况1：最基本的扫描方式

```xml
<!--扫描组件：精确一点可减少扫描时间-->
<context:component-scan base-package="com.atguigu">
</context:component-scan>
```

情况2：指定要排除的组件

```xml
<context:component-scan base-package="com.atguigu">
<!-- context:exclude-filter标签：指定排除规则 -->
<!--
type：设置排除或包含的依据
type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
-->
<context:exclude-filter type="annotation"
    expression="org.springframework.stereotype.Controller"/>
    <!--<context:exclude-filter type="assignable"
    expression="com.atguigu.controller.UserController"/>-->
</context:component-scan>

```

情况3：仅扫描指定组件

```xml
<context:component-scan base-package="com.atguigu" use-default-filters="false">
<!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 -->
<!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
<!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 -->
<!--
type：设置排除或包含的依据
type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
-->
<context:include-filter type="annotation"
    expression="org.springframework.stereotype.Controller"/>
    <!--<context:include-filter type="assignable"
    expression="com.atguigu.controller.UserController"/>-->
</context:component-scan>

```

###### 2.2.2组件所对应bean的id

在我们使用XML方式管理bean的时候，每个bean都有一个唯一标识，便于在其他地方引用。现在使用 注解后，每个组件仍然应该有一个唯一标识。

默认情况 

类名首字母小写就是bean的id。

例如：UserController类对应的bean的id就是userController。

 自定义bean的id 

可通过标识组件的注解的value属性设置自定义的bean的id 

@Service("userService")//默认为userServiceImpl public class UserServiceImpl implements UserService {}

2.3、基于注解自动装配

（1）@Autowired：**先根据类型（byType）查找，如果存在多个（Bean）再根据名称（byName）进行查找；**

（2）@Resource：**先根据名称（byName）查找，如果（根据名称）查找不到，再根据类型（byType）进行查找。**

**@Autowired 支持属性注入、构造方法注入和 Setter 注入，而 @Resource 只支持属性注入和 Setter 注入**

#### 3、AOP

##### 3.1、代理模式

二十三种设计模式中的一种，属于结构型模式。它的作用就是通过提供一个代理类，让我们在调用目标 方法的时候，不再是直接对目标方法进行调用，而是通过代理类间接调用。让不属于目标方法核心逻辑 的代码从目标方法中剥离出来——解耦。调用目标方法时先调用代理对象的方法，减少对目标方法的调 用和打扰，同时让附加功能能够集中在一起也有利于统一维护。

**代理：**将非核心逻辑剥离出来以后，封装这些非核心逻辑的类、对象、方法。 

**目标：**被代理“套用”了非核心逻辑代码的类、对象、方法。

##### 3.2、静态代理

声明计算器接口Calculator，包含加减乘除的抽象方法

```java
public interface Calculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}

```

创建静态代理类

```java
public class CalculatorStaticProxy implements Calculator {
    // 将被代理的目标对象声明为成员变量
    private Calculator target;
    public CalculatorStaticProxy(Calculator target) {
        this.target = target;
    }
    @Override
    public int add(int i, int j) {
        // 附加功能由代理类中的代理方法来实现
        System.out.println("[日志] add 方法开始了，参数是：" + i + "," + j);
        // 通过目标对象来实现核心业务逻辑
        int addResult = target.add(i, j);
        System.out.println("[日志] add 方法结束了，结果是：" + addResult);
        return addResult;
	}
}
```

静态代理确实实现了解耦，但是由于代码都写死了，完全不具备任何的灵活性。就拿日志功能来 说，将来其他地方也需要附加日志，那还得再声明更多个静态代理类，那就产生了大量重复的代 码，日志功能还是分散的，没有统一管理。

提出进一步的需求：将日志功能集中到一个代理类中，将来有任何日志需求，都通过这一个代理 类来实现。这就需要使用动态代理技术了。

##### 3.3、动态代理

生产代理对象的工厂类

```java
/**
 * Date:2022/7/4
 * Author:ybc
 * Description:
 */
public class ProxyFactory {

    private Object target;

    public ProxyFactory(Object target) {
        this.target = target;
    }

    public Object getProxy(){
        /**
         * ClassLoader loader：指定加载动态生成的代理类的类加载器
         * Class[] interfaces：获取目标对象实现的所有接口的class对象的数组
         * InvocationHandler h：设置代理类中的抽象方法如何重写
         */
        ClassLoader classLoader = this.getClass().getClassLoader();
        Class<?>[] interfaces = target.getClass().getInterfaces();
        InvocationHandler h = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                Object result = null;
                try {
                    System.out.println("日志，方法："+method.getName()+"，参数："+ Arrays.toString(args));
                    //proxy表示代理对象，method表示要执行的方法，args表示要执行的方法到的参数列表
                    result = method.invoke(target, args);
                    System.out.println("日志，方法："+method.getName()+"，结果："+ result);
                } catch (Exception e) {
                    e.printStackTrace();
                    System.out.println("日志，方法："+method.getName()+"，异常："+ e);
                } finally {
                    System.out.println("日志，方法："+method.getName()+"，方法执行完毕");
                }
                return result;
            }
        };
        return Proxy.newProxyInstance(classLoader, interfaces, h);
    }
}
```

测试

```java
/**
     * 动态代理有两种：
     * 1、jdk动态代理，要求必须有接口，最终生成的代理类和目标类实现相同的接口
     * 在com.sun.proxy包下，类名为$proxy2
     * 2、cglib动态代理，最终生成的代理类会继承目标类，并且和目标类在相同的包下
     */

    @Test
    public void testProxy(){
        /*CalculatorStaticProxy proxy = new CalculatorStaticProxy(new CalculatorImpl());
        proxy.add(1, 2);*/
        ProxyFactory proxyFactory = new ProxyFactory(new CalculatorImpl());
        Calculator proxy = (Calculator) proxyFactory.getProxy();
        proxy.div(1,0);
    }

```

##### 3.4、AOP概念及相关术语

###### 3.4.1、概述

AOP（Aspect Oriented Programming）是一种设计思想，是软件设计领域中的面向切面编程，它是面 向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况 下给程序动态统一添加额外功能的一种技术。

###### 3.4.2、相关术语

###### （1）**横切关注点**

从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方 法进行多个不同方面的增强。

这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个横 切关注点。

###### （2）通知

每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法。

- 前置通知：在被代理的目标方法前执行
- 返回通知：在被代理的目标方法成功结束后执行（寿终正寝）
- 异常通知：在被代理的目标方法异常结束后执行（死于非命）
- 后置通知：在被代理的目标方法最终结束后执行（盖棺定论）
- 环绕通知：使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所 有位置

###### （3）切面

封装通知方法的类

###### （4）目标

被代理的目标对象

###### （5）代理

向目标对象应用通知之后创建的代理对象

###### （6）连接点

这也是一个纯逻辑概念，不是语法定义的。 把方法排成一排，每一个横切位置看成x轴方向，把方法从上到下执行的顺序看成y轴，x轴和y轴的交叉 点就是连接点。

###### （7）切入点

定位连接点的方式。 每个类的方法中都包含多个连接点，所以连接点是类中客观存在的事物（从逻辑上来说）。 如果把连接点看作数据库中的记录，那么切入点就是查询记录的 SQL 语句。 Spring 的 AOP 技术可以通过切入点定位到特定的连接点。 切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条 件。

###### 3.4.3、作用

- 简化代码：把方法中固定位置的重复的代码抽取出来，让被抽取的方法更专注于自己的核心功能， 提高内聚性。
- 代码增强：把特定的功能封装到切面类中，看哪里有需要，就往上套，被套用了切面逻辑的方法就 被切面给增强了。

##### 3.5、基于注解的AOP

###### 3.5.1、技术说明

- 动态代理（InvocationHandler）：JDK原生的实现方式，需要被代理的目标类必须实现接口。因 为这个技术要求代理对象和目标对象实现同样的接口（兄弟两个拜把子模式）。
- cglib：通过继承被代理的目标类（认干爹模式）实现代理，所以不需要目标类实现接口。
- AspectJ：本质上是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最 终效果是动态的。weaver就是织入器。Spring只是借用了AspectJ中的注解。

###### 3.5.2、准备工作

###### （1）添加依赖

在IOC所需依赖基础上再加入下面依赖即可：

```xml
<!-- spring-aspects会帮我们传递过来aspectjweaver -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>

```

###### （2）准备被代理的目标资源

接口

```java
public interface Calculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}
```

实现类

```java
@Component
public class CalculatorPureImpl implements Calculator {
    @Override
    public int add(int i, int j) {
        int result = i + j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int sub(int i, int j) {
        int result = i - j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int mul(int i, int j) {
        int result = i * j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
    @Override
    public int div(int i, int j) {
        int result = i / j;
        System.out.println("方法内部 result = " + result);
        return result;
    }
}

```

###### 3.5.3、创建切面类并配置

```java
/**
 * Date:2022/7/4
 * Author:ybc
 * Description:
 * 1、在切面中，需要通过指定的注解将方法标识为通知方法
 * @Before：前置通知，在目标对象方法执行之前执行
 * @After：后置通知，在目标对象方法的finally字句中执行
 * @AfterReturning：返回通知，在目标对象方法返回值之后执行
 * @AfterThrowing：异常通知，在目标对象方法的catch字句中执行
 *
 *
 * 2、切入点表达式：设置在标识通知的注解的value属性中
 * execution(public int com.atguigu.spring.aop.annotation.CalculatorImpl.add(int, int)
 * execution(* com.atguigu.spring.aop.annotation.CalculatorImpl.*(..)
 * 第一个*表示任意的访问修饰符和返回值类型
 * 第二个*表示类中任意的方法
 * ..表示任意的参数列表
 * 类的地方也可以使用*，表示包下所有的类
 * 3、重用切入点表达式
 * //@Pointcut声明一个公共的切入点表达式
 * @Pointcut("execution(* com.atguigu.spring.aop.annotation.CalculatorImpl.*(..))")
 * public void pointCut(){}
 * 使用方式：@Before("pointCut()")
 *
 * 4、获取连接点的信息
 * 在通知方法的参数位置，设置JoinPoint类型的参数，就可以获取连接点所对应方法的信息
 * //获取连接点所对应方法的签名信息
 * Signature signature = joinPoint.getSignature();
 * //获取连接点所对应方法的参数
 * Object[] args = joinPoint.getArgs();
 *
 * 5、切面的优先级
 * 可以通过@Order注解的value属性设置优先级，默认值Integer的最大值
 * @Order注解的value属性值越小，优先级越高
 *
 */
@Component
@Aspect //将当前组件标识为切面
public class LoggerAspect {

    @Pointcut("execution(* com.atguigu.spring.aop.annotation.CalculatorImpl.*(..))")
    public void pointCut(){}

    //@Before("execution(public int com.atguigu.spring.aop.annotation.CalculatorImpl.add(int, int))")
    //@Before("execution(* com.atguigu.spring.aop.annotation.CalculatorImpl.*(..))")
    @Before("pointCut()")
    public void beforeAdviceMethod(JoinPoint joinPoint) {
        //获取连接点所对应方法的签名信息
        Signature signature = joinPoint.getSignature();
        //获取连接点所对应方法的参数
        Object[] args = joinPoint.getArgs();
        System.out.println("LoggerAspect，方法："+signature.getName()+"，参数："+ Arrays.toString(args));
    }

    @After("pointCut()")
    public void afterAdviceMethod(JoinPoint joinPoint){
        //获取连接点所对应方法的签名信息
        Signature signature = joinPoint.getSignature();
        System.out.println("LoggerAspect，方法："+signature.getName()+"，执行完毕");
    }

    /**
     * 在返回通知中若要获取目标对象方法的返回值
     * 只需要通过@AfterReturning注解的returning属性
     * 就可以将通知方法的某个参数指定为接收目标对象方法的返回值的参数
     */
    @AfterReturning(value = "pointCut()", returning = "result")
    public void afterReturningAdviceMethod(JoinPoint joinPoint, Object result){
        //获取连接点所对应方法的签名信息
        Signature signature = joinPoint.getSignature();
        System.out.println("LoggerAspect，方法："+signature.getName()+"，结果："+result);
    }

    /**
     * 在异常通知中若要获取目标对象方法的异常
     * 只需要通过AfterThrowing注解的throwing属性
     * 就可以将通知方法的某个参数指定为接收目标对象方法出现的异常的参数
     */
    @AfterThrowing(value = "pointCut()", throwing = "ex")
    public void afterThrowingAdviceMethod(JoinPoint joinPoint, Throwable ex){
        //获取连接点所对应方法的签名信息
        Signature signature = joinPoint.getSignature();
        System.out.println("LoggerAspect，方法："+signature.getName()+"，异常："+ex);
    }

    @Around("pointCut()")
    //环绕通知的方法的返回值一定要和目标对象方法的返回值一致
    public Object aroundAdviceMethod(ProceedingJoinPoint joinPoint){
        Object result = null;
        try {
            System.out.println("环绕通知-->前置通知");
            //表示目标对象方法的执行
            result = joinPoint.proceed();
            System.out.println("环绕通知-->返回通知");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println("环绕通知-->异常通知");
        } finally {
            System.out.println("环绕通知-->后置通知");
        }
        return result;
    }

}
```

在Spring的配置文件中配置：

```xml
<!--
基于注解的AOP的实现：
1、将目标对象和切面交给IOC容器管理（注解+扫描）
2、开启AspectJ的自动代理，为目标对象自动生成代理
3、将切面类通过注解@Aspect标识
-->
<context:component-scan base-package="com.atguigu.aop.annotation">
</context:component-scan>
<aop:aspectj-autoproxy />
```

###### 3.5.4、各种通知

- 前置通知：使用@Before注解标识，在被代理的目标方法前执行 
- 返回通知：使用@AfterReturning注解标识，在被代理的目标方法成功结束后执行（寿终正寝） 
- 异常通知：使用@AfterThrowing注解标识，在被代理的目标方法异常结束后执行（死于非命） 
- 后置通知：使用@After注解标识，在被代理的目标方法最终结束后执行（盖棺定论） 
- 环绕通知：使用@Around注解标识，使用try...catch...finally结构围绕整个被代理的目标方法，包 括上面四种通知对应的所有位置

各种通知的执行顺序： Spring版本5.3.x以前： 

前置通知 目标操作 后置通知 返回通知或异常通知

 Spring版本5.3.x以后： 

前置通知 目标操作 返回通知或异常通知 后置通知

###### 3.5.5、基于xml的AOP（了解）

准备工作参考基于注解的AOP环境

实现

```xml
<context:component-scan base-package="com.atguigu.aop.xml"></context:componentscan>
<aop:config>
    <!--配置切面类-->
    <aop:aspect ref="loggerAspect">
        <aop:pointcut id="pointCut" expression="execution(*
        com.atguigu.aop.xml.CalculatorImpl.*(..))"/>
        <aop:before method="beforeMethod" pointcut-ref="pointCut"></aop:before>
        <aop:after method="afterMethod" pointcut-ref="pointCut"></aop:after>
        <aop:after-returning method="afterReturningMethod" returning="result"
        pointcut-ref="pointCut"></aop:after-returning>
        <aop:after-throwing method="afterThrowingMethod" throwing="ex" pointcutref="pointCut"></aop:after-throwing>
        <aop:around method="aroundMethod" pointcut-ref="pointCut"></aop:around>
    </aop:aspect>
    <aop:aspect ref="validateAspect" order="1">   
        <aop:before method="validateBeforeMethod" pointcut-ref="pointCut">
        </aop:before>
    </aop:aspect>
</aop:config>

```

#### 4、声明式事物

既然事务控制的代码有规律可循，代码的结构基本是确定的，所以框架就可以将固定模式的代码抽取出 来，进行相关的封装。

封装起来后，我们只需要在配置文件中进行简单的配置即可完成操作。

- 好处1：提高开发效率 
- 好处2：消除了冗余的代码 
- 好处3：框架会综合考虑相关领域中在实际开发环境下有可能遇到的各种问题，进行了健壮性、性 能等各个方面的优化

- 编程式：自己写代码实现功能 
- 声明式：通过配置让框架实现功能

##### 4.1、基于注解的声明式事物

###### 4.1.1、加入事物

###### （1）添加事物配置

在Spring的配置文件中添加配置：

```xml
<bean id="transactionManager"
class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>
<!--
开启事务的注解驱动
通过注解@Transactional所标识的方法或标识的类中所有的方法，都会被事务管理器管理事务
-->
<!-- transaction-manager属性的默认值是transactionManager，如果事务管理器bean的id正好就
是这个默认值，则可以省略这个属性 -->
<tx:annotation-driven transaction-manager="transactionManager" />
```

注意：导入的名称空间需要 tx 结尾的那个。

###### （2）添加事务注解 

因为service层表示业务逻辑层，一个方法表示一个完成的功能，因此处理事务一般在service层处理 在BookServiceImpl的buybook()添加注解@Transactional

###### （3）观察结果

由于使用了Spring的声明式事务，更新库存和更新余额都没有执行

###### 4.1.2、@Transactional注解标识的位置

@Transactional标识在方法上，咋只会影响该方法 

@Transactional标识的类上，咋会影响类中所有的方法

###### 4.1.3、事物属性

###### （1）只读

对一个查询操作来说，如果我们把它设置成只读，就能够明确告诉数据库，这个操作不涉及写操作。这 样数据库就能够针对查询操作来进行优化。

**使用方式：**在service层方法上加 @Transactional(readOnly = true)   

**注意：** 对增删改操作设置只读会抛出下面异常： Caused by: java.sql.SQLException: Connection is read-only. Queries leading to data modification are not allowed

###### （2）超时

事务在执行过程中，有可能因为遇到某些问题，导致程序卡住，从而长时间占用数据库资源。而长时间 占用资源，大概率是因为程序运行出现了问题（可能是Java程序或MySQL数据库或网络连接等等）。 此时这个很可能出问题的程序应该被回滚，撤销它已做的操作，事务结束，把资源让出来，让其他正常 程序可以执行。概括来说就是一句话：超时回滚，释放资源。

**使用方式：**在service层方法上加 @Transactional(timeout = 3)

执行过程中抛出异常：

 org.springframework.transaction.TransactionTimedOutException: Transaction timed out: deadline was Fri Jun 04 16:25:39 CST 2022

###### （3）回滚策略

声明式事务默认只针对运行时异常回滚，编译时异常不回滚。 可以通过@Transactional中相关属性设置回滚策略

- rollbackFor属性：需要设置一个Class类型的对象
-  rollbackForClassName属性：需要设置一个字符串类型的全类名 
- noRollbackFor属性：需要设置一个Class类型的对象 
- rollbackFor属性：需要设置一个字符串类型的全类名

**使用方式：**在service层方法上加 @Transactional(noRollbackFor = ArithmeticException.class)

虽然购买图书功能中出现了数学运算异常（ArithmeticException），但是我们设置的回滚策略是，当 出现ArithmeticException不发生回滚，因此购买图书的操作正常执行

###### （4）事物隔离级别

数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响，避免各种并发问题。一个事 务与其他事务隔离的程度称为隔离级别。SQL标准中规定了多种事务隔离级别，不同隔离级别对应不同 的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱。

隔离级别一共有四种：

- 读未提交：READ UNCOMMITTED 允许Transaction01读取Transaction02未提交的修改。 

- 读已提交：READ COMMITTED、 要求Transaction01只能读取Transaction02已提交的修改。 

- 可重复读：REPEATABLE READ 

  确保Transaction01可以多次从一个字段中读取到相同的值，即Transaction01执行期间禁止其它 事务对这个字段进行更新。 

- 串行化：SERIALIZABLE 

​	   确保Transaction01可以多次从一个表中读取到相同的行，在Transaction01执行期间，禁止其它 事务对这个表进行添加、更新、删除操作。可以避免任何并发问题，但性能十分低下。

使用方式：

```java
@Transactional(isolation = Isolation.DEFAULT)//使用数据库默认的隔离级别
@Transactional(isolation = Isolation.READ_UNCOMMITTED)//读未提交
@Transactional(isolation = Isolation.READ_COMMITTED)//读已提交
@Transactional(isolation = Isolation.REPEATABLE_READ)//可重复读
@Transactional(isolation = Isolation.SERIALIZABLE)//串行化
```

###### （5）事物传播行为

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中 运行，也可能开启一个新事务，并在自己的事务中运行。

可以通过@Transactional中的propagation属性设置事务传播行为 

修改BookServiceImpl中buyBook()上，注解@Transactional的propagation属性 

@Transactional(propagation = Propagation.REQUIRED)，默认情况，表示如果当前线程上有已经开 启的事务可用，那么就在这个事务中运行。经过观察，购买图书的方法buyBook()在checkout()中被调 用，checkout()上有事务注解，因此在此事务中执行。所购买的两本图书的价格为80和50，而用户的余 额为100，因此在购买第二本图书时余额不足失败，导致整个checkout()回滚，即只要有一本书买不 了，就都买不了

@Transactional(propagation = Propagation.REQUIRES_NEW)，表示不管当前线程上是否有已经开启 的事务，都要开启新事务。同样的场景，每次购买图书都是在buyBook()的事务中执行，因此第一本图 书购买成功，事务结束，第二本图书购买失败，只在第二次的buyBook()中回滚，购买第一本图书不受 影响，即能买几本就买几本

##### 4.2、基于XML的声明式事物

将Spring配置文件中去掉tx:annotation-driven 标签，并添加配置：

```xml
<aop:config>
<!-- 配置事务通知和切入点表达式 -->
    <aop:advisor advice-ref="txAdvice" pointcut="execution(*
       com.atguigu.spring.tx.xml.service.impl.*.*(..))"></aop:advisor>
</aop:config>
    <!-- tx:advice标签：配置事务通知 -->
    <!-- id属性：给事务通知标签设置唯一标识，便于引用 -->
    <!-- transaction-manager属性：关联事务管理器 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
    <!-- tx:method标签：配置具体的事务方法 -->
    <!-- name属性：指定方法名，可以使用星号代表多个字符 -->
    <tx:method name="get*" read-only="true"/>
    <tx:method name="query*" read-only="true"/>
    <tx:method name="find*" read-only="true"/>
    <!-- read-only属性：设置只读属性 -->
    <!-- rollback-for属性：设置回滚的异常 -->
    <!-- no-rollback-for属性：设置不回滚的异常 -->
    <!-- isolation属性：设置事务的隔离级别 -->
    <!-- timeout属性：设置事务的超时属性 -->
    <!-- propagation属性：设置事务的传播行为 -->
    <tx:method name="save*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
    <tx:method name="update*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
    <tx:method name="delete*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
    </tx:attributes>
</tx:advice>
```

注意：基于xml实现的声明式事务，必须引入aspectJ的依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.1</version>
</dependency>
```

