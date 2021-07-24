### 跨域过滤器配置

 浏览器将CORS请求分成两类：**简单请求（simple request）** 和 **非简单请求（not-so-simple request）**。

 只要同时满足以下两大条件，就属于简单请求。 

(1) 请求方法是以下三种方法之一：
HEAD
GET
POST

(2)HTTP的头信息不超出以下几种字段：
Accept
Accept-Language
Content-Language
Last-Event-ID
Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain

```java
/**
(1)Access-Control-Expose-Headers-指定浏览器CORS请求会额外发送的头信息字段
CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma。如果想拿到其他字段，就必须在Access-Control-Expose-Headers里面指定。上面的例子指定，getResponseHeader('FooBar')可以返回FooBar字段的值.

(2)Access-Control-Allow-Methods-允许请求的方法
该字段必需，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法。注意，返回的是所有支持的方法，而不单是浏览器请求的那个方法。这是为了避免多次"预检"请求。
 
(3)Access-Control-Allow-Headers-允许携带的头
如果浏览器请求包括Access-Control-Request-Headers字段，则Access-Control-Allow-Headers字段是必需的。它也是一个逗号分隔的字符串，表明服务器支持的所有头信息字段，不限于浏览器在"预检"中请求的字段。
 
(4)Access-Control-Allow-Credentials-表示是否允许发送Cookie
该字段与简单请求时的含义相同。
 
(5)Access-Control-Max-Age-预检请求的有效期
该字段可选，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是20天（1728000秒），即允许缓存该条回应1728000秒（即20天），在此期间，不用发出另一条预检请求。
*/
```



1. Access-Control-Expose-Headers
2. 该字段可选。。

```java
import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpServletRequest;

/**
 * 跨域配置<br>
 * 跨域资源共享 CORS 详解：http://www.ruanyifeng.com/blog/2016/04/cors.html<br/>
 * HTTP 中文开发手册：https://www.php.cn/manual/view/35587.html
 */
public class CrossFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain chiain)
        throws IOException, ServletException {

        HttpServletRequest request = (HttpServletRequest)servletRequest;
        HttpServletResponse response = (HttpServletResponse)servletResponse;

        // 允许跨域的地址*表示所有
        response.setHeader("Access-Control-Allow-Origin", "http://localhost:8848");
        // 表示是否允许发送Cookie
        response.setHeader("Access-Control-Allow-Credentials", "true");
        // 允许跨域的方法
        response.setHeader("Access-Control-Allow-Methods", "POST,GET,OPTIONS,DELETE");
        // 预检请求的有效期,OPTIONS请求就是想服务端进行探测支持的方法
        response.setHeader("Access-Control-Max-Age", "3600");
        // 跨域时允许在header中携带的字段
        response.setHeader("Access-Control-Allow-Headers",
            "Origin, X-Requested-With, Content-Type, Accept,Token,tokenzsk,tokenzsk1");
        // 检测时直接返回
        if ("OPTIONS".equals(request.getMethod())) {
            response.setStatus(HttpServletResponse.SC_NO_CONTENT);
            return;
        }
        chiain.doFilter(servletRequest, servletResponse);
    }
}

```

#### 注册过滤器

```java
@Bean
public FilterRegistrationBean<CrossFilter> corssFilterRegiste() {
    FilterRegistrationBean<CrossFilter> reFilter = new FilterRegistrationBean<>();
    // 创建并注册TestFilter
    reFilter.setFilter(new CrossFilter());
    // 拦截的路径（对所有请求拦截）
    reFilter.addUrlPatterns("/services/*");
    // 拦截器的名称
    reFilter.setName("CrossFilter");
    // 拦截器的执行顺序。数字越小越先执行
    reFilter.setOrder(1);
    return reFilter;
}
```

