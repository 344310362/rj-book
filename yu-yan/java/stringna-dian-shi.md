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

* 方法的执行是在栈中，new出来的对象是在堆中，所以new出来的字符对象存储的是堆的地址，堆里面的内容是指到字符常量到对象。所以下面的 a 和 c 是不同的。

![](/assets/language/java/string_view1.png)

```
String a = new String("abc");
String c = new String("abc");
```

* 在字符串拼接中，有一个参数是非字面量，而是一个变量的话，整个拼接操作会被编译成 `StringBuilder.append`,这个就产生了新的对象，然后因为它是编译期不确定，运行期才知道的，所以常量池并不会保存。所以下面的不相同。

```
System.out.println("b+'a' = d+'a' :" + (b+"a" == d + "a"));
```

## Intern

上面有提到，字符是可确认的还是不可确认的。可确认的字符在编译期的时候就会被放入class的常量池，加载的时候就会被放入方法区中的字符常量池。像动态拼接的这种不可确认的，可以通过intern方法在运行期动态的加入的常量池中。

```
static final int MAX = 1000 * 10000;
static final String[] arr = new String[MAX];

public static void main(String[] args) throws Exception {
    Integer[] DB_DATA = new Integer[10];
    Random random = new Random(10 * 10000);
    for (int i = 0; i < DB_DATA.length; i++) {
        DB_DATA[i] = random.nextInt();
    }
    for (int i = 0; i < MAX; i++) {
         arr[i] = new String(String.valueOf(DB_DATA[i % DB_DATA.length])).intern();
    }
}
```

## PS

* 在JDK6.0及之前版本，字符串常量池是放在Perm Gen区\(也就是方法区\)中；

* 在JDK7.0版本，字符串常量池被移到了堆中了。至于为什么移到堆内，大概是由于方法区的内存空间太小了。



