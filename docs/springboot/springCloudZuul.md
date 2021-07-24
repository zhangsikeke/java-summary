zuul 本质就是一个基于springboot的应用，内置大量过滤器来实现网关的各种功能

参考：https://blog.csdn.net/maiyikai/article/details/79396732

#### 默认的核心过滤器一览表

| 过滤器                  | order | 描述                                                         | 类型  |
| ----------------------- | ----- | ------------------------------------------------------------ | ----- |
| ServletDetectionFilter  | -3    | 检测请求是用 DispatcherServlet还是 ZuulServlet               | pre   |
| Servlet30WrapperFilter  | -2    | 在Servlet 3.0 下，包装 requests                              | pre   |
| FormBodyWrapperFilter   | -1    | 解析表单数据                                                 | pre   |
| SendErrorFilter         | 0     | 如果中途出现错误                                             | error |
| DebugFilter             | 1     | 设置请求过程是否开启debug                                    | pre   |
| PreDecorationFilter     | 5     | 根据uri决定调用哪一个route过滤器                             | pre   |
| RibbonRoutingFilter     | 10    | 如果写配置的时候用ServiceId则用这个route过滤器，该过滤器可以用Ribbon 做负载均衡，用hystrix做熔断 | route |
| SimpleHostRoutingFilter | 100   | 如果写配置的时候用url则用这个route过滤                       | route |
| SendForwardFilter       | 500   | 用RequestDispatcher请求转发                                  | route |
| SendResponseFilter      | 1000  | 用RequestDispatcher请求转发                                  | post  |

默认是使用了apache http client实现转发,zuul也支持底层更换okhttp

springCloud gateway使用的技术是 netty+webflux ,请求接口的代码见 NettyRoutingFilter.java，参考：https://www.cnblogs.com/hellohero55/p/12723451.html

参考文档：http://ifeve.com/%E3%80%8Aspring-cloud-netflix%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3%E3%80%8B%E8%B7%AF%E7%94%B1%E5%92%8C%E8%BF%87%E6%BB%A4%E5%99%A8-zuul/

例如  SimpleHostRoutingFilter.java

```java
@Override
public Object run() {
	RequestContext context = RequestContext.getCurrentContext();
	HttpServletRequest request = context.getRequest();
	MultiValueMap<String, String> headers = this.helper
			.buildZuulRequestHeaders(request);
	MultiValueMap<String, String> params = this.helper
			.buildZuulRequestQueryParams(request);
	String verb = getVerb(request);
	InputStream requestEntity = getRequestBody(request);
	if (request.getContentLength() < 0) {
		context.setChunkedRequestBody();
	}

	String uri = this.helper.buildZuulRequestURI(request);
	this.helper.addIgnoredHeaders();

	try {
		CloseableHttpResponse response = forward(this.httpClient, verb, uri, request,
				headers, params, requestEntity);
		setResponse(response);
	}
	catch (Exception ex) {
		throw new ZuulRuntimeException(ex);
	}
	return null;
}
```

SendResponseFilter.java



pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.demo</groupId>
	<artifactId>springboot-single-zuul</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.10.RELEASE</version>
		<relativePath />
	</parent>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
		<java.version>1.8</java.version>
	</properties>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Edgware.SR3</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-zuul</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

application.yml

```yml

#192.168.10.17:7001
mall-eureka-ip: 127.0.0.1:7001
#192.168.10.17
mall-zuul-ip: 127.0.0.1

server: 
  port: 7000
  uri-encoding :  UTF-8
spring: 
  application: 
    name: mall-zuul
eureka: 
  client: 
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://admin:admin@${mall-eureka-ip}/eureka/
  instance: 
     prefer-ip-address: true
     ip-address: ${mall-zuul-ip}
     instance-id: ${mall-zuul-ip}:${spring.application.name}:${server.port}:@project.version@
      
#开启https 
#server:
    #port: 8443
    #ssl: 
        #key-store: classpath:www.amsphere.cn.pfx
        #key-store-password: 08132bm4l908hey
        #keyStoreType: PKCS12
        #keyAlias: www.abc.cn  
#feign:
  #hystrix:
    #enabled: true
#ribbon: 
    #httpclient:
        #enabled: true
    #okhttp: 
        #enabled: false
#hystrix调用方法的超时时间，默认是1000毫秒,如果不调第一次启动会访问会报错，第二中方案是设置不超时hystrix.command.default.execution.timeout.enabled: false
#hystrix:
    #command:
        #default:
            #execution:
                #isolation:
                    #thread:
                        #timeoutInMilliseconds: 60000

zuul:
    #默认上传文件需要在api前加/zuul，直接更改为根路径
  servlet-path: / 
  host:
    connect-timeout-millis: 10000
    socket-timeout-millis: 60000
  routes: 
     host-url-1: 
       path: /test/**
       url: http://127.0.0.1:8080
       stripPrefix: false
```

main

```java
@EnableZuulProxy
@SpringCloudApplication
public class ZuulApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(ZuulApplication.class, args);
    }
}
```

