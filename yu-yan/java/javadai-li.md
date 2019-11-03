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

代理类在程序运行时创建的代理方式被成为 动态代理。在了解动态代理之前, 我们先简回顾一下 JVM 的类加载机制中的加载阶段要做的三件事情。

1. 通过一个类的全名或其它途径来获取这个类的二进制字节流
2. 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
3. 在内存中生成一个代表这个类的 Class 对象, 作为方法区中对这个类访问的入口

而我们要说的动态代理，主要就发生在第一个阶段, 这个阶段类的二进制字节流的来源可以有很多, 比如 zip 包、网络、运行时计算生成、其它文件生成 \(JSP\)、数据库获取。其中运行时计算生成就是我们所说的动态代理技术，在 Proxy 类中, 就是运用了 ProxyGenerator.generateProxyClass 来为特定接口生成形式为 \*$Proxy 的代理类的二进制字节流。所谓的动态代理就是想办法根据接口或者目标对象计算出代理类的字节码然后加载进 JVM 中。实际计算的情况会很复杂，我们借助一些诸如 JDK 动态代理实现、CGLIB 第三方库来完成的。

实现动态代理有3种方式

* 通过接口的JDK动态代理
* 通过继承类的CGLib动态代理
* ASM框架的javassist

### JDK 动态代理

在 Java 的动态代理中, 主要涉及 2 个类,java.lang.reflect.Proxy和java.lang.reflect.InvocationHandler

我们需要一个实现 InvocationHandler 接口的中间类, 这个接口只有一个方法 invoke 方法, 方法的每个参数的注释如下代码。

我们对处理类中的所有方法的调用都会变成对 invoke 方法的调用，这样我们可以在 invoke 方法中添加统一的处理逻辑（也可以根据 method 参数判断是哪个方法）。中间类 \(实现了 InvocationHandler 的类\) 有一个委托类对象引用, 在 Invoke 方法中调用了委托类对象的相应方法，通过这种聚合的方式持有委托类对象引用，把外部对 invoke 的调用最终都转为对委托类对象的调用。

实际上，中间类与委托类构成了静态代理关系，在这个关系中，中间类是代理类，委托类是委托类。然后代理类与中间类也构成一个静态代理关系，在这个关系中，中间类是委托类，代理类是代理类。也就是说，动态代理关系由两组静态代理关系组成，这就是动态代理的原理。

```
public interface InvocationHandler {
 /**
 * 调用处理
 * @param proxy 代理类对象
 * @param methon 标识具体调用的是代理类的哪个方法
 * @param args 代理类方法的参数
 */
 public Object invoke(Object proxy, Method method, Object[] args)
 throws Throwable;
}
```

Demo 如下:

```
// 委托类接口
public interface IHelloService {
 /**
 * 方法1
 * @param userName
 * @return
 */
 String sayHello(String userName);
 /**
 * 方法2
 * @param userName
 * @return
 */
 String sayByeBye(String userName);
}
// 委托类
public class HelloService implements IHelloService {
 @Override
 public String sayHello(String userName) {
 System.out.println(userName + " hello");
 return userName + " hello";
 }
 @Override
 public String sayByeBye(String userName) {
 System.out.println(userName + " ByeBye");
 return userName + " ByeBye";
 }
}
// 中间类
public class JavaProxyInvocationHandler implements InvocationHandler {
 /**
 * 中间类持有委托类对象的引用,这里会构成一种静态代理关系
 */
 private Object obj ;
 /**
 * 有参构造器,传入委托类的对象
 * @param obj 委托类的对象
 */
 public JavaProxyInvocationHandler(Object obj){
 this.obj = obj;
 }
 /**
 * 动态生成代理类对象,Proxy.newProxyInstance
 * @return 返回代理类的实例
 */
 public Object newProxyInstance() {
 return Proxy.newProxyInstance(
 //指定代理对象的类加载器
 obj.getClass().getClassLoader(),
 //代理对象需要实现的接口，可以同时指定多个接口
 obj.getClass().getInterfaces(),
 //方法调用的实际处理者，代理对象的方法调用都会转发到这里
 this);
 }
 /**
 *
 * @param proxy 代理对象
 * @param method 代理方法
 * @param args 方法的参数
 * @return
 * @throws Throwable
 */
 @Override
 public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
 System.out.println("invoke before");
 Object result = method.invoke(obj, args);
 System.out.println("invoke after");
 return result;
 }
}
// 测试动态代理类
public class MainJavaProxy {
 public static void main(String[] args) {
 JavaProxyInvocationHandler proxyInvocationHandler = new JavaProxyInvocationHandler(new HelloService());
 IHelloService helloService = (IHelloService) proxyInvocationHandler.newProxyInstance();
 helloService.sayByeBye("paopao");
 helloService.sayHello("yupao");
 }
}
```

在上面的测试动态代理类中, 我们调用 Proxy 类的 newProxyInstance 方法来获取一个代理类实例。这个代理类实现了我们指定的接口并且会把方法调用分发到指定的调用处理器。

首先通过 newProxyInstance 方法获取代理类的实例, 之后就可以通过这个代理类的实例调用代理类的方法，对代理类的方法调用都会调用中间类 \(实现了 invocationHandle 的类\) 的 invoke 方法，在 invoke 方法中我们调用委托类的对应方法，然后加上自己的处理逻辑。

java 动态代理最大的特点就是动态生成的代理类和委托类实现同一个接口。java 动态代理其实内部是通过反射机制实现的，也就是已知的一个对象，在运行的时候动态调用它的方法，并且调用的时候还可以加一些自己的逻辑在里面。

#### Proxy.newProxyInstance 源码阅读

上面说过, Proxy.newProxyInstance 通过反射机制用来动态生成代理类对象, 为接口创建一个代理类，这个代理类实现这个接口。具体源码如下

```
public static Object newProxyInstance(ClassLoader loader,
 Class<?>[] interfaces,
 InvocationHandler h)
 throws IllegalArgumentException
 {
 // 检查空指针
 Objects.requireNonNull(h);
 // 用原型实例指定创建对象的种类,并且通过拷贝这些原型创建新的对象
 final Class<?>[] intfs = interfaces.clone();
 // 获取系统的安全接口,不为空的话需要验证是否允许访问这种关系的代理访问
 final SecurityManager sm = System.getSecurityManager();
 if (sm != null) {
 checkProxyAccess(Reflection.getCallerClass(), loader, intfs);
 }
 /*
 * 生成代理类 Class,通过类加载器和接口
 */
 Class<?> cl = getProxyClass0(loader, intfs);
 /*
 * 通过构造器来创建实例
 */
 try {
 if (sm != null) {
 checkNewProxyPermission(Reflection.getCallerClass(), cl);
 }
 //获取所有的构造器
 final Constructor<?> cons = cl.getConstructor(constructorParams);
 final InvocationHandler ih = h;
 // 构造器不是public的话需要设置可以访问
 if (!Modifier.isPublic(cl.getModifiers())) {
 AccessController.doPrivileged(new PrivilegedAction<Void>() {
 public Void run() {
 cons.setAccessible(true);
 return null;
 }
 });
 }
 // 返回创建的代理类Class的实例对象
 return cons.newInstance(new Object[]{h});
 } catch (IllegalAccessException|InstantiationException e) {
 throw new InternalError(e.toString(), e);
 } catch (InvocationTargetException e) {
 Throwable t = e.getCause();
 if (t instanceof RuntimeException) {
 throw (RuntimeException) t;
 } else {
 throw new InternalError(t.toString(), t);
 }
 } catch (NoSuchMethodException e) {
 throw new InternalError(e.toString(), e);
 }
 }
```

### CGLIB 动态代理

JDK 动态代理依赖接口实现，而当我们只有类没有接口的时候就需要使用另一种动态代理技术 CGLIB 动态代理。首先 CGLIB 动态代理是第三方框架实现的，在 maven 工程中我们需要引入 cglib 的包, 如下:

```
<dependency>
 <groupId>cglib</groupId>
 <artifactId>cglib</artifactId>
 <version>2.2</version>
</dependency>
```

CGLIB 代理是针对类来实现代理的，原理是对指定的委托类生成一个子类并重写其中业务方法来实现代理。代理类对象是由 Enhancer 类创建的。CGLIB 创建动态代理类的模式是:

1. 查找目标类上的所有非 final 的 public 类型的方法 \(final 的不能被重写\)

2. 将这些方法的定义转成字节码

3. 将组成的字节码转换成相应的代理的 Class 对象然后通过反射获得代理类的实例对象

4. 实现 MethodInterceptor 接口, 用来处理对代理类上所有方法的请求

```
// 委托类,是一个简单类
public class CglibHelloClass {
 /**
 * 方法1
 * @param userName
 * @return
 */
 public String sayHello(String userName){
 System.out.println("目标对象的方法执行了");
 return userName + " sayHello";
 }
 public String sayByeBye(String userName){
 System.out.println("目标对象的方法执行了");
 return userName + " sayByeBye";
 }
}
/**
 * CglibInterceptor 用于对方法调用拦截以及回调
 *
 */
public class CglibInterceptor implements MethodInterceptor {
 /**
 * CGLIB 增强类对象，代理类对象是由 Enhancer 类创建的，
 * Enhancer 是 CGLIB 的字节码增强器，可以很方便的对类进行拓展
 */
 private Enhancer enhancer = new Enhancer();
 /**
 *
 * @param obj 被代理的对象
 * @param method 代理的方法
 * @param args 方法的参数
 * @param proxy CGLIB方法代理对象
 * @return cglib生成用来代替Method对象的一个对象，使用MethodProxy比调用JDK自身的Method直接执行方法效率会有提升
 * @throws Throwable
 */
 @Override
 public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
 System.out.println("方法调用之前");
 Object o = proxy.invokeSuper(obj, args);
 System.out.println("方法调用之后");
 return o;
 }
 /**
 * 使用动态代理创建一个代理对象
 * @param c
 * @return
 */
 public Object newProxyInstance(Class<?> c) {
 /**
 * 设置产生的代理对象的父类,增强类型
 */
 enhancer.setSuperclass(c);
 /**
 * 定义代理逻辑对象为当前对象，要求当前对象实现 MethodInterceptor 接口
 */
 enhancer.setCallback(this);
 /**
 * 使用默认无参数的构造函数创建目标对象,这是一个前提,被代理的类要提供无参构造方法
 */
 return enhancer.create();
 }
}
//测试类
public class MainCglibProxy {
 public static void main(String[] args) {
 CglibProxy cglibProxy = new CglibProxy();
 CglibHelloClass cglibHelloClass = (CglibHelloClass) cglibProxy.newProxyInstance(CglibHelloClass.class);
 cglibHelloClass.sayHello("isole");
 cglibHelloClass.sayByeBye("sss");
 }
}
```

对于需要被代理的类，它只是动态生成一个子类以覆盖非 final 的方法，同时绑定钩子回调自定义的拦截器。值得说的是，它比 JDK 动态代理还要快。值得注意的是，我们传入目标类作为代理的父类。不同于 JDK 动态代理，我们不能使用目标对象来创建代理。目标对象只能被 CGLIB 创建。在例子中，默认的无参构造方法被使用来创建目标对象。

#### 总结

静态代理比较容易理解, 需要被代理的类和代理类实现自同一个接口, 然后在代理类中调用真正实现类, 并且静态代理的关系在编译期间就已经确定了。而动态代理的关系是在运行期间确定的。静态代理实现简单，适合于代理类较少且确定的情况，而动态代理则给我们提供了更大的灵活性。

JDK 动态代理所用到的代理类在程序调用到代理类对象时才由 JVM 真正创建，JVM 根据传进来的 业务实现类对象 以及 方法名 ，动态地创建了一个代理类的 class 文件并被字节码引擎执行，然后通过该代理类对象进行方法调用。我们需要做的，只需指定代理类的预处理、调用后操作即可。

静态代理和动态代理都是基于接口实现的, 而对于那些没有提供接口只是提供了实现类的而言, 就只能选择 CGLIB 动态代理了

JDK 动态代理和 CGLIB 动态代理的区别

* JDK 动态代理基于 Java 反射机制实现, 必须要实现了接口的业务类才能用这种方法生成代理对象。

* CGLIB 动态代理基于 ASM 框架通过生成业务类的子类来实现。

* JDK 动态代理的优势是最小化依赖关系，减少依赖意味着简化开发和维护并且有 JDK 自身支持。还可以平滑进行 JDK 版本升级，代码实现简单。基于 CGLIB 框架的优势是无须实现接口，达到代理类无侵入，我们只需操作我们关系的类，不必为其它相关类增加工作量，性能比较高。

描述代理的几种实现方式? 分别说出优缺点?

代理可以分为 "静态代理" 和 "动态代理"，动态代理又分为 "JDK 动态代理" 和 "CGLIB 动态代理" 实现。

静态代理：代理对象和实际对象都继承了同一个接口，在代理对象中指向的是实际对象的实例，这样对外暴露的是代理对象而真正调用的是 Real Object.

* 优点：可以很好的保护实际对象的业务逻辑对外暴露，从而提高安全性。

* 缺点：不同的接口要有不同的代理类实现，会很冗余

JDK 动态代理：

为了解决静态代理中，生成大量的代理类造成的冗余；

JDK 动态代理只需要实现 InvocationHandler 接口，重写 invoke 方法便可以完成代理的实现，

jdk 的代理是利用反射生成代理类 Proxyxx.class 代理类字节码，并生成对象

jdk 动态代理之所以只能代理接口是因为代理类本身已经 extends 了 Proxy，而 java 是不允许多重继承的，但是允许实现多个接口

* 优点：解决了静态代理中冗余的代理实现类问题。

* 缺点：JDK 动态代理是基于接口设计实现的，如果没有接口，会抛异常。

CGLIB 代理：

由于 JDK 动态代理限制了只能基于接口设计，而对于没有接口的情况，JDK 方式解决不了；

CGLib 采用了非常底层的字节码技术，其原理是通过字节码技术为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑，来完成动态代理的实现。

实现方式实现 MethodInterceptor 接口，重写 intercept 方法，通过 Enhancer 类的回调方法来实现。

但是 CGLib 在创建代理对象时所花费的时间却比 JDK 多得多，所以对于单例的对象，因为无需频繁创建对象，用 CGLib 合适，反之，使用 JDK 方式要更为合适一些。

同时，由于 CGLib 由于是采用动态创建子类的方法，对于 final 方法，无法进行代理。

优点：没有接口也能实现动态代理，而且采用字节码增强技术，性能也不错。

缺点：技术实现相对难理解些。

