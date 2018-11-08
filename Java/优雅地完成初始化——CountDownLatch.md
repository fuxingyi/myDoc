---
title : 优雅地完成初始化——CountDownLatch
flag : java CountDownLatch
---
# 优雅地完成初始化——CountDownLatch
 
## CountDownLatch 介绍
一个同步辅助类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待。
用给定的_计数_ 初始化 `CountDownLatch`。由于调用了 `countDown()`方法，所以在当前计数到达`0`之前，`await`方法会一直受阻塞。
**示例**
```java
import java.util.concurrent.CountDownLatch;   
/**
 * 实例：一个裁判，当裁判开始开枪，运动员才能开始跑 
 */ 
 public class Referee {

    public static void main(String[] args) throws InterruptedException {
        //开枪信号
        CountDownLatch runSignal = new CountDownLatch(1);    
        //运动员开始准备
        for (int i = 0; i < 8; i++) {
            new Thread(new Runner("运动员"+i , runSignal)).start();
        }
        System.out.println("裁判准备开枪。。。");
        //开枪
        runSignal.countDown();
    }

}

class Runner implements Runnable {

    private final CountDownLatch runSignal ;   
    private String name ;   
    public Runner(String name , CountDownLatch runSignal) {
        this.name = name ;
        this.runSignal = runSignal;
    }

    @Override
    public void run() {
        try {
            runSignal.await();
            System.out.println( name + "  is running ....");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
```java
//输出结果
裁判准备开枪。。。
运动员1  is running ....
运动员4  is running ....
运动员3  is running ....
运动员2  is running ....
运动员0  is running ....
运动员5  is running ....
运动员6  is running ....
运动员7  is running ....
```
> 注意
如果开枪信号这里改为`2`,那么即使裁判开枪了，运动员也不会跑的，除非开两枪。
//开枪信号
CountDownLatch runSignal = new CountDownLatch(`2`);   

## 优雅的初始化
模拟一个应用程序启动类，开始就启动N个线程，去检查N个外部服务是否正常并通知`倒计时锁存器`(CountDownLatch)。启动类一直在倒计时锁存器上等待，一旦验证和检查了所有外部服务，就恢复启动类执行。

 BaseHealthChecker.java :这个类是实现了Runnable接口，负责所有特定的外部服务健康检查的基类。

```java
import java.util.concurrent.CountDownLatch;

public abstract class BaseHealthChecker implements Runnable {

    private CountDownLatch _latch;
    private String _serviceName;
    private boolean _serviceUp;

    public BaseHealthChecker(String serviceName, CountDownLatch latch)
    {
        super();
        this._latch = latch;
        this._serviceName = serviceName;
        this._serviceUp = false;
    }

    @Override
    public void run() {
        try {
            verifyService();
            _serviceUp = true;
        } catch (Throwable t) {
            t.printStackTrace(System.err);
            _serviceUp = false;
        } finally {
            if(_latch != null) {
                _latch.countDown();
            }
        }
    }

    public String getServiceName() {
        return _serviceName;
    }

    public boolean isServiceUp() {
        return _serviceUp;
    }

    public abstract void verifyService();
}

```

NetworkHealthChecker.java,DatabaseHealthChecker.java和CacheHealthChecker.java都继承自BaseHealthChecker，引用CountDownLatch实例，除了服务名和休眠时间不同外，都实现各自的verifyService方法。

NetworkHealthChecker.java类

```java
import java.util.concurrent.CountDownLatch;

public class NetworkHealthChecker extends BaseHealthChecker
{
    public NetworkHealthChecker (CountDownLatch latch)
    {
        super("Network Service", latch);
    }

    @Override
    public void verifyService() 
    {
        System.out.println("Checking " + this.getServiceName());
        try 
        {
            Thread.sleep(7000);
        } 
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
        System.out.println(this.getServiceName() + " is UP");
    }
}

```

DatabaseHealthChecker.java类

```java
import java.util.concurrent.CountDownLatch;

public class DatabaseHealthChecker extends BaseHealthChecker
{
    public DatabaseHealthChecker (CountDownLatch latch)
    {
        super("Database Service", latch);
    }

    @Override
    public void verifyService() 
    {
        System.out.println("Checking " + this.getServiceName());
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(this.getServiceName() + " is UP");
    }
}

```

CacheHealthChecker.java类

```java
import java.util.concurrent.CountDownLatch;

public class CacheHealthChecker extends BaseHealthChecker
{
    public CacheHealthChecker (CountDownLatch latch)
    {
        super("Cache Service", latch);
    }

    @Override
    public void verifyService() 
    {
        System.out.println("Checking " + this.getServiceName());
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(this.getServiceName() + " is UP");
    }
}

```

ApplicationStartupUtil.java:是一个主启动类，它负责初始化闭锁，然后等待所有服务都被检查完成，再恢复执行。

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

public class ApplicationStartupUtil 
{
    private static List<BaseHealthChecker> _services;
    private static CountDownLatch _latch;

    private ApplicationStartupUtil()
    {
    }

    private final static ApplicationStartupUtil INSTANCE = new ApplicationStartupUtil();

    public static ApplicationStartupUtil getInstance()
    {
        return INSTANCE;
    }

    public static boolean checkExternalServices() throws Exception
    {
        _latch = new CountDownLatch(3);
        _services = new ArrayList<BaseHealthChecker>();
        _services.add(new NetworkHealthChecker(_latch));
        _services.add(new CacheHealthChecker(_latch));
        _services.add(new DatabaseHealthChecker(_latch));

        Executor executor = Executors.newFixedThreadPool(_services.size());

        for(final BaseHealthChecker v : _services) 
        {
            executor.execute(v);
        }

        _latch.await();

        for(final BaseHealthChecker v : _services) 
        {
            if( ! v.isServiceUp())
            {
                return false;
            }
        }
        return true;
    }
}

```

测试代码检测闭锁功能：

```java
public class Main {
    public static void main(String[] args) 
    {
        boolean result = false;
        try {
            result = ApplicationStartupUtil.checkExternalServices();
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("External services validation completed !! Result was :: "+ result);
    }
}

```

执行结果：
```java
Checking Network Service
Checking Cache Service
Checking Database Service
Database Service is UP
Cache Service is UP
Network Service is UP
External services validation completed !! Result was :: true
```

**参考文献**
[CountDownLatch](https://www.jianshu.com/p/4b6fbdf5a08f)