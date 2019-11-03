## 引言

> java 文件是有javac编译为class字节码文件，javac编译期并不进行连接（分配内存工作），而是jvm运行时才进行动态加载和动态连接。

## 什么是类的加载

jvm将class文读取到内存中，经过对class文件的校验、转换解析、初始化最终在jvm的heap和方法区分配内存形成可以被jvm直接使用的类型的过程。

## 类的生命周期

![](/assets/language/java/class_lifecycle.jpg)

加载 验证 准备 初始化和卸载 的顺序是确定的，而“解析”不一定在初始化之前，很有可能在初始化之后。

## 分阶段解释

### 1、加载Loading

这个阶段jvm完成以下动作：

1. 类加载器通过类的全路径限定名读取类的二进制字节流，
2. 将二进制字节流代表的类结构转化到运行时数据区的 方法区中，
3. 最后  在jvm堆中生成代表这个类的java.lang.Class实例\(不是这个类的实例\)

#### 类加载器

获取类的二进制流 既可以使用jvm自带的类加载器，也可以自己写加载器来加载，这一小步是完全可控的。不同的加载器可以从各种地方读取：zip包jar包，class文件，网络流 。。。读取类的二进制字节流。

同一个加载器加载的同源类才是真的同类。不同加载器加载同源类，不是同类！instanceof为FALSE。

#### 类加载的双亲委派模型

各个加载器都是先委托自己的父加载器加载类，若确实没加载到再自己来加载  
于是java默认的类查找加载顺序是自顶向下的，树状结构  
双亲委托的意图是保证java类型体系中最基础的行为一致，优先加载JDK中的类

![](/assets/language/java/class_load_parents_delegation.png)

加载器主要有四种：

启动类加载器bootstrap loader

> 用c++实现为jvm的一部分\(仅指sun的hotspot\)，负责 JAVA\_HOME/lib下面的类库中的类的加载，这个加载器，java程序无法引用到。

扩展类加载器Extension Loader

> 由sun.misc.Launcher$ExtClassLoader类实现，可在java中使用，负责JAVA\_HOME/lib/ext 目录和java.ext.dir目录中类库的类的加载。

应用系统类加载器Application System Loader

> 由sun.misc.Louncher$AppClassLoader实现，负责加载用户类路径中类库中的类，如果没有使用自定义的加载器，这个就是默认的 加载器！

用户自定义加载器

> 自己定义从哪里加载类的二进制流



