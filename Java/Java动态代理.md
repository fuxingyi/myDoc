---
title : Java动态代理
flag : java 动态代理 proxy cglib
time : 2018/10/30
---

# Java动态代理
> 代理模式的定义：为其他对象提供一种[代理](https://baike.baidu.com/item/%E4%BB%A3%E7%90%86)以方便对这个对象的访问。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用。
> 简单说，A对象存在一个代理对象A+，其他对象不能直接访问A对象，而是通过A+对象来操作A对象。A+对象本质上是持有A对象的引用。A+对象也可以说是A对象的加强或修饰。

## 静态代理
> 在开始执行前就已经确定好关系的代理，称为静态代理。在Java程序上指，在**编译期**就已经确定的代理关系。
例如：
```java
/**
 *  超类，定义方法接口 
 */ 
public interface SuperClass {
    void sayHello(String name); 
}
```
```java
/**
 * SuperClass 类的一个实现类 
 */ 
public class RealClass implements SuperClass {
    @Override
    public void sayHello(String name) {
        System.out.println("hi ," + name );
    }
}
```
```java
/**
 *  静态代理类 
 */ 
public class StaticProxyClass implements SuperClass {

    private SuperClass realClass ;
    
    @Override
    public void sayHello(String name) {
        System.out.println("执行被代理方法前的操作...");
        this.realClass.sayHello(name);
        System.out.println("执行被代理方法后的操作...");
    }

    public StaticProxyClass(SuperClass realClass) {
        this.realClass = realClass;
    }
}
```

```java
/**
 * 测试类
 */
public void test(){
    SuperClass clazz = new RealClass();
    StaticProxyClass proxyClass = new StaticProxyClass(clazz);
    proxyClass.sayHello("fuxingyi");   
}
```
```java
/**
 * 输出结果
 */
执行被代理方法前的操作...
hi ,fuxingyi
执行被代理方法后的操作... 

```

## 动态代理
> 动态代理的思想和静态代理是一样的，唯一的区别是何时确定代理关系。动态代理是在Java程序**运行时**确定关系的代理。
> Java 实现动态代理的方式有两种，一种是Java JDK自带的实现方式；一种是CgLib提供的实现方式。

### Java JDK实现动态代理
> 主要涉及一个接口==InvocationHandler== 和一个类==Proxy==。Proxy类在创建代理类时，需要提供一个代理类的接口（也就说JDK实现代理时，被代理的类必须存在接口）和实现了InvocationHandler接口的实现类。
```java
/**
 * 定义接口
 */
public interface IOrderService {
    public Long save(String order);   
    public Boolean update(String order);   
    public void delete(String order);   
    public String combineOrder(String order0,String order1);   
}
```
```java
/**
 * 对接口进行实现
 */
public class OrderServiceImpl implements IOrderService  {

    @Override
    public Long save(String order) {
        System.out.println("保存Order，order："+order);
        return 1L ;
    }
    @Override
    public Boolean update(String order) {
        System.out.println("修改Order,order:"+order );
        return true ;  
    }
    @Override
    public void delete(String order) {
        System.out.println("删除Order，order:"+order);
    }
    @Override
    public String combineOrder(String order0, String order1) {
        System.out.println("合并订单 order0："+order0+",order1:"+order1);
        return order0 + order1;
    }
}
```
```java
public class OrderServiceProxy  implements InvocationHandler {

    private Object originalObject ; 
    public Object bind(Object obj){
        this.originalObject = obj ;
        return Proxy.newProxyInstance(
                obj.getClass().getClassLoader(),
                obj.getClass().getInterfaces(),
                this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object result = null;
        if ("save".equals(method.getName())){
            System.out.println("执行前 ... ");
            result = method.invoke(this.originalObject,args);
            System.out.println("执行后 ... ");
        }else{
            result = method.invoke(this.originalObject,args);
        }
        return result ;
    }
}
```
```java
/**
 * 测试方法
 */
public void test() {
    IOrderService orderService = new OrderServiceImpl();
    OrderServiceProxy orderServiceProxy = new OrderServiceProxy();            
    Long result = ((IOrderService) orderServiceProxy.bind(orderService)).save("{id:1,name:zbx}");    
    System.out.println(result);
}
```
```java
/**
 * 执行结果
 */
 执行前 ... 
 保存Order，order：{id:1,name:zbx}
 执行后 ... 
 1
```

### CgLib实现动态代理
> cgLib是java的一个开发包，请自行引入。cgLib可以对接口、实现类做动态代理。一般先定义一个拦截器，再使用cgLib提供的增强器来生成一个代理类。
```java
/**
 * 自己实现了一个拦截器，实现接口MethodInterceptor的intercept方法
 */
public class MyInterceptor implements MethodInterceptor {

    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println("start ...");
        Object result = methodProxy.invokeSuper(o,objects);
        System.out.println("end ...");
        return result ;
    }
}
```
```java
/**
 *  测试类，声明一个Enhancer增强类，使用增强类生成一个RealClass的代理类
 */
public void test(){
    Enhancer enhancer = new Enhancer();
    enhancer.setSuperclass(RealClass.class);
    enhancer.setCallback(new MyInterceptor());    
    RealClass proxyClazz = (RealClass) enhancer.create(); 
    proxyClazz.sayHello("fuxingyi"); 
}
```
```java
/**
 * 执行结果
 */
start ...
hi ,fuxingyi
end ...
```

### JDK动态代理与CGLib动态代理原理分析

|动态代理|实现原理|优缺点|
|--|--|--|
|JDK| 利用反射原理 | 代理类需要有接口定义，实现简单 |
|cgLib| 新建代理类子类的原理 | 通过转换字节码来生成子类，效率高；无法代理final方法 |







**参考博文**
[秒懂Java代理与动态代理模式](https://blog.csdn.net/ShuSheng0007/article/details/80864854)










