MDC 全称是 Mapped Diagnostic Context，==可以粗略的理解成是一个线程安全的存放诊断日志的容器。==

==可以用于格式化日志的输出==

``` java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;
import java.util.UUID;

/**
 * MDC快速入门示例
 *
 * @author 一猿小讲
 */
public class SimpleMDC {

    private static final Logger logger = LoggerFactory.getLogger(SimpleMDC.class);
    public static final String REQ_ID = "REQ_ID";

    public static void main(String[] args) {
        MDC.put(REQ_ID, UUID.randomUUID().toString());
        logger.info("开始调用服务A，进行业务处理");
        logger.info("业务处理完毕，可以释放空间了，避免内存泄露");
        MDC.remove(REQ_ID);
        logger.info("REQ_ID 还有吗？{}", MDC.get(REQ_ID) != null);
    }
}
```

接下来配置 logback.xml

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>[%t] [%X{REQ_ID}] - %m%n</Pattern>
        </layout>
    </appender>
    <root level="debug">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```

结果是
![[Pasted image 20250528195337.png]]

在多线程下，这些放入MDC的变量互相使用threadLocal隔离
![[Pasted image 20250528195432.png]]

## MDC能干什么

MDC 的应用场景其实蛮多的，下面简单列举几个。

a）在 WEB 应用中，如果想在日志中输出请求用户 IP 地址、请求 URL、统计耗时等等，MDC 基本都能支撑；

b）在 WEB 应用中，如果能画出用户的请求到响应整个过程，势必会快速定位生产问题==，那么借助 MDC 来保存用户请求时产生的 reqId，当请求完成后，再将这个 reqId 进行移除，这么一来通过 grep reqId 就能轻松 get 整个请求流程的日志轨迹；==  

c）在微服务盛行的当下，链路跟踪是个难题，而借助 MDC 去埋点，巧妙实现链路跟踪应该不是问题。