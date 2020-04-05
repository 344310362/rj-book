## make
 make 用来为 slice，map 或 chan 类型分配内存和初始化一个对象
 

```
var m map[string]int
//m["two"] = elem //这里会引发panic【除了添加键值对，我们对一个nil字典进行任何操作都不会报错】
 
if m == nil{
	m=make(map[string]int)
}
m["two"] = elem // 要先make否则 这里会引发panic。 
```

