## 设置前后台交互时间的格式

@DatetimeFormat是将String转换成Date，前台给后台传值时用

@JsonFormat(pattern="yyyy-MM-dd")  将Date转换成String  后台传值给前台用

```java
/**
 * 创建时间
 */
@DateTimeFormat(pattern = "yyyy/MM/dd HH:mm:ss")
private Date createTime;
```

## 配置拦截器

新建拦截器继承HandlerInterceptor接口，实现三个方法

```java
package com.hozuo.xdw.common.intercepor;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import lombok.extern.slf4j.Slf4j;

import java.io.IOException;

@Slf4j
@Component
public class LoginInterceptorAdmin implements HandlerInterceptor {

    // 这个方法是在访问接口之前执行的，我们只需要在这里写验证登陆状态的业务逻辑，就可以在用户调用指定接口之前验证登陆状态了
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws ServletException, IOException {
        final String method = request.getMethod();
        final String requestURI = request.getRequestURI();
        log.debug("method:{}", method);
        log.debug("requestURI:{}", requestURI);
        final String[] loginUsernames = request.getParameterValues("loginUsername");
        if (loginUsernames.length != 1) {
            return false;
        }
        final String loginUsername = loginUsernames[0];
        final String adminStr = "admin";
        final boolean equals = adminStr.equals(loginUsername);
        if (!equals){
            // 请求转发
            request.getRequestDispatcher("/api/error/notAdmin").forward(request, response);
            return false;
        }
        return true;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable ModelAndView modelAndView) {}

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable Exception ex) {}
}
```

在配置文件中注册，addInterceptor表示拦截路径，addPathPatterns表示排除路径

```java
package com.hozuo.xdw.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import com.hozuo.xdw.common.intercepor.LoginInterceptorAdmin;

@Configuration
public class WebConfigurer implements WebMvcConfigurer {

    private LoginInterceptorAdmin loginInterceptorAdmin;

    @Autowired
    public void setLoginInterceptorAdmin(LoginInterceptorAdmin loginInterceptorAdmin) {
        this.loginInterceptorAdmin = loginInterceptorAdmin;
    }

    // 这个方法是用来配置静态资源的，比如html，js，css，等等
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {}

    // 这个方法用来注册拦截器，我们自己写好的拦截器需要通过这里添加注册才能生效
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        /*registry.addInterceptor(loginInterceptorAdmin).addPathPatterns("/**").excludePathPatterns("/api/user/login",
            "/register", "/api/error/*");*/
        registry.addInterceptor(loginInterceptorAdmin).addPathPatterns("/api/user/register");
    }
}
```