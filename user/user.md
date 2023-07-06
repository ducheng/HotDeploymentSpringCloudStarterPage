# 1，maven 官方仓库查找

## 1.1 复制maven 的坐标 

```
<dependency>
  <groupId>com.github.ducheng</groupId>
  <artifactId>dynamic-log-desensitize-spring-cloud-starter</artifactId>
  <version>0.0.3</version>
</dependency>
```



# 1.2 在springboot 的resource的目录

bootstrap.yaml 的文件

```yaml
spring:
  cloud:
   dynamic: 
     log: 
       dataId : xxxxxxxx //你的nacos 关于日志脱敏的文件dataId
    nacos:
      discovery:
        server-addr: localhost:8848
      config:
        server-addr: localhost:8848
        namespace: 80d2b616-2c31-409a-855c-1a6b02ee11a6
        file-extension: yaml
        refresh-enabled: true
```

在编写logback 的xml 文件

```xml
<configuration>

    //默认控制台打印
    <appender name="STDOUT" class="com.ducheng.dynamic.log.desensitize.advice.CustomerConsoleAppender">
        <!-- encoders are assigned the type
             ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>


    <!-- Strictly speaking, the level attribute is not necessary since -->
    <!-- the level of the root level is set to DEBUG by default.       -->
    <root level="INFO">
        <appender-ref ref="STDOUT" />
    </root>

</configuration>
```

logback的自定义的实现类， 现在支持三种模式

1，com.ducheng.dynamic.log.desensitize.advice.CustomerConsoleAppender 控制台适配器

2， com.ducheng.dynamic.log.desensitize.advice.CustomerFileAppender 文件适配器

3，com.ducheng.dynamic.log.desensitize.advice.CustomerRollingFileAppender 滚动文件适配器

# 1.3 在nacos 写你的配置文件

```yaml
# 日志脱敏
log-desensitize:
  # 是否忽略大小写匹配，默认为true
  ignore: true
  # 是否开启脱敏，默认为false
  open: true
  # pattern下的key/value为固定脱敏规则
  pattern:
    # 邮箱 - @前第4-7位脱敏
    email: "@>(4,7)"
    # qq邮箱 - @后1-3位脱敏
    qqemail: "@<(1,3)"
    # 姓名 - 姓脱敏，如*杰伦
    name: 1,1
    # 密码 - 所有需要完全脱敏的都可以使用内置的password
    password: password
  patterns:
    # 身份证号，key后面的字段都可以匹配以下规则(用逗号分隔)
    - key: identity,card_no,cardNo
      # 定义规则的标识
      custom:
        # defaultRegex表示使用组件内置的规则：identity表示身份证号 - 内置的18/15位
        - defaultRegex: identity
          position: 9,13
        # 内置的other表示如果其他规则都无法匹配到，则按该规则处理
        - defaultRegex: other
          position: 9,10
    # 电话号码，key后面的字段都可以匹配以下规则(用逗号分隔)
    - key: phone,cellphone,mobile
      custom:
        # 手机号 - 内置的11位手机匹配规则
        - defaultRegex: phone
          position: 4,7
        # 自定义正则匹配表达式：座机号(带区号，号码七位|八位)
        - customRegex: "^0[0-9]{2,3}-[0-9]{7,8}"
        # -后面的1-4位脱敏
          position: "-<(1,4)"
        # 自定义正则匹配表达式：座机号(不带区号)
        - customRegex: "^[0-9]{7,8}"
          position: 3,5
        # 内置的other表示如果其他规则都无法匹配到，则按该规则处理
        - defaultRegex: other
          position: 1,3
    # 这种方式不太推荐 - 一旦匹配不上，就不会脱敏
    - key: localMobile
      custom:
          customRegex: "^0[0-9]{2,3}-[0-9]{7,8}"
          position: 1,3
```

下面就是愉快的使用了。详细教程和原理请参考 [掘金](https://juejin.cn/post/7209547043936845883)

