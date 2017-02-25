---
title: HandlerInterceptorAdapter
tags:
- spring-mvc
- java
categories: [Technology]
---

# prelude
As we all know, interceptor in spring-mvc is very useful to address various needs,
one need is How to mark the request log more compact.

In our project the request log will record all the html page which made it very large
and after about two years running, the server space is about to be filled up.

One solution is to buy greater capacity of server space, but it is not the best way.
though the whole html file is large, but the useful infomation density is low, actually,
the ultimate aim of us is to record the ModelAndView instance, not the whole output page.
so I use HandlerInterceptorAdapter to get ModelAndView instance;

`org.springframework.web.servlet.handler.HandlerInterceptorAdapter` has some functions to help developer to control the flow and data of web service. one of them is `void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)` which will be called between a ModelAndView instance builded completed by controller layer and be rendered by view layer. so you can do something at an architectural level in this process, which is we need.

# configuration
this is the Spring-mvc interceptor configuration
```<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/*" />
      <!-- request of static resource don`t has ModelAndView instance  -->
			<mvc:exclude-mapping path="/static/*" />
			<bean
					class="*.LoggingResponse" />
		</mvc:interceptor>
	</mvc:interceptors>
```

we get the ModelAndView instance in spring-mvc interceptor, and record it in filter,
out of system maintainability


# code

## postHandle
```
public class LoggingResponse extends HandlerInterceptorAdapter {
    //to save and transmitting the ModelAndView instance
    public static ThreadLocal<String> threadLocal = new ThreadLocal<>();

    @Override
    public void postHandle(HttpServletRequest request,
                           HttpServletResponse response,
                           Object handler, ModelAndView modelAndView) throws Exception {

        if (modelAndView != null) {
            threadLocal.set(modelAndView.toString());
        }

    }
}
```

```
private void logResponse(final ResponseWrapper response) {
        StringBuilder msg = new StringBuilder();
        msg.append(RESPONSE_PREFIX);
        msg.append("request id=").append(response.getId());

        String modelAndView = threadLocal.get();
        if (null != modelAndView) {
            msg.append(";").append(modelAndView);
        } else {
            msg.append(";modelAndView=null");
        }
        logger.info(msg.toString());
        //destroyed the threadLocal manually to prevent memory leak
        threadLocal.remove();

    }
    ```

# more
by the way, I list the other functions of HandlerInterceptorAdapter to
facilitate you.

## preHandle
```
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler)
                  throws Exception
                  ```
true if the execution chain should proceed with the next interceptor or the handler itself. Else, DispatcherServlet assumes that this interceptor has already dealt with the response itself.

## afterCompletion
```
public void afterCompletion(HttpServletRequest request,
                            HttpServletResponse response,
                            Object handler,
                            Exception ex)
                     throws Exception
                     ```
this function will be called on the completion of the view, of course, the preHandle's return value muse be true.

## afterConcurrentHandlingStarted
```
public void afterConcurrentHandlingStarted(HttpServletRequest request,
                                           HttpServletResponse response,
                                           Object handler)
                                    throws Exception
                                    ```
this function will be called on the start of controller is being called in asynchronous state.
