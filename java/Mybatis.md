# Mybatis

#### 环境搭建

构建工具：maven 3.5.4 

MySQL版本：MySQL 8 

MyBatis版本：MyBatis 3.5.7 

MySQL不同版本的注意事项 

1、驱动类driver-class-name 

MySQL 5版本使用jdbc5驱动，驱动类使用：com.mysql.jdbc.Driver

 MySQL 8版本使用jdbc8驱动，驱动类使用：com.mysql.cj.jdbc.Driver 

2、连接地址url 

MySQL 5版本的url： jdbc:mysql://localhost:3306/ssm 

MySQL 8版本的url： jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC 

否则运行测试用例报告如下错误： java.sql.SQLException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more

#### 核心配置文件

##### 1、依赖包

```xml
 <dependencies>
        <!-- Mybatis核心 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
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
    </dependencies>

```

##### 2、MyBatis核心配置文件

习惯上命名为mybatis-config.xml，这个文件名仅仅只是建议，并非强制要求。将来整合Spring 之后，这个配置文件可以省略。

 核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息 核心配置文件存放的位置是src/main/resources目录下

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

    <!--引入properties文件，此后就可以在当前文件中使用的方式访问value-->
    <properties resource="jdbc.properties"/>
    
    <settings>
        <!--将下划线映射为驼峰-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>

    <!--
        typeAliases：设置类型别名，即为某个具体的类型设置一个别名
        在MyBatis的范围中，就可以使用别名表示一个具体的类型
    -->
    <typeAliases>
        <!--
            type：设置需要起别名的类型
            alias：设置某个类型的别名
        -->
        <!--<typeAlias type="com.atguigu.mybatis.pojo.User" alias="abc"></typeAlias>-->
        <!--若不设置alias，当前的类型拥有默认的别名，即类名且不区分大小写-->
        <!--<typeAlias type="com.atguigu.mybatis.pojo.User"></typeAlias>-->
        <!--通过包设置类型别名，指定包下所有的类型将全部拥有默认的别名，即类名且不区分大小写-->
        <package name="org.soft.model"/>
    </typeAliases>

    <!--
        environments：配置连接数据库的环境
        属性：
        default：设置默认使用的环境的id
    -->
    <environments default="development">
        <!--
            environment：设置一个具体的连接数据库的环境
            属性：
            id：设置环境的唯一标识，不能重复
        -->
        <environment id="development">
            <!--
                transactionManager：设置事务管理器
                属性：
                type：设置事务管理的方式
                type="JDBC|MANAGED"
                JDBC：表示使用JDBC中原生的事务管理方式
                MANAGED：被管理，例如Spring
            -->
            <transactionManager type="JDBC"/>
            <!--
                dataSource：设置数据源
                属性：
                type：设置数据源的类型
                type="POOLED|UNPOOLED|JNDI"
                POOLED：表示使用数据库连接池
                UNPOOLED：表示不使用数据库连接池
                JNDI：表示使用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入mybatis的映射文件-->
    <mappers>
        <!--        <mapper resource="mappers/UserMapper.xml"/>-->
        <!--
            以包的方式引入映射文件，但是必须满足两个条件：
            1、mapper接口和映射文件所在的包必须一致
            2、mapper接口的名字和映射文件的名字必须一致
        -->
        <package name="org/soft/mapper"/>
    </mappers>
</configuration>
```

jdbc.properties

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_study?serverTimezone=UTC
jdbc.username=root
jdbc.password=123456
```

##### 3、MyBatis映射文件

1、映射文件的命名规则： 

表所对应的实体类的类名+Mapper.xml 

例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml 

因此一个映射文件对应一个实体类，对应一张表的操作 

MyBatis映射文件用于编写SQL，访问以及操作表中的数据 

MyBatis映射文件存放的位置是src/main/resources/mappers目录下 

2、 MyBatis中可以面向接口操作数据，要保证两个一致： 

a>mapper接口的全类名和映射文件的命名空间（namespace）保持一致 

b>mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=mapper接口类全路径-->
<mapper namespace="org.example.mapper.UserMapper">
<!--id=mapper接口中的方法名-->
    <insert id="insertUser">
        insert into t_user values(null,'李四','123456',18,'男','260521812@qq.com')
    </insert>

    <update id="updateUser">
        update t_user set username = '张三' where id = 1
    </update>

    <delete id="delUser">
        delete from t_user where id = 2
    </delete>

    <select id="selectUserById" resultType="org.example.model.User">
        select * from t_user where id = 1
    </select>

    <select id="selectAllUser" resultType="org.example.model.User">
        select * from t_user
    </select>

</mapper>

```

##### 4、加入log4j日志功能

###### （1）添加依赖

```xml
<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

###### （2）加入log4j配置文件

   log4j的配置文件名为log4j.xml，存放的位置是src/main/resources目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}
%m (%F:%L) \n"/>
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug"/>
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info"/>
    </logger>
    <root>
        <level value="debug"/>
        <appender-ref ref="STDOUT"/>
    </root>
</log4j:configuration>
```

###### （3）日志级别

 FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试) 从左到右打印的内容越来越详细

#### MyBatis获取参数的两种方式

MyBatis获取参数的两种方式：#{} 、${}

${}：本质是字符串拼接。注意该方法会引起SQL注入，若为字符串类型或日期类型的字段进行赋值时，需要手动加单号；

#{}：占位符赋值（推荐使用）。

##### 1、单个字面量参数

若mapper接口中的方法参数为单个的字面量类型

此时可以使用${}和#{}以任意的名称获取参数的值，注意${}需要手动加单引号

##### 2、多个字面量类型参数的参数

若mapper接口中的方法参数为多个时 

此时MyBatis会自动将这些参数放在一个map集合中，

以arg0,arg1...为键，以参数为值；

以 param1,param2...为键，以参数为值；

因此只需要通过${}和#{}访问map集合的键就可以获取相 对应的 值，注意${}需要手动加单引号

##### 3、Map集合类型为参数

若mapper接口中的方法需要多个参数时，可手动创建map集合，将数据放在map集合中。

通过map集合中的键访问参数值。（#{}。${}需要手动添加单引号）。

##### 4、实体类型的参数

mapper接口方法中的参数为实体类型，可以通过实体类属性名获取值（#{} 。 ${}需要手动添加单引号）

##### 5、使用@Param注解

当mapper接口传递多参数的时候可以使用@Param注解标注参数名。

```java
//mapper接口
public int fn(@Param("username") String username,@Param(password) String password);
```

```xml
<!--xml SQl语句-->
<select id="fn" resultType="User">
	select * from t_user where username = #{username} and password = #{password}
</select>
```

#### 特殊SQL执行

##### 1、模糊查询

```java
//接口
List<User> getUserByLike(@Param("username") String username);
```

```xml
<select id="getUserByLike" resultType="User">
        <!--select * from t_user where username like '%${username}%'-->
        <!--select * from t_user where username like concat('%',#{username},'%')-->
        select * from t_user where username like "%"#{username}"%"
</select>
```

##### 2、获取主键

```java
/**
* 添加用户信息
* @param user
* @return
* useGeneratedKeys：设置使用自增的主键
* keyProperty：因为增删改有统一的返回值是受影响的行数，因此只能将获取的自增的主键放在传输的参
数user对象的某个属性中
*/
int insertUser(User user);
```

```xml
<!--int insertUser(User user);-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
	insert into t_user values(null,#{username},#{password},#{age},#{sex})
</insert>
```

#### resultMap标签

##### 1、resultMap处理字段和属性的映射关系

若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射

```xml
<!--
resultMap：设置自定义映射
属性：
id：表示自定义映射的唯一标识
type：查询的数据要映射的实体类的类型
子标签：
id：设置主键的映射关系
result：设置普通字段的映射关系
association：设置多对一的映射关系
collection：设置一对多的映射关系
属性：
property：设置映射关系中实体类中的属性名
column：设置映射关系中表中的字段名
-->
<resultMap id="userMap" type="User">
    <id property="id" column="id"></id>
    <result property="userName" column="user_name"></result>
    <result property="password" column="password"></result>
    <result property="age" column="age"></result>
    <result property="sex" column="sex"></result>
</resultMap>
<!--List<User> testMohu(@Param("mohu") String mohu);-->
<select id="testMohu" resultMap="userMap">
    <!--select * from t_user where username like '%${mohu}%'-->
    select id,user_name,password,age,sex from t_user where user_name like
    concat('%',#{mohu},'%')
</select>

```

若字段名和实体类中的属性名不一致，但是字段名符合数据库的规则（使用_），实体类中的属性 名符合Java的规则（使用驼峰） 

此时也可通过以下两种方式处理字段名和实体类中的属性的映射关系 

a>可以通过为字段起别名的方式，保证和实体类中的属性名保持一致 

_b>可以在MyBatis的核心配置文件中设置一个全局配置信息mapUnderscoreToCamelCase，可 以在查询表中数据时，自动将_类型的字段名转换为驼峰 

例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为 userName

##### 2、多对一处理映射

###### 1、级联方式处理映射

```xml
<!--多对一： 级联方式映射-->
    <resultMap id="empDeptMap" type="Emp">
        <id column="emp_id" property="empId"></id>
        <result column="emp_name" property="empName"/>
        <result column="age" property="age"/>
        <result column="gender" property="gender"/>
        <result column="dept_id" property="deptId"/>
        <result column="dept_id" property="dept.deptId"/>
        <result column="dept_name" property="dept.deptName"/>
    </resultMap>
    
    <select id="getEmpDeptById" parameterType="int" resultMap="empDeptMap">
        select emp.*,dept.*
        from t_emp emp
            left join t_dept dept
                on emp.dept_id = dept.dept_id
        where emp.emp_id = 1
    </select>
```

###### 2、使用association处理映射关系

```xml
<!--使用association处理映射关系-->
    <resultMap id="empDeptMap_ass" type="Emp">
        <id column="emp_id" property="empId"></id>
        <result column="emp_name" property="empName"/>
        <result column="age" property="age"/>
        <result column="gender" property="gender"/>
        <result column="dept_id" property="deptId"/>
        <!--
            property: 实体类属性
            javaType: 实体类型
         -->
        <association property="dept" javaType="Dept">
            <id column="dept_id" property="deptId"></id>
            <result column="dept_name" property="deptName"/>
        </association>
    </resultMap>
    <select id="getEmpDeptById_ass" parameterType="int" resultMap="empDeptMap">
        select emp.*,dept.*
        from t_emp emp
        left join t_dept dept
        on emp.dept_id = dept.dept_id
        where emp.emp_id = 1
    </select>
```

###### 3、使用分布查询

1、先查询员工信息

```java
/**
     * 使用分步查询，先查询出员工信息
     * @param empId
     * @return
     */
    List<Emp> getEmpDeptMap_step(@Param("empId") int empId);
```

```xml
 <!--使用分步查询处理多对一-->
    <resultMap id="empDeptMap_step" type="Emp">
        <id column="emp_id" property="empId"></id>
        <result column="emp_name" property="empName"/>
        <result column="age" property="age"/>
        <result column="gender" property="gender"/>
        <result column="dept_id" property="deptId"/>
        <!--
            property: 实体类属性
            select: 设置分步查询，查询某个属性的值的sql的标识（namespace.sqlId）
            column：将sql以及查询结果中的某个字段设置为分步查询的条件
         -->
        <association property="dept" select="org.soft.mapper.DeptMapper.getDeptById" column="dept_id"/>
    </resultMap>

    <select id="getEmpDeptMap_step" resultMap="empDeptMap_step" parameterType="int">
        select * from t_emp where emp_id = #{empId}
    </select>
```

2、根据员工部门id查询部门信息

```java
Dept getDeptById(@Param("deptId") int deptId);
```

```xml
	<resultMap id="deptResultMap" type="Dept">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
    </resultMap>
    
    <select id="getDeptById" resultMap="deptResultMap" parameterType="int">
        select * from t_dept where dept_id = #{deptId}
    </select>
```

