# MyBatis-plus

官网：[MyBatis-Plus (baomidou.com)](https://baomidou.com/)

代码生成器

```java
FastAutoGenerator.create("jdbc:mysql://127.0.0.1:3306/coriander-oa?characterEncoding=utf-8&userSSL=false", "root", "123456")
                .globalConfig(builder -> {
                    builder.author("姓陈的") // 设置作者
                            //.enableSwagger() // 开启 swagger 模式
                            .fileOverride() // 覆盖已生成文件
                            .outputDir("D://mybatis_plus_generator"); // 指定输出目录
                })
                .packageConfig(builder -> {
                    builder.parent("com.coriander") // 设置父包名
                            .moduleName("auth") // 设置父包模块名
                            .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "D://mybatis_plus_generator"))// 设置mapperXml生成路径
                            .entity("model");
                })
                .strategyConfig(builder -> {
                    builder.addInclude("sys_user").serviceBuilder().formatServiceFileName("%sService"); // 设置需要生成的表名
                            //.addTablePrefix("t_", "c_"); // 设置过滤表前缀
                })
                .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker
                //引擎模板，默认的是Velocity引擎模板
                .execute();
    }
```

