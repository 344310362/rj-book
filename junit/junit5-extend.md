junit5 舍去了 junit4 runner 的概率，新增了 Extend

自定义 Extend 要继承 `org.junit.jupiter.api.extension.Extension`

## 案例

```
public class DiyCondition implements ExecutionCondition, BeforeAllCallback {
...
}

// 具体的测试类要声明使用
@ExtendWith({DiyCondition.class})
public class HelloJMockitTest {

}
```

## 默认 Extension 的设置

利用 serviceLoader ，这样没有声明 \`@ExtendWith\` 的时候默认用这个。

![](/assets/junit/junit5-extend-serviceLoad.png)



## Extension 有 4 种添加方式

1. 测试类上面添加 @ExtendWith
2. serviceLoad：声明在 METE-INF.service 里面
3. 在 annotation 中添加 @ExtendWith


    ```
    @Target({ ElementType.ANNOTATION_TYPE, ElementType.METHOD })
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @API(status = EXPERIMENTAL, since = "5.0")
    @TestTemplate
    @ExtendWith(ParameterizedTestExtension.class)
    public @interface ParameterizedTest {}
    ```
4.通过 @RegisterExtension 
