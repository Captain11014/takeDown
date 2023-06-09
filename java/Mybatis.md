# Mybatis

#### 一、环境搭建

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

#### 二、核心配置文件

##### 2.1、依赖包

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

##### 2.2、MyBatis核心配置文件

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

##### 2.3、MyBatis映射文件

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

##### 2.4、加入log4j日志功能

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

#### 三、MyBatis获取参数的两种方式

MyBatis获取参数的两种方式：#{} 、${}

${}：本质是字符串拼接。注意该方法会引起SQL注入，若为字符串类型或日期类型的字段进行赋值时，需要手动加单号；

#{}：占位符赋值（推荐使用）。

##### 3.1、单个字面量参数

若mapper接口中的方法参数为单个的字面量类型

此时可以使用${}和#{}以任意的名称获取参数的值，注意${}需要手动加单引号

##### 3.2、多个字面量类型参数的参数

若mapper接口中的方法参数为多个时 

此时MyBatis会自动将这些参数放在一个map集合中，

以arg0,arg1...为键，以参数为值；

以 param1,param2...为键，以参数为值；

因此只需要通过${}和#{}访问map集合的键就可以获取相 对应的 值，注意${}需要手动加单引号

##### 3.3、Map集合类型为参数

若mapper接口中的方法需要多个参数时，可手动创建map集合，将数据放在map集合中。

通过map集合中的键访问参数值。（#{}。${}需要手动添加单引号）。

##### 3.4、实体类型的参数

mapper接口方法中的参数为实体类型，可以通过实体类属性名获取值（#{} 。 ${}需要手动添加单引号）

##### 3.5、使用@Param注解

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

#### 四、特殊SQL执行

##### 4.1、模糊查询

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

##### 4.2、获取主键

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

#### 五、resultMap标签

##### 5.1、resultMap处理字段和属性的映射关系

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

##### 5.2、多对一处理映射

###### （1）级联方式处理映射

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

###### （2）使用association处理映射关系

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

###### （3）使用分布查询

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

##### 5.3、一对多映射处理

###### （1）使用collection处理一对多映射

```java
	/**
     * 使用collection处理一对多映射
     * @param deptId
     * @return
     */
    Dept getDeptAndEmpsById(@Param("deptId") int deptId);
```

```xml
 <!--使用collection处理一对多映射-->
    <resultMap id="deptAndEmpsResultMap" type="Dept">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
        <!--
            ofType：设置collection标签所处理的集合属性中存储数据的类型
        -->
        <collection property="emps" ofType="Emp">
            <id column="emp_id" property="empId"></id>
            <result column="emp_name" property="empName"/>
            <result column="age" property="age"/>
            <result column="gender" property="gender"/>
            <result column="dept_id" property="deptId"/>
        </collection>
    </resultMap>
    <select id="getDeptAndEmpsById" resultMap="deptAndEmpsResultMap" parameterType="int">
        select * from t_dept d left join t_emp e on d.dept_id = e.dept_id  where d.dept_id = #{deptId}
    </select>
```

###### （2）使用分步查询处理一对多

查询部门信息

```java
/**
     * 使用分步查询处理一对多映射关系，查询部门信息
     * @param deptId
     * @return
     */
    Dept getDeptAndEmpById(@Param("deptId") int deptId);
```

```xml
<!--分步查询处理一对多映射-->
    <resultMap id="deptAndEmpsMap" type="Dept">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
        <collection property="emps" select="org.soft.mapper.EmpMapper.getEmps" column="dept_id"/>
    </resultMap>

    <select id="getDeptAndEmpById" resultMap="deptAndEmpsMap" parameterType="int">
        select * from t_dept where dept_id = #{deptId}
    </select>
```

查询部门对应的员工信息

```java
/**
     * 使用分步查询处理一对多映射关系。查询部门员工信息
     * @param deptId
     * @return
     */
    List<Emp> getEmps(@Param("deptId") int deptId);
```

```xml
 <!--使用分步查询处理一对多映射关系。查询部门员工信息-->
    <resultMap id="empsResultMap"  type="Emp">
        <id column="emp_id" property="empId"></id>
        <result column="emp_name" property="empName"/>
        <result column="age" property="age"/>
        <result column="gender" property="gender"/>
        <result column="dept_id" property="deptId"/>
    </resultMap>
    <select id="getEmps" parameterType="int" resultMap="empsResultMap">
        select * from t_emp where dept_id = #{deptId}
    </select>
```

分步查询的优点：可以实现延迟加载 但是必须在核心配置文件中设置全局配置信息： lazyLoadingEnabled：延迟加载的全局开关。当开启时，所有关联对象都会延迟加载 aggressiveLazyLoading：当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属 性会按需加载 此时就可以实现按需加载，获取的数据是什么，就只会执行相应的sql。此时可通过association和 collection中的fetchType属性设置当前的分步查询是否使用延迟加载， fetchType="lazy(延迟加 载)|eager(立即加载)"

#### 六、动态SQL

```xml
 <!--
        动态SQL：
        1、if，通过test属性中的表达式判断标签中的内容是否有效（是否会拼接到sql中）
        2、where
        a.若where标签中有条件成立，会自动生成where关键字
        b.会自动将where标签中内容前多余的and去掉，但是其中内容后多余的and无法去掉
        c.若where标签中没有任何一个条件成立，则where没有任何功能
        3、trim
        prefix、suffix：在标签中内容前面或后面添加指定内容
        prefixOverrides、suffixOverrides：在标签中内容前面或后面去掉指定内容
        4、choose、when、otherwise
        相当于java中的if...else if...else
        when至少设置一个，otherwise最多设置一个
        5、foreach
        collection：设置要循环的数组或集合
        item：用一个字符串表示数组或集合中的每一个数据
        separator：设置每次循环的数据之间的分隔符
        open：循环的所有内容以什么开始
        close：循环的所有内容以什么结束
        6、sql片段
        可以记录一段sql，在需要用的地方使用include标签进行引用
        <sql id="empColumns">
            emp_id,emp_name,age,gender,dept_id
        </sql>
        <include refid="empColumns"></include>
    -->

    <sql id="empColumns">
        emp_id,emp_name,age,gender,dept_id
    </sql>

    <!--List<Emp> getEmpByCondition(Emp emp);-->
    <select id="getEmpByCondition" resultType="Emp">
        select <include refid="empColumns"></include> from t_emp
        <trim prefix="where" suffixOverrides="and">
            <if test="empName != null and empName != ''">
                emp_name = #{empName} and
            </if>
            <if test="age != null and age != ''">
                age = #{age} and
            </if>
            <if test="gender != null and gender != ''">
                gender = #{gender}
            </if>
        </trim>
    </select>
    <select id="getEmpByConditionTwo" resultType="Emp">
        select * from t_emp
        <where>
            <if test="empName != null and empName != ''">
                emp_name = #{empName}
            </if>
            <if test="age != null and age != ''">
                and age = #{age}
            </if>
            <if test="gender != null and gender != ''">
                and gender = #{gender}
            </if>
        </where>
    </select>
    <select id="getEmpByConditionOne" resultType="Emp">
        select * from t_emp where 1=1
        <if test="empName != null and empName != ''">
            and emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="gender != null and gender != ''">
            and gender = #{gender}
        </if>
    </select>

    <!--List<Emp> getEmpByChoose(Emp emp);-->
    <select id="getEmpByChoose" resultType="Emp">
        select * from t_emp
        <where>
            <choose>
                <when test="empName != null and empName != ''">
                    emp_name = #{empName}
                </when>
                <when test="age != null and age != ''">
                    age = #{age}
                </when>
                <when test="gender != null and gender != ''">
                    gender = #{gender}
                </when>
            </choose>
        </where>
    </select>

    <!--void insertMoreEmp(@Param("emps") List<Emp> emps);-->
    <insert id="insertMoreEmp">
        insert into t_emp values
        <foreach collection="emps" item="emp" separator=",">
            (null,#{emp.empName},#{emp.age},#{emp.gender},null)
        </foreach>
    </insert>

    <!--void deleteMoreEmp(@Param("empIds") Integer[] empIds);
		如果没有使用@Param注解标明参数名，则collection中填写按照mybatis map的键
	-->
    <delete id="deleteMoreEmp">
        <!--delete from t_emp where emp_id in
        <foreach collection="empIds" item="empId" separator="," open="(" close=")">
            #{empId}
        </foreach>-->
        delete from t_emp where
        <foreach collection="empIds" item="empId" separator="or">
            emp_id = #{empId}
        </foreach>
    </delete>
```

#### 七、MyBatis的缓存

##### 7.1、MyBatis的一级缓存

一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就 会从缓存中直接获取，不会从数据库重新访问

使一级缓存失效的四种情况：

1) 不同的SqlSession对应不同的一级缓存

2) 同一个SqlSession但是查询条件不同

3) 同一个SqlSession两次查询期间执行了任何一次增删改操作 
4) 同一个SqlSession两次查询期间手动清空了缓存

##### 7.2、MyBatis的二级缓存

二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被 缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取

二级缓存开启的条件：

a>在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置 

b>在映射文件中设置标签 

c>二级缓存必须在SqlSession关闭或提交之后有效 

d>查询的数据所转换的实体类类型必须实现序列化的接口

**使二级缓存失效的情况：** 两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

##### 7.3、二级缓存的相关配置

在mapper配置文件中添加的cache标签可以设置一些属性： 

①eviction属性：缓存回收策略，默认的是 LRU。 

LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。 

FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。 

SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。

WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。 

②flushInterval属性：刷新间隔，单位毫秒 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新 

③size属性：引用数目，正整数 代表缓存最多可以存储多少个对象，太大容易导致内存溢出 

④readOnly属性：只读， true/false 

true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了 很重 要的性能优势。 

false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是 false。

##### 7.4、MyBatis缓存查询的顺序

先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。 

如果二级缓存没有命中，再查询一级缓存 

如果一级缓存也没有命中，则查询数据库 

SqlSession关闭之后，一级缓存中的数据会写入二级缓存

#### 八、MyBatis逆向工程

正向工程：先创建Java实体类，由框架负责根据实体类生成数据库表。 Hibernate是支持正向工 程的。 

逆向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源： 

Java实体类 

Mapper接口 

Mapper映射文件

##### 8.1创建逆向工程的步骤

###### （1）添加依赖

```xml
	<dependencies>
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
        <!-- log4j日志 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
    </dependencies>
    <!-- 控制Maven在构建过程中相关配置 -->
    <build>
    <!-- 构建过程中用到的插件 -->
        <plugins>
            <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.0</version>
                <!-- 插件的依赖 -->
                <dependencies>
                    <!-- 逆向工程的核心依赖 -->
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.2</version>
                    </dependency>
                    <!-- MySQL驱动 -->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.16</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
```

###### （2）创建MyBatis核心配置文件

###### （3）创建逆向工程的配置文件

文件名必须是：generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
"http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
<!--
targetRuntime: 执行生成的逆向工程的版本
MyBatis3Simple: 生成基本的CRUD（清新简洁版）
MyBatis3: 生成带条件的CRUD（奢华尊享版）
-->
    <context id="DB2Tables" targetRuntime="MyBatis3">
    <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
            connectionURL="jdbc:mysql://localhost:3306/mybatis?
            serverTimezone=UTC"
            userId="root"
            password="123456">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.atguigu.mybatis.pojo"
            targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.atguigu.mybatis.mapper"
            targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER"
            targetPackage="com.atguigu.mybatis.mapper" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
```

###### （4）执行MBG插件的generate目标

点击：maven -->Plugins -->mybatis-generator-->mybatis-generator:generate

##### 8.2、QBC查询

```java
@Test
public void testMBG(){
    try {
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new
        SqlSessionFactoryBuilder().build(is);
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        //查询所有数据
        /*List<Emp> list = mapper.selectByExample(null);
        list.forEach(emp -> System.out.println(emp));*/
        //根据条件查询
        /*EmpExample example = new EmpExample();
        example.createCriteria().andEmpNameEqualTo("张
        三").andAgeGreaterThanOrEqualTo(20);
        example.or().andDidIsNotNull();
        List<Emp> list = mapper.selectByExample(example);
        list.forEach(emp -> System.out.println(emp));*/
        mapper.updateByPrimaryKeySelective(new
        Emp(1,"admin",22,null,"456@qq.com",3));
    } catch (IOException e) {
    	e.printStackTrace();
    }
}

```

#### 九、分页插件

limit index,pageSize 

pageSize：每页显示的条数 

pageNum：当前页的页码 

index：当前页的起始索引，index=(pageNum-1)*pageSize 

count：总记录数 

totalPage：总页数 

totalPage = count / pageSize; 

if(count % pageSize != 0){

 	totalPage += 1; 

} 

pageSize=4，pageNum=1，index=0 limit 0,4

 pageSize=4，pageNum=3，index=8 limit 8,4 

pageSize=4，pageNum=6，index=20 limit 8,4

##### 9.1分页插件的使用步骤

###### （1）添加依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
```

###### （2）配置分页插件

在MyBatis核心配置文件中配置插件

```xml
<plugins>
    <!--设置分页插件-->
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

##### 9.2、分页插件的使用步骤

a>在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能

pageNum：当前页的页码 

pageSize：每页显示的条数

b>在查询获取list集合之后，使用PageInfo pageInfo = new PageInfo<>(List list, int navigatePages)获取分页相关数据

list：分页之后的数据 

navigatePages：导航分页的页码数

c>分页相关数据

PageInfo{ 

pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, pages=8, 

list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, total=30, pages=8, reasonable=false, pageSizeZero=false}, 

prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, hasPreviousPage=true, 

hasNextPage=false, navigatePages=5, navigateFirstPage4, navigateLastPage8, 

navigatepageNums=[4, 5, 6, 7, 8] }

pageNum：当前页的页码 

pageSize：每页显示的条数 

size：当前页显示的真实条数 

total：总记录数 

pages：总页数 

prePage：上一页的页码 

nextPage：下一页的页码 

isFirstPage/isLastPage：是否为第一页/最后一页 

hasPreviousPage/hasNextPage：是否存在上一页/下一页 

navigatePages：导航分页的页码数 

navigatepageNums：导航分页的页码，[1,2,3,4,5]
