title: spring boot开发脚手架
tags:
  - spring boot，java
categories: []
author: haven
date: 2019-02-14 16:37:00
---
玉柴中台上线后，为了减少培训和推广中台技术架构，本人特意为简化spring boot的开发，研发了一个开发脚手架，下面是简单的使用手册。

## 快速开始

### 添加框架依赖

``` 
<dependency>
	<groupId>com.yuchai</groupId>
	<artifactId>yuchai-framework-starter-common</artifactId>
	<version>2.1.2.RELEASE</version>
</dependency>

<dependency>
	<groupId>com.yuchai</groupId>
	<artifactId>yuchai-framework-starter-mybatisplus</artifactId>
	<version>2.1.2.RELEASE</version>
</dependency>
```

这两个框架的简单介绍：1、common包是通用是一下功能组件，包括：通一异常处理，日志，swagger接口文档集成，附件上传，jwt鉴权，excel导入导出，@RequestAttribute("currentUser")获取当前登录人，@UserLoginToken注解接口需要登录的访问控制，集成企业微信
2、mybatisplus包是集成了mybatis的增强功能，主要是集成mybatis，接口的方式生成代码
#### 配置玉柴maven私有仓库
由于框架依赖没有发布到maven中央仓库，所以必须配置玉柴私有仓库,maven才能下载jar包成功
```
<repositories>
        <!--  配置nexus远程仓库 -->
        <repository>
            <id>nexus</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://172.16.90.84:8081/repository/maven-public/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

### yml配置数据源
数据源配置支持动态数据源、多数据源配置，如果你使用的是mysql，则mysql主从的配置如下
``` 
spring:
  datasource:
    dynamic:
      primary: master #设置默认的数据源或者数据源组,默认值即为master
      datasource:
        master:
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
          url: jdbc:mysql://xx.xx.xx.xx:3306/dynamic
        slave_1:
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
          url: jdbc:mysql://xx.xx.xx.xx:3307/dynamic
        slave_2:
          username: root
          password: 123456
          driver-class-name: com.mysql.jdbc.Driver
          url: jdbc:mysql://xx.xx.xx.xx:3308/dynamic
       #......省略
       #以上会配置一个默认库master，一个组slave下有两个子库slave_1,slave_2
```
如果你使用的是oracle数据库，则配置如下：
```
spring:
  datasource:
    dynamic:
      primary: oracle
      datasource:
         oracle:
          username: SRM
          password: SRM
          url: jdbc:oracle:thin:@172.16.135.41:1521/ndb2
          driver-class-name: oracle.jdbc.driver.OracleDriver
          output-dir: D://workingSpace//idea//demo2//src//main//java
          base-package: com.example.demo
```
同时还支持mysql和oracle混合配置。
更多请参考官方文档: [Server](http://blog.mybatis.org/)

### 配置mybatis的mapper包扫描路径
为了使mybatis配置生效，必须在启动类配置mybatis的mapper扫描路径@MapperScan("com.example.*.mapper*")
``` 
@SpringBootApplication
@MapperScan("com.example.*.mapper*")
public class YuchaiDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(YuchaiDemoApplication.class, args);
    }

}
```



### 运行项目
配置好后即可运行项目，访问http://localhost:8080/swagger-ui.html地址进行mybatis自动代码生成。

{% asset_img pasted-0.png 这是一个新的博客的图片的说明 %}
根据你的数据源类型选择mysql还是oracle接口
执行mybatis自动生成接口，输入参数是数据库表名称数组：例如 tb_role,tb_user


### 测试
进行controller的业务接口开发，并登陆http://localhost:8080/swagger-ui.html进行测试