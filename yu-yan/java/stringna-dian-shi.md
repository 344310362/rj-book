## 引言

```
string在我们的编程中使用频率非常之高，它的设计又比较特别，所以我们有必要拿下这个高地。
```

## 案例

```
@Test
public void test(){
    String a = new String("abc");
    String c = new String("abc");
    String b = "abc";
    String d = "abc";

    System.out.println("a = abc :" + (a == "abc"));
    System.out.println("b = 'abc' :" + (b == "abc"));
    System.out.println("b+'a' = d+'a' :" + (b+"a" == d + "a"));
    System.out.println("a = c :" + (a == c));
    System.out.println("b = d :" + (b == d));
}

// 结果    
a = abc :false
b = abc :true
b+'a' = d+'a' :false
a = c :false
b = d :true
```

## **class常量池**

### **class常量池简介**

我们写的每一个Java类被编译后，就会形成一份class文件；class文件中除了包含类的版本、字段、方法、接口等描述信息外，还有一项信息就是常量池\(constant pool table\)，用于存放编译器生成的各种字面量\(Literal\)和符号引用\(Symbolic References\)；

每个class文件都有一个class常量池。

### **字面量和符号引用**

* 字面量包括：1.字符（特殊的基本类型） 2.八种基本类型的值 3.被声明为final的常量等;

* 符号引用包括：1.类和方法的全限定名 2.字段的名称和描述符 3.方法的名称和描述符。

### **运行时常量池**

* 运行时常量池存在于内存中，也就是class常量池被加载到内存之后的版本，不同之处是：它的字面量可以动态的添加\(String\#intern\(\)\),符号引用可以被解析为直接引用

* JVM在执行某个类的时候，必须经过加载、连接、初始化，而连接又包括验证、准备、解析三个阶段。而当类加载到内存中后，jvm就会将class常量池中的内容存放到运行时常量池中，由此可知，运行时常量池也是每个类都有一个。在解析阶段，会把符号引用替换为直接引用，解析的过程会去查询字符串常量池，也就是我们上面所说的StringTable，以保证运行时常量池所引用的字符串与字符串常量池中是一致的。

## 剖析

* 从上面我们了解到，从类的编译到加载到内存然后连接初始化后，一个类中声明的可确认的（排除动态拼接的）字符都会加载到字符常量池中。所以下面2个变量的字符引用都会指到常量池中的引用，是相同的。

```
String b = "abc";
String d = "abc";
```

* 方法的执行是在栈中，new出来的对象是在堆中



