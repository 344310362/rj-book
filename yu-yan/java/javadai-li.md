## 引言

> Java 的代理就是客户类不再直接和委托类打交道, 而是通过一个中间层来访问, 这个中间层就是代理。

优点

* 隐藏委托类的实现
* 代码解耦
* 可以批量处理统一的一些代码逻辑

在 Java 中我们有很多场景需要使用代理类, 比如远程 RPC 调用的时候我们就是通过代理类去实现的, 还有 Spring 的 AOP 切面中我们也是为切面生成了一个代理类等等。

代理类主要分为静态代理、JDK 动态代理和 CGLIB 动态代理，它们各有优缺点，没有最好的, 存在就是有意义的，在不同的场景下它们会有不同的用武之地。

## 静态代理

定义接口和接口的实现类, 然后定义接口的代理对象, 将接口的实例注入到代理对象中, 然后通过代理对象去调用真正的实现类，实现过程非常简单也比较容易理解, **静态代理的代理关系在编译期间就已经确定了**。

```
// 委托接口
public interface IHelloService {
 /**
 * 定义接口方法
 * @param userName
 * @return
 */
 String sayHello(String userName);
}
// 委托类实现
public class HelloService implements IHelloService {
 @Override
 public String sayHello(String userName) {
 System.out.println("helloService" + userName);
 return "HelloService" + userName;
 }
}
// 代理类
public class StaticProxyHello implements IHelloService {
 private IHelloService helloService = new HelloService();
 @Override
 public String sayHello(String userName) {
 /** 代理对象可以在此处包装一下*/
 System.out.println("代理对象包装礼盒...");
 return helloService.sayHello(userName);
 }
}
// 测试静态代理类
public class MainStatic {
 public static void main(String[] args) {
 StaticProxyHello staticProxyHello = new StaticProxyHello();
 staticProxyHello.sayHello("isole");
 }
}
```

## 动态代理

代理类在程序运行时创建的代理方式被成为 动态代理。



