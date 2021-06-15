---
title: Feign转发Header
updated: 2021-05-23 00:24:14
description: Feign的远程调用在默认情况下是不会转发Header的，如果需要转发Header，那么就得自定义Feign的隔离策略
tags:
- SpringBoot
# 置顶优先级 数值越大优先级越高 #
sticky: 9981
# 【可选】文章分类 #
categories: SpringBoot
# 【可选】文章关键字 #
keywords:
- Feign
date: 2019-10-21 18:55:32
---

### Feign的调用链

![](https://i.loli.net/2021/05/23/V7yRsFmtXlqW3zC.png)

> 实现RequestInterceptor接口

```java
package club.zby.newplan.Interceptor;

import feign.RequestTemplate;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;
import java.util.Enumeration;

/**
 * @Author: 赵博雅
 * @Date: 2019/10/11 18:03
 */
@Configuration
public class FeignInterceptor implements feign.RequestInterceptor {

    private final Logger logger = LoggerFactory.getLogger(getClass());

    @Override
    public void apply(RequestTemplate template) {
        System.out.println("*****************");
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder
                .getRequestAttributes();
        System.out.println("Feign远程服务调用的请求转发（重写请求模板，转发到B服务）");
        HttpServletRequest request = attributes.getRequest();

        System.out.println((String) request.getAttribute("userid"));

        Enumeration<String> headerNames = request.getHeaderNames();

        System.out.println("获取A请求头");
        if (headerNames != null) {
            System.out.println("遍历Authrorization");
            while (headerNames.hasMoreElements()) {
                String name = headerNames.nextElement();
                String values = request.getHeader(name);
                template.header(name, values);
                System.out.println(name +"-----"+  values);
            }
            logger.info("feign interceptor header:{}",template);
        }
        System.out.println(3);

    }
}
```

> 自定义Hystrix的隔离策略（不能使用SEMAPHORE规则）

```java
package club.zby.newplan.Interceptor;

import com.netflix.hystrix.HystrixThreadPoolKey;
import com.netflix.hystrix.HystrixThreadPoolProperties;
import com.netflix.hystrix.strategy.HystrixPlugins;
import com.netflix.hystrix.strategy.concurrency.HystrixConcurrencyStrategy;
import com.netflix.hystrix.strategy.concurrency.HystrixRequestVariable;
import com.netflix.hystrix.strategy.concurrency.HystrixRequestVariableLifecycle;
import com.netflix.hystrix.strategy.eventnotifier.HystrixEventNotifier;
import com.netflix.hystrix.strategy.executionhook.HystrixCommandExecutionHook;
import com.netflix.hystrix.strategy.metrics.HystrixMetricsPublisher;
import com.netflix.hystrix.strategy.properties.HystrixPropertiesStrategy;
import com.netflix.hystrix.strategy.properties.HystrixProperty;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.Callable;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * 自定义Feign的隔离策略;
 * 在转发Feign的请求头的时候，如果开启了Hystrix，Hystrix的默认隔离策略是Thread(线程隔离策略)，
 * 因此转发拦截器内是无法获取到请求的请求头信息的，
 * 可以修改默认隔离策略为信号量模式：hystrix.command.default.execution.isolation.strategy=SEMAPHORE，
 * 这样的话转发线程和请求线程实际上是一个线程，
 * 这并不是最好的解决方法，信号量模式也不是官方最为推荐的隔离策略；
 * 另一个解决方法就是自定义Hystrix的隔离策略，
 * 思路是将现有的并发策略作为新并发策略的成员变量,
 * 在新并发策略中，返回现有并发策略的线程池、Queue；将策略加到Spring容器即可；
 * @Author: 赵博雅
 * @Date: 2019/10/21 17:02
 */

@Component
public class FeignHystrixConcurrencyStrategyIntellif extends HystrixConcurrencyStrategy {

    private static final Logger log = LoggerFactory.getLogger(FeignHystrixConcurrencyStrategyIntellif.class);
    private HystrixConcurrencyStrategy delegate;

    public FeignHystrixConcurrencyStrategyIntellif() {
        try {
            this.delegate = HystrixPlugins.getInstance().getConcurrencyStrategy();
            if (this.delegate instanceof FeignHystrixConcurrencyStrategyIntellif) {
                // Welcome to singleton hell...
                return;
            }
            HystrixCommandExecutionHook commandExecutionHook =
                    HystrixPlugins.getInstance().getCommandExecutionHook();
            HystrixEventNotifier eventNotifier = HystrixPlugins.getInstance().getEventNotifier();
            HystrixMetricsPublisher metricsPublisher = HystrixPlugins.getInstance().getMetricsPublisher();
            HystrixPropertiesStrategy propertiesStrategy =
                    HystrixPlugins.getInstance().getPropertiesStrategy();
            this.logCurrentStateOfHystrixPlugins(eventNotifier, metricsPublisher, propertiesStrategy);
            HystrixPlugins.reset();
            HystrixPlugins.getInstance().registerConcurrencyStrategy(this);
            HystrixPlugins.getInstance().registerCommandExecutionHook(commandExecutionHook);
            HystrixPlugins.getInstance().registerEventNotifier(eventNotifier);
            HystrixPlugins.getInstance().registerMetricsPublisher(metricsPublisher);
            HystrixPlugins.getInstance().registerPropertiesStrategy(propertiesStrategy);
        } catch (Exception e) {
            log.error("Failed to register Sleuth Hystrix Concurrency Strategy", e);
        }
    }

    private void logCurrentStateOfHystrixPlugins(HystrixEventNotifier eventNotifier,
                                                 HystrixMetricsPublisher metricsPublisher, HystrixPropertiesStrategy propertiesStrategy) {
        if (log.isDebugEnabled()) {
            log.debug("Current Hystrix plugins configuration is [" + "concurrencyStrategy ["
                    + this.delegate + "]," + "eventNotifier [" + eventNotifier + "]," + "metricPublisher ["
                    + metricsPublisher + "]," + "propertiesStrategy [" + propertiesStrategy + "]," + "]");
            log.debug("Registering Sleuth Hystrix Concurrency Strategy.");
        }
    }

    @Override
    public <T> Callable<T> wrapCallable(Callable<T> callable) {
        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        return new WrappedCallable<>(callable, requestAttributes);
    }

    @Override
    public ThreadPoolExecutor getThreadPool(HystrixThreadPoolKey threadPoolKey,
                                            HystrixProperty<Integer> corePoolSize, HystrixProperty<Integer> maximumPoolSize,
                                            HystrixProperty<Integer> keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
        return this.delegate.getThreadPool(threadPoolKey, corePoolSize, maximumPoolSize, keepAliveTime,
                unit, workQueue);
    }

    @Override
    public ThreadPoolExecutor getThreadPool(HystrixThreadPoolKey threadPoolKey,
                                            HystrixThreadPoolProperties threadPoolProperties) {
        return this.delegate.getThreadPool(threadPoolKey, threadPoolProperties);
    }

    @Override
    public BlockingQueue<Runnable> getBlockingQueue(int maxQueueSize) {
        return this.delegate.getBlockingQueue(maxQueueSize);
    }

    @Override
    public <T> HystrixRequestVariable<T> getRequestVariable(HystrixRequestVariableLifecycle<T> rv) {
        return this.delegate.getRequestVariable(rv);
    }

    static class WrappedCallable<T> implements Callable<T> {
        private final Callable<T> target;
        private final RequestAttributes requestAttributes;

        public WrappedCallable(Callable<T> target, RequestAttributes requestAttributes) {
            this.target = target;
            this.requestAttributes = requestAttributes;
        }

        @Override
        public T call() throws Exception {
            try {
                RequestContextHolder.setRequestAttributes(requestAttributes);
                return target.call();
            } finally {
                RequestContextHolder.resetRequestAttributes();
            }
        }
    }
}
```