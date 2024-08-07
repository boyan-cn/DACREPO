# 解决方案 - Java语言基础 - Scanner - 手动EOF(End of File)方式 & Scanner.close() 使用注意



#### 1. Scanner 输入时，手动 EOF(End of File) 方式代码段

```java
Scanner scan = new Scanner(System.in);
// 在无限循环中
while(true){
    String c= scan.next();				// 1. 接收字符串
    if (c.matches("###")) break;	// 2. 判断 EOF 则 break 跳出循环
    else citys.add(c);						// 3. 符合操作条件 正常往下走
}
scan.close();
```

- EOF 是 "End of File" 的缩写，直译为“文件结束”。在计算机编程和数据处理中，EOF 用于指示数据源或数据流的终点。在**文件读写操作**中：在进行文件读写操作时，EOF 用来标识文件内容的结束。例如，在使用 C 语言或其他编程语言进行文件操作时，很多函数（如 `fread`, `fgets`）会返回特定的 EOF 标志（通常是 `-1` 或者 `null`），用来指示已经读到文件末尾。
- 反思：一开始没想到的原因：被 while(!hasNextLine) 先入为主限制了思路；



#### 2. Scanner.close() 方法使用的注意事项

如果后面还要用 scanner 输入，不要调用 .close() 函数！关掉后再次使用会空指针报错！

因为使用close()关掉之后将无法再使用Scanner，即使再次声明了一个Scanner对象。

