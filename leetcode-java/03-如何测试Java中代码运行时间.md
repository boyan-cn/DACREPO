# 如何测试Java中代码运行时间

### 1.截取运行前后时刻计算，获取实时时刻：System.currentTimeMillis();

```java
long starTime = System.currentTimeMillis();
// Your Code Block
long endTime = System.currentTimeMillis();
System.out.println("程序运行时间："+(endTime-starTime)+"/毫秒ms");
```

### 2.问题：当函数运行时间太短，测试结果为0s时，如何测试准确的运行时间？

答案：重复！比如获取循环执行 1k-1w 遍的执行时间，然后做简单除法；

