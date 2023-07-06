# 1，maven 官方仓库查找

## 1.1 复制maven 的坐标 

```
<dependency>
  	<groupId>com.github.ducheng</groupId>
	<artifactId>hot-deployment-spring-cloud-starter</artifactId>
	<version>0.0.2</version>
</dependency>
```



# 1.2 在springboot 的resource的目录

bootstrap.yaml 的文件

```yaml
spring:
  cloud:
    hot：
      deployment：
        dataId : xxxxxxxx //你的nacos 关于日志脱敏的文件dataId
    nacos:
      discovery:
        server-addr: localhost:8848
      config:
        server-addr: localhost:8848
        namespace: 80d2b616-2c31-409a-855c-1a6b02ee11a6
        file-extension: yaml
        refresh-enabled: true
        extensionConfigs[0]:
          dataId: xxxxxxxx
          refresh: true
```

在编写base64 的encode方法 对编译后的class 文件进行encode

下面就是愉快的使用了。详细教程和原理请参考 [juejin](https://juejin.cn/post/7215790043050246199?share_token=42a3c6d5-8d35-481b-8303-73b18db68474)

