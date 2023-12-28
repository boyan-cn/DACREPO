# 基本编程格式

### 1. 多行注释

：用来写**类/成员函数**的声明（Java标准类库中的注释格式）

```java
/**
*对于函数/类的描述：只有类的成员函数外部 有这样的缩进格式，函数内部不提供美观缩进优化；
*
*
*@param  参数1
*@param  参数2
*@return 返回值
*@author 作者
*/
```

### 2. 快速操作

```
1.查看方法源代码 command + 鼠标左 
2.回去 option + command + 方向⬅️

可以让idea快速的帮我们把框架搭起来
3.Generate 自动生成 getter/setter constructor……
4.直接使用未构造的内容后，点一下左栏中的提示💡，可以生成没有的类/函数
```







# Java核心语法

### 输入 java.util.Scanner

```java
//创建Scanner对象
Scanner scan = new Scanner();
//在读取前：判断是否还有输入？hasNext()/hasNextLine()
if(scan.hasNextLine()){}
//获得用户输入：通过Scanner类中的 next() nextInt()等方法
int n=scan.nextInt();
//关闭输入
scan.close();
```

1.next和nextLine区别

next():

- 一定要读取到有效字符后才可以结束输入。
- 对输入有效字符之前遇到的空白，next() 方法会自动将其去掉。
- 只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。 
- next() 不能得到带有空格的字符串。

nextLine()： 

- 以Enter为结束符,也就是说 nextLine()方法返回的是输入回车之前的所有字符。 
- 可以获得空白。

2. 其他类型数据也可以，最好在用之前has方法验证一下，再用next方法获得；



### 逻辑运算/布尔运算 &&｜｜！

两种称呼，逻辑运算又称布尔运算

&& 与 都真才真

｜｜或 任意真就真

！非 反转当前状态



### String 和 StringBuffer

1. 字符串连接

一般不会通过String直接用"s1+s2"连接，系统开销会很大，因为String变量是一旦创建不可更改的，只能重新生成一个新的String变量；

因此，经常采用：StringBuffer变量：s1.append(s2) 来做字符串连接；





# 数据类型容器

### 一. 数组（定长）

```java
int[] array = new int[100];
```

这样申请出来的数组空间，一旦被申请出来，便不能再改变。

定长数组满了之后：一个很不好用的方法是，再申请一个更大的数组，把当前的数据全部逐个复制过去，但这样代码的时间复杂度极高，所以想实现可变长度的数组，见2，通过java容器类。

##### 声明：给数组变量起名

```java
int[] array; //首选方法 因为和C/C++一样，更具有通用性；
```

##### 创建：实例化

```java
array = new int[size]; //创建固定大小的数组空间，并赋给array变量名；
```

##### 处理：逐个/快速

###### 1. 逐个处理数组元素：for/foreach循环

```java
for(datatype element:array){
  //循环执行的基本语句
}//（1.数组元素的数据类型 2.任意临时变量名 3.要循环遍历的数组名）
```

###### *2. 快速操作：java.util.Arrays类中的方法

```java
//快速打印全部数组内容
System.out.println(Arrarys.toString(数组名));
```

##### 数组作为函数的参数

```java
//1.调用
System.out.println(array);
//2.接收
public void printArray(int[] array){}//声明一个数组名接收传来的数组空间地址
//3.作为函数返回值
public int[] reverse(int[] array){
  //……
  return array;//用函数名返回
}
```

##### 二维矩阵（数组）的sout

```java
for (int[] f :instances) {
    System.out.println(Arrays.toString(f));
}
```



### 二. 容器（动态）

！！！能用Java自带的容器里的方法实现尽管放手去用，肯定比自己写的高效

要阅读并熟悉各容器类提供的功能；

以及容器中的内容都是可以sout(name)直接输出所有内容的；

#### S1 顺序容器 ArrayList< Object> 

#### S2 对象数组 Object[]

###### 1. foreach 循环：object[]  与 基本类型[] 区别 

```java
// Object[] 对象类型数据的数组：因为Object[]存储的是地址，所以修改原内容；
// int[]等 基本数据类型的数组：因为直接存储的数据内容，所以用临时变量复制内容，不修改原内容；
```

###### 2. 如何传递任意数量参数的函数 - Java？...

```java
public void receive(String...s){ // ...
    System.out.println(Arrays.toString(s));
    System.out.println(s[0]); // 其实以String[]形式打包接收动态数量的参数！
    System.out.println(s[1]);
    System.out.println(s[2]);
}
rnparameter.receive("Hello ","World ","!"); // usage，直接输入任意数量的参数
```



#### S3 集合容器 Set< Object>（HashSet……）

Set中的数据，是 无序 且无重复的（=数学中集合的概念）无法通过get()获取某位置上存储的内容；

在java.util中，Set是一个抽象类，无法直接被实现

 

#### S4 散列表 HashMap<Key, Value> 

*一个非常好用的Container！用好了会非常省事！

​		1. HashMap <Key, Value>: 任意数据类型，但必须都是大写的数据类型！ Integer✅ int❌

​		2. 同一个Key，只保留最后更新的一个Value：多次对同一个Key更新Value只存储最后一版

















# Java面向对象

### S0. 编程思想

##### · OOAD

> 封装思想：当面向类的使用者时，使用者在声明+实例化这个对象后，只需要调用该对象的方法就好，不需要知道是怎么实现的；
>
> 接口设计：老规矩，先定义接口，再考虑实现功能！
>
> 避免初学者倾向：所有功能部分都在一起的，人机交互I/O和业务逻辑computing部分完全捆绑在一起的；
>
> 真正的软件：UI 和 业务逻辑 分离！
>
> 理想的模型：Object之间尽量“松耦合”，平行的Object之间尽量减少联系，让Higher Layer的Object提供通信服务；



##### · 拿到陌生程序的两种读法 - S3

> 找运行入口Main 读大逻辑 从大到细；
>
> 找最小的类->追到父类，看懂每一个小部件的作用；



##### · “面向接口的编程方式” - S4

> 设计程序时先定义接口，再定义类
>
> 任何需要在函数间传入传出的一定是接口，而不是具体的类
>
> 这种编程方式是Java成功的关键之一，因为极适合多人同时写一个大程序
>
> 也是Java被批评的要点之一，因为代码量膨胀起来很快



### S1. 封装和修饰符（Class内部）

```java
class Object{

  //1.属性：All Private（无意外情况下）不被外界随意使用；
  
  //2.方法：只有对外提供服务的才 Public
  				 理论上，将所有getter/setter()全public是不合理的；
}
```

##### 	1. private

​					只能用于类的属性、方法（即类的成员变量、成员函数）被Private修饰的元素只能类自己内部访问；

​					这个private限制是对**类**的，不是对**对象**的！

​					继承：父类中的private**虽然被继承，但不可见**，所以改成protected；

##### 	2. public

​					任何人都可以访问

​				   当一个编译单元(.java)中有多个class时，最多只能有一个是public class(可以没有)，且此类与文件同名；

##### 	3. “friendly”

​					未加private/public限定词==“friendly”，表示 与其在同一package内可访问

​					是同一层**目录**，不是同一个文件！

##### 	4. static

​		   类中由static修饰的内容，不再属于实例化的对象，而是属于类本身，每个实例化的对象均可访问；

​			1. 类成员变量可以由任何对象访问/修改；

​			2. 类函数中，不可访问任何non-static的内容，也没有this.访问方式，因为无法建立类函数与调用他们的对象之间的关系；

##### 5. protected - 继承

​			在“friendly”基础上，增加任何子类可用；



### S2. 继承与多态

##### 1. 继承

> 面向对象程序设计语言有三大特性：封装、继承和多态性。继承是面向对象语言的重要特征之一，没有继承的语言只能被称作“使用对象的语言”。**继承**是非常简单而强大的设计思想，它**提供了我们代码重用和程序组织**的有力工具。
>
> 类是规则，用来制造对象的规则。我们不断地定义类，用定义的类制造一些对象。类定义了对象的属性和行为，就像图纸决定了房子要盖成什么样子。
>
> 一张图纸可以盖很多房子，它们都是相同的房子，但是坐落在不同的地方，会有不同的人住在里面。假如现在我们想盖一座新房子，和以前盖的房子很相似，但是稍微有点不同。任何一个建筑师都会拿以前盖的房子的图纸来，稍加修改，成为一张新图纸，然后盖这座新房子。所以一旦我们有了一张设计良好的图纸，我们就可以基于这张图纸设计出很多相似但不完全相同的房子的图纸来。
>
> 基于已有的设计创造新的设计，就是面向对象程序设计中的继承。在继承中，新的类不是凭空产生的，而是基于一个已经存在的类而定义出来的。通过继承，新的类自动获得了基础类中所有的成员，包括成员变量和方法，包括各种访问属性的成员，无论是public还是private。当然，在这之后，程序员还可以加入自己的新的成员，包括变量和方法。显然，通过继承来定义新的类，远比从头开始写一个新的类要简单快捷和方便。继承是支持代码重用的重要手段之一。
>
> 类这个词有分类的意思，具有相似特性的东西可以归为一类。比如所有的鸟都有一些共同的特性：有翅膀、下蛋等等。鸟的一个子类，比如鸡，具有鸟的所有的特性，同时又有它自己的特性，比如飞不太高等等；而另外一种鸟类，比如鸵鸟，同样也具有鸟类的全部特性，但是又有它自己的明显不同于鸡的特性。
>
> 如果我们用程序设计的语言来描述这个鸡和鸵鸟的关系问题，首先有一个类叫做“鸟”，它具有一些成员变量和方法，从而阐述了鸟所应该具有的特征和行为。然后一个“鸡”类可以从这个“鸟”类派生出来，它同样也具有“鸟”类所有的成员变量和方法，然后再加上自己特有的成员变量和方法。无论是从“鸟”那里继承来的变量和方法，还是它自己加上的，都是它的变量和方法。
>
> 本周的学习，建议按照视频中的操作，自己把媒体资料库从头到尾实现一遍，重点在于弄清楚子类和父类的关系，理解子类从父类继承得到了什么。

Java的继承只允许单继承，即一个类只能有一个父类。在继承时获得全部，相当于把父类代码自动复制过来了；

super()：作为调用父类构造器，必须在子类构造函数的第一行，只能调用一次；

##### 2. 多态

###### 多态变量

> **一个变量可以保存其所声明的类型或该类型的任何子类型。**
>
> Java中全部的**对象变量**都是多态变量。“多态”这个术语(字面意思是许多形态)是指一个变量可以保存不同类型(即其声明的类型或任何子类型)的对象。所以：每个对象变量都有两个类型，1.声明类型 2.动态类型；
>
> 

子类的对象可以被当作父类的变量来用：赋值、传参、放进容器（都是传地址，即“管理权”）；（类：类型）

###### “向上造型”

：把子类对象赋给其父类变量；

```java
// DoME的例子中
// 1. 赋值
Item a = new Item(); // 正确
Item a = new CD();   // 正确 a的类型(声明类型)：Item，a中保存的对象的类型(动态类型)：CD

// 2. 传参
add(Item item);
add(new CD());
add(new DVD()); // 也能接收，此时item传入的就是CD DVD类型的参数，也就是多态变量的一个简单实现；

// 3. 放进容器


// “向上造型”
        Item item = new Item("a",0,true,"...");
        CD cd = new CD("abc",0,true,"...","xxx",10);

        // Error
        // cd = item;      直接反向，会报错，是不安全的
        // cd = (CD)item;  运行报错，无法强制把Item类型转换为CD

        item = cd; // 现在让item的 声明类型=Item，动态类型改为=CD，item内实际存储管理一个cd
        // CD cc = item;   现在理论上是正确的，但编译器绕不过来，所以手动cast一下
        CD cc = (CD)item;  // 其实现在 item：声明类型=动态类型=CD，再传进CD cc，就没有问题了
```

###### "造型"与“强制类型转换”的区别

> 1. 强制类型转换：真的转换，(int)10.23，是真的把10.23->10；
> 2. cast：不真的转换，只是把Item对象当作CD对象来看待；

###### Override Overload

> 1. Override：继承概念，重写继承自父类的method，name、parapeters均相同；
>
>    “覆盖关系”：重写后不代表父类中的方法已经不存在了，而是当通过子类的对象调用这个方法时，子类中的方法取代了父类的方法，父类的这个方法被“覆盖”起来而看不见了。而当通过父类的对象调用这个方法时，实际上执行的仍然是父类中的这个方法。
>
> 2. Overload：重载，多个name相同，但parameters不同的methods；

###### “静/动态绑定”的method调用

> 1. 早绑定，又称静态绑定，这种绑定在编译的时候就确定了；
>
> 2. 晚绑定，即动态绑定。动态绑定在运行的时候根据变量当时实际所指的对象的类型动态决定调用的方法。
>
>    Java缺省（默认）使用动态绑定。

###### 单根的类型系统的Root：Object

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230406202942510.png" alt="image-20230406202942510" style="zoom:45%;" />

> 除了C++，几乎所有的OOP Language都是这样的单根系统，所有的Class默认继承自Object类，且如果直接继承自Object，不需要加extends;

###### Object中的函数：toString() equals()

1. toString()

：是Object类自带的一个函数，帮助以某种形式输出内容，@Override修改toString()后，**无需手动使用**，会在sout(name)时自动调用toString()内的输出格式输出；

```java
// toString - 通过generate可以勾选生成基础格式 这里Override的是Object类中的toString()
@Override
public String toString() {
    return "CD{" +
            "artist='" + artist + '\'' +
            ", numofTracks=" + numofTracks +
            ", title='" + title + '\'' +
            '}';
}
```

2. equals()

：object1.equals(object2) == true/false;	判断两个管理者是否管理同一个对象（被管理的地址是否是同一个）；

```java
// equals - 通过generate可以勾选生成基础格式 
@Override
public boolean equals(Object o) { // 用Object类型接收的实际CD对象
    // Situation 1：管理同一个地址
    if (this == o) return true;
    // Situation 2：o不是CD及其子类的实例
    if (!(o instanceof CD)) return false;
    // Situation 3：非上述情况，不确定，需要进一步对比字段
    CD cd = (CD) o; // 所以这里需要"造型" 恢复原始类型
    return numofTracks == cd.numofTracks && artist.equals(cd.artist); // 对比非空字段
}
```



### S3. 设计原则

> Castle代码里出现的问题（Problem List）：
>
> 1. 代码冗余，多处复制；
>
> 2. 程序耦合度紧，可扩展性差；
> 3. 所有成员变量都是public
>
> 
>
> Solution：消除代码复制，通过两个手段：1 函数 2 父类
>



##### 评估设计的优劣

“程序设计的目标：是一系列通过定义明确的借口通信来协同工作的类；”

耦合：反映了类之间联系的紧密程度；我们努力要获得“松耦合 loose coupling”，也就是低的耦合程度；

​			松耦合的系统中，常常可以修改一个类，但同时不会修改其他类，而且 整个程序还可以正常运作；

​			紧耦合的系统中，对一个类的修改也会导致 对其他一些类的修改，一一加以修改，是一件既困难又费时的事情；

聚合：与程序中一个单独的单元所承担的任务的数量和种类相对应有关；

​			类、方法，在理想的情况下，一个代码单元应该负责一个聚合的任务，成为一个逻辑单元。

​			类：应该代表一个类型的实体；

​			方法：应该实现一个逻辑操作；

聚合背后的“重用”理论：如果一个方法或类是只负责一件定义明确的事情，那么就很有可能在 另外不同的上下文环境中使用。遵循这个理论的一个额外的好处是，当程序某部分的代码需要 改变时，在某个代码单元中很可能会找到所有需要改变的相关代码段。

“可以运行的代码!=好的代码，在维护的时候就可以轻易看出代码质量！！！”



##### 措施

###### 1. 用封装来降低耦合：

​		把所有成员变量都改成private/protected；

​		然后根据“标红的”外界的实际需要，让被访问类本身提供一些劳动（methods）；

###### 2. 增加可扩展性：

​		用接口来实现聚合：“给Room类实现的新方法，维持接口，把方法的细节彻底隐藏在类的内部，今后关于方向的如何实现就和外部无关了” ；

​		减少“硬编码”，用容器来实现灵活性；

​		从程序中识别出框架和数据，以代码实现框架，将部分功能以数据的方式加载，这样能在很大程度上实现可扩展性。

###### + 通过Handler+ HashMap<String, Handler> 实现解析用户命令，通过String->对应函数的动态连接！！！

-这个好牛！这里贴上关于handler设置的核心代码

```java
// Game
class Game{
  // 关于handler的成员变量
  private HashMap<String,Handler> handlers = new HashMap<>();
  
  // constructor中添加关于handlers的设置
 	public Game(){
  	// 初始化 handlers 把对应的子类对象赋进去 
    handlers.put<"bye",new HandlerBye(this)>;
    handlers.put<"help",new HandlerHelp(this)>;
    handlers.put<"go",new HandlerGo(this)>;
 	  // 调用相关函数 初始化 game
  }
  
  // 使用handlers动态调控
  public void play(){
    	Scanner in = new Scanner(System.in);
      while ( true ) {
          String line = in.nextLine();
          String[] words = line.split(" "); // TODO：如何解析用户输入的命令，根据解析出的不同的字符串，转到对应的函数？

          Handler handler = handlers.get(words[0]); // 根据命令动态分配handler

          String value = "";
          if (words.length>1){
              value = words[1];
          }
          if (handler!=null){ // 已经根据输入""找到对应handler
              handler.doCmd(value);
              if (handler.isBye()) break;
          }
      }
   }
}
 
// Handler  
class Handler{
  	// 把Game传进来让子类继承，有需要的子类直接使用Game内数据，没需要的子类就继承放着也没什么
  	protected Game game;
  	// constructor
  	public Handler(Game game){
    		this.game = game;
    }
  	// 
  	public void docmd(String word){}
  
  	public boolean isBye(){return false;} // 默认false
}

// 以下均从Handler继承了全部的内容
// HandlerGo 需要Game的内容
class HandlerGo extends Handler{
  	// constructor
    public HandlerGo(Game game) {
        super(game);
    }
    @Override
    public void doCmd(String cmd) {
        game.goRoom(cmd); // 转移到这的所需内容太多了，还是放在Game类里
    }
}

// HandlerBye
class HandlerBye extends Handler{
    // constructor
  	public HandlerBye(Game game) {
        super(game);
    }
  	@Override
  	public void docmd(String word){
      	System.out.println("感谢您的光临。再见！");
    }
  	@Override
  	public boolean isBye(){return true;} // 用来判断结束break；
}

// HandlerHelp
class HandlerHelp extends Handler{
  	// constructor
    public HandlerHelp(Game game) {
        super(game);
    }
    @Override
    public void doCmd(String cmd) {
        System.out.println("迷路了吗？你可以做的命令有：go bye help");
        System.out.println("如：go east");
    }
}
```





### S4. 抽象与接口

##### 抽象 概念

> Shape类表达的是一种概念，一种共同属性的抽象集合，我们并不希望任何Shape类的对象会被创建出来。那么，我们就应该把这个Shape类定义为抽象的。我们用abstract关键字来定义抽象类。抽象类的作用仅仅是表达接口，而不是具体的实现细节。抽象类中可以存在抽象方法。抽象方法也是使用abstract关键字来修饰。抽象的方法是不完全的，它只是一个方法签名而完全没有方法体。
>
> 
>
> 如果一个类有了一个抽象的方法，这个类就必须声明为抽象类。如果父类是抽象类，那么子类必须覆盖所有在父类中的抽象方法，否则子类也成为一个抽象类。一个抽象类可以没有任何抽象方法，所有的方法都有方法体，但是整个类是抽象的。设计这样的抽象类主要是为了防止制造它的对象出来。
>
> 
>
> 两种抽象的概念：1.与具体相对，表示一种概念，而非实体（这里） 2.与细节相对，表示一定程度上忽略细节而着眼大局；



##### 设计：数据与表现分离

###### 1. 程序的业务逻辑与表现无关

表现可以是图形的，也可以是文本的；可以是当地的，也可以是远程的；

###### 2. 表现层与数据层的关系

一旦数据更新，通知表现层重新更新所有画面。这样不需要设计哪个局部需要更新，简化了程序逻辑，因为计算机运算速度提高了得以实现的；

###### 3. 责任驱动的设计

将程序要实现的功能，分配到合适的类/对象中，是设计中非常重要的一环；

每一个部分，都只做自己最擅长的部分。



##### abstract 抽象类/抽象函数

> 抽象函数：表达概念而无法实现具体代码的函数		
>
> 抽象类：表达概念而无法构造出实体的类
>
> 
>
> - 抽象类不能制造对象，但可以定义变量（作为类型），任何继承了该抽象类的非抽象类对象，可以赋值给这个变量来管理。
> - 继承自抽象类的子类，必须Override父类中的所有抽象函数，否则这个类自己成为抽象类。

```java
public abstract class Shape{							// 有抽象函数的类必须是抽象类
		public abstract void draw(Graphic g); // 没有{}
}
```



##### interface 接口 / implements 实现

> 接口是纯抽象类
>
> ​		所有成员函数都是抽象函数
>
> ​		所有成员变量都是 public static final

> 接口的实现类
>
> ​		类用extends，接口用implements
>
> ​		**类**可以实现很多**接口**，也可以同时继承一个**类**
>
> ​		接口不能实现接口
>
> “接口规定了长什么样，但不管里面有什么”





### S5. IOC 和 MVC

#### 控制反转机制(IOC, Inversion of Control)

有一个听起来很复杂的名字，但其实：

使用Swing的消息机制，通过写内部类/匿名类，设置ActionListener，并编写事件触发后的处理逻辑，完成一个事件响应的闭环。

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230518163723651.png" alt="image-20230518163723651" style="zoom:80%;" />

eg. 在已有代码 FoxAndRabbit上，**添加 "单步功能"：每点击以下，进行下一步操作**

```java
        // + 
        JButton btnStep = new JButton("next step");
        frame.add(btnStep, BorderLayout.NORTH);
        // ⬇以下是一个匿名类，可以改写为内部类
        btnStep.addActionListener(new ActionListener() {
           @Override
           public void actionPerformed(ActionEvent e) {
              System.out.println("Click!");
              step();
              theView.repaint();
           }
        });*/

          
        // 内部类写法
        // ActionListener 内部类 作为大类的成员变量
        private class StepAction implements ActionListener{
            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println("Click!");
                step();            // 如果这个类不是内部类，就无法直接使用step和repaint！
                frame.repaint();
            }
        }
        // 使用
        btnStep.addActionListener(new StepAction());
```

使用到的知识：

> 1.布局管理器 Layout Manager
>
> 需要有一种能更通用地指定布局的方法,比如,要求“这个部件应该在那个部件的下面“或者”这个部件在窗口改变尺寸时能自动拉伸,但是其他部件保持尺寸不变”。这些可以通过布局管理器(layout manager)来实现。Layout Manager会帮助计算一个灵活的显示效果（虽然不一定好看），但可以自适应不同的环境。
>
> <img src="/Users/lby/Library/Application Support/typora-user-images/image-20230518200755806.png" alt="image-20230518200755806" style="zoom:30%;" />
>
> 2.事件处理：用来响应用户的Input的技术
>
> Java类库处理这类事情的模型是基于事件的。 如果用户激活了一个部件(比如,点击按钮或者选择菜单项),系统就会产生一个事件。应用 程序可以收到关于这个事件的通知(以程序的一个方法被调用的方式),然后就可以采取程序该做的动作了。
>
> 3.Swing
>
> Swing使用一个非常灵活的：事件监听器的事件处理(event handling)模型，处理GUI的输入。
>
> Swing框架本身以及大部分部件在发生一些情况时会触发相关的事件,而其他的对象也许会对这些事件感兴趣。不同类型的动作会导致不同类型的事件。当点击一个按钮或选中一个菜单项,部件就会触发动作事件;而当点击或移动鼠标时,会触发鼠标事件;当框架被关闭或最小化时,会触发窗口事件。另外还有许多种其他事件。
>
> 所有的对象都可以成为任何这些事件的监听器,而**一旦成为监听器,就可以得到这些事件触发的通知**。
>
> 实现了众多监听器接口之一的对象就成为一个事件监听器。如果对象实现了恰当的接口, 就可以注册到它想监听的组件上。
>
> 4.内部类
>
> 定义在别的类/函数内部的类，**内部类可以直接访问外部的全部资源（包括private的资源）**，但当外部是函数时，内部类只能访问函数里final的变量。
>
> 5.匿名类
>
> 在new对象的时候给出的类，形成了匿名类，匿名类可以继承也可以实现其他类/接口，Swing的消息机制广泛使用匿名类，**好处是不用起那么多名字！**



#### MVC设计模式=(Model,View,Control)

> Model 模型：保存和维护数据，提供接口让外部修改数据，通知View有改动需要刷新；
>
> View    表现：从Model获得数据，很单纯的把所有数据全部画一遍，不用揪细节；
>
> Control 控制：从用户得到**输入**，根据输入调整数据（事件响应）；

值得注意的是：Control和View完全无关，从而大大简化了逻辑！

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230518202645297.png" alt="image-20230518202645297" style="zoom:40%;" />





### S6. 异常

：有不同寻常的事情发生了，当这个事情发生的时候，原本打算要继续做的事情不能再继续了，必须要停下来处理；

​    希望我们的程序能够对程序运行中的各种情况进行处理，不至于崩溃；

##### 捕捉异常 try - catch - throw

```java
public class CatchException {

    public static void main(String[] args) {

        int[] a = new int[10];

        int idx;
        Scanner scan = new Scanner(System.in);
        idx = scan.nextInt();

        try {
            // 可能出现异常的语句 这里：数组访问越界
            a[idx] = 10;
            System.out.println("a[ "+idx+" ] = "+a[idx]);
        } catch (ArrayIndexOutOfBoundsException e) {
            // 如果捕捉到异常的处理语句，具体的异常处理逻辑取决于业务需要
            System.out.println("Exception Caught:"); // 捕捉到的异常：
            System.out.println(e.getMessage());      // 12
            System.out.println(e);                   // java.lang.ArrayIndexOutOfBoundsException: 12
            e.printStackTrace();                     // java.lang.ArrayIndexOutOfBoundsException: 12 at ooad_ZheJiangUniversity.w8_exception.CatchException.main(CatchException.java:17)
            
          	// Throw
            // 如果在这个层面上需要处理，但不能做最终的决定： 再度抛出，就需要再有一个try catch来捕捉这个异常了！
          	// 其中 可以 throw的异常 是属于Throwable类的异常
            System.err.println("An exception was thrown"); // 红色文字
            throw e;

        } // catch ( Exception2 e ) { }
          // 可以通过多个catch种类，捕捉try中语句的多种可能异常
					// ...
    }
}
```

##### 异常捕捉机制 图解

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230412193050024.png" alt="image-20230412193050024" style="zoom:67%;" />

优点：异常机制最大的好处就是清晰的分开了，正常业务逻辑代码，和遇到情况时的处理代码；

捕捉：抛出的子类异常会被其父类的异常捕捉器catch到；



##### 一个流程的异常处理 while(1)

> 如果要读文件：
>
> 	1. 打开文件；
> 	1. 判断文件大小；
> 	1. 分配足够的内存空间；
> 	1. 把文件读入内存；
> 	1. 关闭文件；

```java
while(true){
  	try{
      // 业务逻辑：调用整个业务逻辑完整的所有函数
      
    }catch(Exception e){
      // 错误处理代码：流程内可能出现的所有种类的异常
      do something;
    }catch(Exception2 e){
      do something;
    }
} // 处理好异常，要继续往下做，靠外面的while循环回来；
```



##### 抛出异常 throws

```java
public static void read() {
    if (open()==-1){
        throw new OpenDataException(); // 这句报错
    }
}
```

两个处理方法：

1. try catch 环绕

```java
public static void main(String[] args) {
    read();
}

public static int open(){
    return -1;
}

public static void read() { 
    if (open()==-1){
        try {
              throw new OpenDataException();
        } catch (OpenDataException e) {
              e.printStackTrace();
        }           
    }
}
```

2. 添加throws到方法签名

```java
public static void main(String[] args) {
       // 调用此方法的地方要做好处理异常的准备（也是两个处理方法）
       try {
           read();
       } catch (OpenDataException e) {
           e.printStackTrace();
       }
}

public static int open(){
       return -1;
}

public static void read() throws OpenDataException,NullPointerException { 
  	// 声明我的函数会抛出这个异常，可以声明不会抛出的异常（没关系的），让接收异常的地方提前做好准备
    if (open()==-1){
        throw new OpenDataException();
        // 处理：添加throws到方法签名；
        //      调用此方法的地方要做好处理异常的准备
    }
}
```



##### 运行时刻异常不需要声明

：像ArrayIndexOutOfBoundsException这样的异常是不需要声明的，但是如果没有适当的机制来捕捉，就会最终导致程序终止；



##### 异常声明+继承关系

> 成员函数：当Override一个函数的时候，子类不能声明抛出比父类版本更多的异常；
>
> 构造函数：在子类的构造函数中，必须声明父类可能抛出的全部异常，可以再加上自己可能抛出的异常的声明；















### S7. 流：处理I/O的手段

#### Byte层面的I/O

##### 1. I/O流 InputStream / OutputStream

：Stream是单方向/一维的，**所有的I/O操作，一定都是带Exception处理的**；只能做Byte字节层面的读写；

​	Java文档：http://docs.oracle.com/javase/8/docs/api/overview-summary.html 可看所有官方函数的功能；

```java
void byteIO(){
    System.out.println("hello byteIO");
    byte[] buffer = new byte[1024];
    // All I/O Need Exception Handling
    try{
        int len = System.in.read(buffer);
        String s = new String(buffer,0,len);
        System.out.println("读到了"+len+"字节");
        System.out.println(s);
        System.out.println("s的长度是："+s.length());
    } catch (IOException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
    }
}
```

> **所有InputStream包含的内容**
>
> read:	int read()	read(byte b[])	read(byte[], int off, int len)	// 字节层面的读和写
>
> skip(long n)
>
> int available()
>
> mark()	// 只能打一次，记住这个地方，不是所有的地方都能做mark的
>
> reset()	// 回到打mark的地方
>
> boolean markSupported()
>
> close()
>
> **所有OutputStream包含的内容**
>
> write:	write(int b)	write(byte b[])	write(byte[] b, int off, int len)
>
> flush()	// 保证把内容写到物理介质中去
>
> close()



##### 2. 文件流 FileInputStream / FileOutputStream

对文件进行读写操作，实际工程中已经较少使用，更常用的是：以内存数据 / 通信数据上建立的流，如数据库的二进制数据读写 或 网络端口通信；具体的文件读写往往有更专业的类，如配置文件和日志文件。

```java
FileOutputStream out = new FileOutputStream("a.dat"); // 创建了一个a.dat在java_learn目录下
out.write(buffer); // byte类型的数组名
out.close();
```



#### 基本数据类型层面的I/O

##### 3. 流过滤器

以一个介质流对象为基础，层层构建过滤器流，最终形成的流对象能在数据的I/O中，逐层使用过滤器流的方法来读写数据；

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230414201452939.png" alt="image-20230414201452939" style="zoom:100%;" />

> FileInputStream（Byte层面）BufferedInputStream（缓冲作用）DataInputStream（数据处理）	基本数据类型：嵌套过滤器
>

其中，DataInputStream DataOutputStream：把基本类型数据的内容以二进制的形式在文档中交换；

```java
// 通过使用多层过滤器嵌套
DataOutputStream out = new DataOutputStream(new BufferedOutputStream(new FileOutputStream("a.dat")));
int i = 1024;
out.writeInt(i); // 可以写int类型 以二进制形式写入到文件中
out.close();
System.out.println("已完成在a.dat的output");

DataInputStream in = new DataInputStream(new BufferedInputStream(new FileInputStream("a.dat")));
int j = in.readInt();
System.out.println(j);
```



#### *文本层面的I/O Reader / Writer

##### Writer

> PrintWriter	BufferedWriter	OutputStreamWriter	FileOutputStream("文件全名")	四个嵌套；

```java
// 在流上建立文本处理
// Writer
PrintWriter out = new PrintWriter(new BufferedWriter(new OutputStreamWriter(new FileOutputStream("a.txt"))));
int i = 1024; // int
out.println(i);
out.println("Hi Txt"); // 文本
out.close();
System.out.println("已完成在a.txt的output");
```

##### Reader

###### 1. BufferedReader

> BufferedReader	InputStreamReader	FileInputStream("文件全名")	三个嵌套；

```java
BufferedReader in = new BufferedReader(new InputStreamReader(new FileInputStream("/Users/lby/IdeaProjects/java_learn/src/ooad_ZheJiangUniversity/w9_stream/Main.java")));
String line;
while((line=in.readLine())!=null){ // 当读到文件末尾 in.readLine() return null 跳出循环
    System.out.println(line);      // 逐行输出文件内容
}
```

> **FileInputStream** 处理二进制01流，以字节为单位读取；- Byte层面

> **InputStreamReader** 是 Java 中一个用于读取字节流并将其转换为字符流的转换流（InputStream -> Reader），可以根据指定的编码方式读取字节流并转换成相应的字符流，提供了更加灵活、高效的读取方式。通常情况下，我们使用 InputStreamReader 读取字节流时，还需要配合使用 BufferedReader 对读取的内容进行缓存处理。
>
> InputStreamReader 的主要作用如下：
>
> 1. 将字节流转换为字符流：InputStreamReader 可以帮助我们将字节流（InputStream）中的数据转换成字符流（Reader）中的数据，方便我们进行文本内容的读取和处理操作。
> 2. 指定字符编码方式：InputStreamReader 可以指定读取字节流时所采用的字符编码方式，比如 UTF-8、GBK、ISO-8859-1 等编码方式，提高了读取和处理文本内容的灵活性和可靠性。
>
> 需要注意的是，当使用 InputStreamReader 读取字节流时，可能会出现乱码的问题，这是因为在进行转换时使用的编码方式与实际的编码方式不匹配导致的。因此，需要根据实际情况选择正确的编码方式进行读取，并且在使用完毕后及时关闭流资源。

> **BufferedReader** 是 Java 中一个用于读取文本内容的缓冲字符流，可以通过它一次读取一行文本内容，并将其存储到一个缓冲区中。相比直接使用 FileReader 或 InputStream 等基本的字节流读取器，BufferedReader 可以提供更高效的读取性能和更好的用户体验。
>
> 具体来说，BufferedReader 主要有以下作用：
>
> 1. 提供缓冲功能：可以使得读取内容更加高效，减少不必要的IO操作；
> 2. 逐行读取文本内容：提供 readLine() 方法，可以一次读取一行内容，简化了对文本内容的读取和处理操作；
> 3. 易于使用：只需要使用 BufferedReader 就能读取文本内容，并且可以方便地与其他 IO 类一起使用。
>
> 需要注意的是，由于 BufferedReader 是缓冲字符流，因此在使用时需要注意及时关闭流并刷新缓冲区，以避免内存泄漏等问题。



###### 2. LineNumberReader

> 通过getLineNumber()多了跟踪行号的功能，但不能帮助跳到想去的行数，setLineReader() 没有这个功能，只能设置当前行号；

```java
LineNumberReader in = new LineNumberReader(new InputStreamReader(new FileInputStream("/Users/lby/IdeaProjects/java_learn/src/ooad_ZheJiangUniversity/w9_stream/Main.java")));
String line;
// in.setLineNumber(3); 初始行号默认值为0
while((line=in.readLine())!=null){
    System.out.println(in.getLineNumber()+":"+line); // in.getlineNumber() 从1开始跟踪行号
}
```



###### 3. FileReader 

：所有方法继承自InputStreamReader，在用法上也代替InputStreamReader的位置，外面嵌套BufferedReader；

> 如果直接对一个“裸的”文件进行操作，FileReader很好用，但FileReader不能指定编码转换的方式；
>
> 构造函数：
>
> FileReader(File file);给定从中读取的File，构造FileReader
>
> FileReader(String filename);给定从中读取的File名，构造FileReader





##### 汉字编码 InputStreamReader(file,"utf8")

处理纯汉字的文件，就涉及到不同编码的问题，gbk是国标可以正常输出，uft8就会需要编码转换，不然显示的会是乱码；

在使用InputStreamReader()时，通过new调用不同的构造函数，按照第二个参数，处理第一个参数的输入流；

> InputStreamReader(InputStream in)	// 创建一个使用默认字符集的InputStreamReader；
>
> InputStreamReader(InputStream in, Charset cs)	// 创建一个使用给定字符集的InputStreamReader；
>
> InputStreamReader(InputStream in, String charsetName)	// 创建一个使用指定字符集的InputStreamReader；
>
> InputStreamReader(InputStream in, CharsetDecoder dec)	// 创建一个使用给定字符集解码器的InputStreamReader；

其中，字符解码器

> 字符集解码器是将一种字符集编码的二进制Byte字节序列转换成 Java 字符串的组件。
>
> 在 Java 中，对于不同的字符集编码方式，可以使用不同的解码器进行转换。常见的字符集包括 ASCII、ISO-8859-1、UTF-8、UTF-16 等。字符集解码器通过读取二进制输入流，将字节序列转换为字符序列，使得程序能够正常地读取和处理来自外部系统的文本数据。
>
> 例如，当处理一个中文文件时，如果文件采用了 UTF-8 编码，那么需要使用 UTF-8 解码器将文件转换为 Java 内部所使用的字符编码方式，才能正确处理其中的中文字符。
>
> 在 Java 中，可以使用 InputStreamReader 类来创建字符集解码器，并且它支持从文件、网络等各种输入流中读取数据并进行解码。通常情况下，使用该类时需指定相应的字符集编码方式，以便正确地进行字符集解码操作。



##### 格式化 Stream/Reader/Scanner

<img src="/Users/lby/Library/Application Support/typora-user-images/image-20230423203441661.png" alt="image-20230423203441661" style="zoom:40%;" />



##### 对象串行化

把对象整个的I/O进内存 stream里面，可以直接通过ObjectInputStream.readObject()和ObjectOutputStream.writeObject()实现；

这个对象所在类，必须实现一个接口：eg. class Student implements Serializable{};

ObjectInputStream/ ObjectOutputStream是可以直接操作Object的I/O流，但必须建立在某个实体的流上，eg. FileOutputStream；

```java
    // Output
    Student s1 = new Student(1, "LiMing");
    System.out.println("Standard Output: "+s1);

    try {
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("a.dat"));
        out.writeObject(s1);
        out.close();
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Input
    try {
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("a.dat"));
        Student s2 = (Student) in.readObject(); // Student-Object 因为现在直到读入的一定是Student对象
        System.out.println("ReadObject from FileInputStream："+s2);
        in.close();
        // 内容一样 但不是同一个对象
        System.out.println(s1==s2);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    }
```





##### Stream的应用

现在已经很少用流来读入一个文件，更多的场景是：通过其他方式获得了一个流，然后在这个流的基础上做一些事情：eg. socket中server接收client的I/O Stream（见S9）

###### 阻塞/非阻塞

> read()函数是阻塞的，在读到所需的内容之前会停下来等，使用read()的更高级的函数，如nextInt(),readLine()都是这样的；
>
> 所以常用单独的线程来做 socket 读 的等待，或使用nio的channel选择机制
>
> 对于socket，可以设置SO时间：setSOTimeout(int timeOut)

###### NIO的channel选择机制（多线程补充

> NIO（Non-blocking I/O）中的Channel选择机制主要是基于事件驱动的，即当一个或多个Channel发生了一个或多个事件时，就会触发相应的回调函数来处理这些事件。在Java NIO中，可以使用Selector类实现多路复用，通过调用Selector.select()方法来等待已注册的Channel中是否有事件发生，如果有则进行处理。同时，也可以使用register()方法将某个Channel注册到Selector中，并指定它感兴趣的事件类型（如读、写、连接等），当该事件类型发生时，就会通知相应的线程进行处理。





### S9. Socket 网络编程

Socket是一种编程方式！

![image-20230425195929224](/Users/lby/Library/Application Support/typora-user-images/image-20230425195929224.png)

> Socket 编程一般包括以下步骤：
>
> 1. 创建服务器端套接字对象（ServerSocket）并绑定监听端口。
> 2. 调用 ServerSocket 的 accept() 方法等待客户端连接请求。
> 3. 创建客户端套接字对象（Socket），并使用其 connect() 方法向服务器端发出连接请求。
> 4. 服务器端接收到客户端连接请求后，调用 accept() 方法接收客户端的连接，并返回代表与客户端通信的 Socket 对象。
> 5. 客户端和服务器端通过 Socket 对象进行数据传输和接收。
>
> 需要注意的是，在进行 Socket 编程时，需要根据需求选择相应的协议和参数设置，以确保数据传输的稳定性和安全性。



















# FAQ列表

###### 1. scanner输入手动EOF问题

```java
        Scanner scan = new Scanner(System.in);
				// 在无限循环中
        while(true){
            String c= scan.next();				// 1. 接收字符串
            if (c.matches("###")) break;	// 2. 判断 EOF 则 break 跳出循环
            else citys.add(c);						// 3. 符合操作条件 正常往下走
        }
        scan.close();

// review: 被 while(!hasNextLine) 先入为主限制了思路
```



###### 2. scanner.close()

> 如果后面还要用 scanner输入，不要调用 .close()函数！关掉后再次使用会空指针报错！
>
> 因为使用close()关掉之后将无法再使用Scanner，即使再次声明了一个Scanner对象。



































