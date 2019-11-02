## 引言

> 循环依赖就是N个类中循环嵌套引用，如果在日常开发中我们用new 对象的方式发生这种循环依赖的话程序会在运行时一直循环调用，直至内存溢出报错。下面说一下Spring是如果解决循环依赖的。

## 构造器参数依赖

Spring容器会将每一个正在创建的Bean 标识符放在一个“当前创建Bean池”中，Bean标识符在创建过程中将一直保持在这个池中。

因此如果在创建Bean过程中发现自己已经在 **当前创建Bean池 **里时将抛出BeanCurrentlyInCreationException异常表示循环依赖；而对于创建完毕的Bean将从“当前创建Bean池”中清除掉。

首先我们先初始化三个Bean。

```
public class StudentA {

    private StudentB studentB ;

    public void setStudentB(StudentB studentB) {
        this.studentB = studentB;
    }

    public StudentA() {
    }

    public StudentA(StudentB studentB) {
        this.studentB = studentB;
    }
}
public class StudentB {

    private StudentC studentC ;

    public void setStudentC(StudentC studentC) {
        this.studentC = studentC;
    }

    public StudentB() {
    }

    public StudentB(StudentC studentC) {
        this.studentC = studentC;
    }
}
public class StudentC {

    private StudentA studentA ;

    public void setStudentA(StudentA studentA) {
        this.studentA = studentA;
    }

    public StudentC() {
    }

    public StudentC(StudentA studentA) {
        this.studentA = studentA;
    }
}

```

上面是很基本的3个类，，StudentA有参构造是StudentB。StudentB的有参构造是StudentC，StudentC的有参构造是StudentA ，这样就产生了一个循环依赖的情况，
我们都把这三个Bean交给Spring管理，并用有参构造实例化。


```
    <bean id="a" class="com.zfx.student.StudentA">
        <constructor-arg index="0" ref="b"></constructor-arg>
    </bean>
    <bean id="b" class="com.zfx.student.StudentB">
        <constructor-arg index="0" ref="c"></constructor-arg>
    </bean>
    <bean id="c" class="com.zfx.student.StudentC">
        <constructor-arg index="0" ref="a"></constructor-arg>
    </bean>

```




