## Java 程序执行过程

1. 源文件（.java），通过编译器 compiler（javac 命令），生成字节码文件（.class）；
2. 字节码文件（.class），通过解释器 interpreter（java 命令），运行代码；

## **JDK** 介绍

Java Development Kit，Java 语言的软件开发工具包（也就是一堆工具命令），主要包含 javac、java 两个组件。

## **JRE** 介绍

Java Runtime Environment，包含 Java 虚拟机（JVM）、Java 核心类库和支持文件。

## JDK 和 JRE 的”联系与区别“

- JDK = JRE + 开发工具集（例如 javac 编译命令）
  - （作为开发者）如果要开发 Java 软件，需要下载 JDK；
- JRE = JVM + JavaSE 标准类库
  - （作为用户）如果只是运行 Java 程序，只需要下载 JRE；
- JDK 包含 JRE，JRE 又包含 JVM；

## Java 平台

- Java SE：Java 标准版，桌面程序；
- Java EE：Java 企业版，Web 程序；
- Java ME：Java 微型版，移动设备（基本没有份额了）；

## 数据类型

- 基本数据类型

  - 数值型

    整数型（byte、short、int、long）

    浮点类型（float、double）

  - 字符型（char）

    单引号，中间只能是一个字符或者是 Unicode 编码；或者是代表 ASCII 码的数字；

    char a = ‘a’;

    char a = 65; // 对应字符为 A

    char a = 65535; // 2 个字节表示，表示数据范围为 0 ~ 65535

    char a = '\u005d';

    Unicode 是 \u 开头，后跟四位十六进制；

    char 能自动转换为 int（类型数据范围小的，能自动向类型大的做自动转换）（只有数字才能这样转换？）；

  布尔型（boolean）

- 引用数据类型

  类（class）

  接口（interface）

  - 数组

    声明方式有两种：

    - int[] a; 更常用！
    - int a[];

    创建方式有两种：

    - a = new int[10]; 此时取 a[1] = 0; 有默认赋值！
    - a = new int{1, 2, 3}; 数组的长度就是初始化时的个数；

    二维数组的声明：

    - `int[][] a = new int[2][3];`
    - 可以只指定行数：`int[][] a = new int[2][];`  `a[0] = new int[1];` `a[1] = new int[2];`

任何类型的数据，没有默认初始化值！**在未赋值的情况下使用，就会编译失败！**（类的成员属性都是有初始值的，例如 int 的值为 0，String 的值为 null）

## 进制表示

- 八进制：0开头即可：037、056；
- 十六进制：0x 或 0X 开头：0x12、0XABCFF；

## & 与 && 的区别

- && 叫做短路运算符，前面为 true，则后面式子不执行；
- & 无论前面结果，后面都会执行（不常用）；

## 对象实例化（创建）

- 声明对象 Cat cat; 是在“栈”空间开辟一块空间；
- 实例化对象 cat = new Cat(); 是在“堆”空间开辟一块空间；

## 栈内存 & 堆内存

- 栈存放局部变量；int a = 10; Cat cat;
- 堆存放动态数据；cat = new Cat();

## 导入包

- import 包名.类名;（建议采用，因为只加载特定类，加载速度快）
- 导入包中全部的类：import com.xx.*;
- 导入包中指定类：import com.xx.animal.Cat;
- import 只能访问指定包名下的类，无法跨级；import xx 无法间接导入 animal 文件夹下的 Cat；

## static

- 类对象共享（所有的 Cat 的 price 共用一份内存空间）；
- 类加载时产生，销毁时释放（所有 Cat 对象都销毁后），生命周期长；
- static + 属性 = 静态属性; static + 方法 = 静态方法（只能继承，不能被子类重写）; static + 类 = 语法不存在; static + 方法内局部变量 = 语法不存在；
- 在 static 方法中，**不能直接访问非静态成员**，只能直接调用非静态成员；（但可以在方法中通过实例化 new Cat().eat(); 间接调用）

```java
Cat cat1 = new Cat();
cat1.price = 100; // 可以通过对象的方法访问静态变量
Cat cat2 = new Cat();
cat2.price = 200;

Cat.price = 300; // 类.静态成员（推荐）

// 最后所有猫的价格都是 300;
```

## 代码块

- 通过 {}  可以形成代码块；（变量的作用域就只在这个块内）
- 方法内的代码块被称为：普通代码块；
- **类内的代码块**被称为：构造代码块；（每次 new Cat() 就会调用一次）
- **static + 构造代码块**：静态代码块；（只会在第一次 new Cat() 调用一次）

## 访问修饰符（属性、方法）

| 访问修饰符 | 本类 | 同包（文件夹） | 子类 | 其他 |
| ---------- | ---- | -------------- | ---- | ---- |
| private    | ✅    |                |      |      |
| 默认       | ✅    | ✅              |      |      |
| protected  | ✅    | ✅              | ✅    |      |
| public     | ✅    | ✅              | ✅    | ✅    |

- protected：例如有两个类，分别为 Animal 和 Test：
  - 子类 Cat 无论是在同包还是不同包（跨包），都可以正常访问；
  - 在同包的情况下，Test 里可以 new Animal().xx 访问父类 protected 的成员；
  - 在不同包，则会报错；
- 默认：
  - 同包子类能访问；同包 Test 可以访问；
  - 跨包子类不能访问；跨 Test 子类不能访问；

## final

- 如果用来修饰类，则表示**不允许被继承（终点）**；
- 如果用来修饰方法，表示不允许被子类重写；
  - 但是可以被继承；
  - 不能用 final 修饰构方法；
- 修饰类中成员属性，也表示不允许修改（必须要在定义时赋值 private final int age = 1; 或者构造代码块、方法中赋值，不能在其他方法中赋值...）；
- 修饰普通变量，表示不允许修改（**使用前**赋值即可，不一定非要在声明时赋值）（final 针对当前类；private 来阻止子类访问）；
- 配合 static 使用，变成常量；只被初始化一次，提高性能；

## 注解

- JDK 1.5 版本引入的一个特性；
- 按照运行机制分类：
  - 源码注解、编译时注解、运行时注解；
- 按照来源分类：
  - 来自 JDK 的注解；
  - 来自第三方的注解；
  - 我们自己定义的注解；
- 元注解；

## 单例模式（Singleton）

- 目的：使得类的一个对象成为该类系统中的唯一实例；
- 定义：一个类有且仅有一个实例，并且**自行**实例化想整个系统提供；
- 实现：
  - 只提供“私有的构造方法”；
  - 含有一个该类的“静态私有对象”；
  - 提供一个“静态的公有方法”用于创建、获取静态私有对象；
- 代码方案：饿汉式 & 懒汉式；
- 优点：
  1. 在内存中只有一个对象，节省内存空间；
  2. 避免频繁创建销毁对象，提高性能；
  3. 避免对共享资源的多重占用；
- 缺点：
  1. 扩展比较困难；
  2. 如果实例化后的对象长期不利用，系统默认为垃圾进行回收，造成对象状态丢失；

```java
package com.singleton;

// 饿汉式：创建对象实例的时候直接初始化；空间换时间；（线程安全）
public class SingletonOne {
  // 1. 私有的构造函数
  private SingletonOne(String name) {
    System.out.println("hello world" + "--------" + name + this.testCount); // hello world--------'instance'1
  }
  // 2. 创建该类型的私有静态实例
  private static SingletonOne instance = new SingletonOne("'instance'");
  private int testCount = 1;
	// 静态属性初始的执行顺序是什么？？？为什么 SingletonOne.CONSTANT 没有执行 new，而 speak 执行了 new
	public static final int CONSTANT = 2;

  // 3. 创建公有静态方法返回静态实例对象
  public static SingletonOne getInstance() {
    return instance;
  }

  // 如果只调用 speak（不调用 getInstance），同样会初执行 new SingletonOne
  public static void speak() {
    System.out.println("speak");
  }
}

// hello world--------'instance'1 先打印，说明按顺序先执行静态变量赋值，再调用静态方法
// speak
package com.singleton;

// 懒汉式：时间换空间（存在线程风险）
public class SingletonTwo {
  private SingletonTwo() {

  }

  private static SingletonTwo instance = null;

  // 只有调用这个方法才会执行初始化操作
  private static SingletonTwo getInstance() {
    if (instance == null) {
      instance = new SingletonTwo();
    }
    return instance;
  }
}
```

## 多态——继承

- 分类：
  - 编译时多态（设计时多态）：方法重载
  - 运行时多态：一个引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在程序运行时才能决定（平常说的多态更多指这个）；
- 必要条件：满足继承关系，且分类指向子类对象；
- 转型分类：
  - 向上转型（Upcast）：将子类转换为父类 Animal x = new Cat()（自动、隐式）；
  - 向下转型（Downcast）：将父类转换为子类 Cat c = (Cat)x（强制类型转换，否则不能调用子类专属方法）；
  - x instanceof Cat：避免强制类型转换的安全问题，提高健壮性；

abstract：

- abstract class Animal: {} 不能直接实例化 new Animal()；
- abstract void eat(); 抽象方法只要定义，不需要实现，必须被子类实现，除非抽象类；
- abstract 不能与 static、final、private 共存；

## 多态——接口语法规则

- 接口及接口成员默认的访问权限为：public 或者 默认；
- 常量默认添加 static final 关键字；
- 抽象方法默认添加 abstract 关键字（都是 abstract，不需要添加）；
- 只有 default 方法和 static 方法可以添加方法体；
- 当一个类同时实现多个接口，且其中同时具有相同的方法时，实现类必须重写该方法，否则会编译报错；

## 内部类

- 作用：内部类提供了更好的封装，可以把内部类隐藏在外部类之内，不允许*同一个包中的其他类*访问该类，更好的实现了信息的隐藏；

- 分类：

  - 成员内部类；

    - 内部类相当于外部类的一个成员变量，可以用任意修饰符；
    - 内部类中定义的方法可以直接访问外部类中的数据，而不受访问控制符的影响；
    - 需要通过外部类对象来创建内部类实例：new Person().new Heart();
    - 如果外部类和内部类具有相同的成员，内部类默认优先访问自己的成员；可以通过 Person.this.age 和 [Person.name](http://Person.name) 访问外部类成员；
    - 编译后产生：外部类$内部类.class；

    ```java
    public class Person {
      private int age = 1;
      public static String name = "person";
    
      class Heart {
        private int age = 2;
        public void beat() {
          System.out.println(age + "---" + Person.this.age + "---" + Person.name);
        }
      }
    }
    
    // beat 输出：2---1---person
    ```

  - 静态内部类；

    - 静态内部类对象可以不依赖外部类对象直接创建 new Person.Heart()；
    - 静态内部类不能直接访问类的非静态成员，但可以通过“外部类**对象**.成员”的方式访问；

  - 方法内部类；

    - 定义在方法内部：void heartBeat() { class Heart{} new Heart().beat(); }
    - 方法内部类不能使用访问控制符和 static 修饰符，但可以使用 final 和 abstract 修饰；
    - 编译后产生：外部类$数字.class；

  - 匿名内部类；

    - 如果某个类的实例只用一次，则可以将类的定义与创建放到一起完成；
    - 不能有构造方法，可以通过构造代码块实现数据初始化；
    - 不能定义任何静态成员、静态方法；
    - 不能使用 public、protected、private、static、abstract、final 修饰；
    - 一个匿名内部类一定是在 new 的后面，用其隐含实现一个接口或继承一个类，但二者不能兼得；new Person(){ 重写抽象方法 };
    - 编译后产生：Outter$1.class；

## Java 异常

### 异常分类

- Throwable（根类）
  - Error：例如 VirtualMachineError（虚拟机错误）、OutOfMemoryError（内存溢出）、ThreadDeath（线程死锁）；这些错误我们无法解决；
  - Exception：
    - RuntimeException：例如 NullPointerException（空指针异常）、ArrayIndexOutOfBoundsException（数组下标越界异常）、ArithmeticException（算数异常）、ClassCastException（类型转换异常）；这个是非检查异常，即使方法通过 throws 抛出，编译器不会报错，因为不是必须要处理；
    
    - Checked Exception（检查异常）：编译器要求必须处置的异常，例如 IOException、SQLException；
    
    - ```java
      public static int test() throws ArithmeticException {} // 如果外部调用，因为是非检查异常，编译器不做下标红波浪线提示
      public static int test() throws Exception {} // 如果外部调用，因为 Exception 包含 Checked Exception，则编译器会爆红提示
      ```

### 异常关键字

- 捕获异常：try、catch、finally；
  - finally 备注：
    - finally 一定执行；即使 catch 里面有 return 照样执行，执行完了再 return catch 里的结果；如果 finally 有 return，则 return finally 里的结果；
    - 有特例：System.exit(1) 终止程序运行；
  
- 声明异常：throws；

- 手动抛出异常：throw：

  - 通过 try...catch 包含 throw 语句——自己抛出自己处理；

  - 通过 throws 在方法声明中抛出异常类型——谁调用谁处理——调用者可以自己处理，也可以继续上抛；

  - ```java
    public static void testAge() {
      try {
        throw new Exception("18 岁以下");
      } catch(Exception e) {
        e.printStackTrace();
      }
    }
    
    public static void testAge() throws Exception {
      throw new Exception("18 岁以下");
    }
    
    // 最好不要抛非检查异常类型，例如 ArithmeticException，约束性不大，编译器不会自动爆红；
    ```

## 包装类

- 包装类 <-装箱/拆箱-> 基本数据类型
- 基本数据类型 <--> 字符串

```java
public static void main(String[] args) {
  Integer one = new Integer(100);
  Integer two = new Integer(100);
  System.out.println(one == two); // 1. false，因为两个对象地址不同
  
  Integer three = 100;
  // Integer three = Integer.valueOf(100);
  System.out.println(three == 100); // 2. true，因为自动拆箱
  
  Integer four = 100;
   // Integer four = Integer.valueOf(100);
  System.out.println(four == three); // 3. true，不同于 1，因为指向同一个地址
  
  Integer five = 200;
  System.out.println(five == 200); // 4. true，同 2
  
  Integer six = 200;
  System.out.println(six == five); // 5. false，不同于 3，因为大于 127
}

// 当编译器执行到 Integer four = 100 的时候，实际执行的是 Integer four = Integer.valueOf(100);
// 如此提高运行效率；
// 这个 100 被存储在常量缓存区（或者叫对象池）——参数 >= -128 && 参数 <= 127，则会被放置在里面；
// 如果有这个对象则直接产生，否则就实例化 Integer；
// 除了 Float 和 Double 类型，都是可以应用对象常量池概念的；
```

## String 字符串

### 字符串与 byte 数组间的相互转换

常用于网络通信。

```Java
package com.string;

import java.io.UnsupportedEncodingException;
import java.nio.charset.StandardCharsets;

public class main {
  public static void main(String[] args) throws UnsupportedEncodingException {
    String str = new String("JAVA 编程 基础");
    // 将字符串转换为 byte 数组，并打印输出
    byte[] arrs = str.getBytes(StandardCharsets.UTF_8);
    for (int i = 0; i < arrs.length; i++) {
      System.out.print(arrs[i] + " ");
    }
    System.out.println();

    // 通过 byte 数组，来实例化一个 String
    String str1 = new String(arrs, StandardCharsets.UTF_8); // UnsupportedEncodingException 为 Checked Exception，不通过编译，我们选择 throws 直接抛出；
    System.out.println(str1);
    // 也可以转换为 GBK，但此时为乱码
    String str2 = new String(arrs, "GBK");
    System.out.println(str2);
  }
}
// 74 65 86 65 32 -25 -68 -106 -25 -88 -117 32 -27 -97 -70 -25 -95 -128 
// JAVA 编程 基础
// JAVA 缂栫▼ 鍩虹
// 其中 32 为”空格“
```

### == 和 equals 方法的区别

```java
String str1 = "hello";
String str2 = "hello";
String str3 = new String("hello");

str1.equals(str2) // true，内容相同
str1.equals(str3) // true, 内容相同

str1 == str2      // true, 地址相同，如图
str1 == str3      // false, 地址不同，如图
```

![image.png](http://tva1.sinaimg.cn/large/006KtwDzgy1gyyyycfsbhj30wi0iadlq.jpg)

### String 的不可变性

String 对象一旦被创建，则不能被修改，是不可变的；所谓的修改其实是创建了新的对象，所指向的内存空间不变；

```java
String s1 = "imooc";
String s2 = "Hello," + s1;
// s1 一直没有变化
// 如果一直累加，这样子会生成很多中间变量；通过 StringBuilder 来解决；
```

### StringBuilder

- String 和 StringBuilder 的区别：String 具有不可变性，而 StringBuilder 不具备；
- 建议：当频繁操作字符串时，使用 StringBuilder；
- 又不得不提 StringBuffer，StringBuilder 和 StringBuffer 二者相似，区别：StringBuffer 是线程安全的，StringBuilder 不是，所以性能略高；

```java
StringBuilder str = new StringBuilder("你好");
str.append(",");
str.append("imooc");
System.out.println("str="+ str); //你好,imooc
```

## 集合

### 集合的应用场景（为什么要使用集合，而不用数组？）

- 无法预测存储数据的数量；
- ~~同时存储具有一对一关系的数据；~~
- 频繁进行数据的增删（效率高于数组）；
- 数据重复问题；

### *集合框架的体系结构

- Collection（接口）：
  - List（接口）：ArrayList（实现类）；
  - Queue（接口）：LinkedList（实现类）（同时也实现了 List 接口）；
  - Set（接口）：HashSet（实现类）；
- Map（接口）：HashMap（实现类）；

### List

- 是元素有序并且可以重复的集合，称为序列；
- 可以精确地控制每个元素的插入位置，或删除某个位置的元素；
- ArrayList：底层是由数组实现的，动态增长；**在列表尾部插入或者删除数据非常高效，更适合查找和更新元素**；ArrayList 中的元素可以为 null；
- 常用 API：add、remove、set、get；

### Set

- HashSet 中的元素无序并且不可以重复（只允许有一个 null 元素）；
  - 需要重写 Class 的`hashCode`和`equals`方法：如何做 hash、如何判断对象是否相等（重复）；
- 具有良好的存取和查找性能；

```java 
/**
* Set 的遍历方式（迭代器）
**/
Set set = new HashSet();
set.add("blue");
set.add("red");

Iterator it = set.iterator();
while(it.hasNext()) {
  System.out.print(it.next());
}

```

```java
/**
* 删除数据的操作
**/
Set targetSet = new Set<Cat>();
for(Cat cat : set) { // 增强 for 循环
  if (cat.getMonth() < 5) {
    targetSet.add(cat); // 不能一边遍历，一边删除 set.remove(cat);
  }
}
set.removeAll(targetSet);
```

### HashMap

- 基于哈希表的 Map 接口的实现；
- 允许使用 null 值和 null 键；（Key 值不允许重复）；

```java
public static void main(String[] args) {
  Map<String, String> map = new HashMap<>();
  map.put("cat", "猫");
  map.put("dog", "狗");
  map.put("bird", "鸟");
  // 遍历 map
  Set<Map.Entry<String, String>> mapEntrySet = map.entrySet();
  for(Map.Entry<String, String> entry : mapEntrySet) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
  }
}

map.containsKey("cat");
```

## 集合排序

### Collections

> 数组排序 inte[] arr = {1, 2, 3, 4, 5}; Arrays.sort(arr);

使用 Collections 类的 `sort(List<T> list)` 方法：根据元素的自然顺序对指定列表按升序进行排序。

注意：Collection 是接口，Collections 是类。

```java
Collections.sort(list);
```

### Comparator 接口

强行对某个对象进行整体排序的比较函数

`int compare(T o1, T o2)` 比较用来排序的两个参数：

- 如果 o1 < o2, 返回负数；
- 如果 o1 == o2，返回 0；
- 如果 o1 > o2, 返回正整数；

```java
// 按照名字升序排列
public class NameComparator implements Comparator<Cat> {
  public int compare(Cat o1, Cat o2) {
    String name1 = o1.getName();
    String name2 = o2.getName();
    return name1.compareTo(name2);
  }
}

Collections.sort(catList, new NameComparator());
```

### Comparable 接口

实现 `int compareTo(T o)` 方法：返回值规则同 Comparator 接口中的 compare 方法；

和 Comparator 不同的是：为了比较两个对象，新声明了一个 Comparator Class，而 Comparable 是直接由比较对象的类来实现，不需要额外引入一个对象；

## 泛型

### 为什么需要泛型

在 Java 中增加泛型之前，是使用继承来实现的，存在以下坏处：

- 可向集合中添加任意类型的对象；
- 需要强制转换，存在风险；

```java
abstract class Goods {}
class Cloth extends Goods {}
class Book extends Goods {}

// ❌ ⭐️变量声明的类型 必须匹配 传递给实际对象的类型（不能声明时候使用父类，实例化的时候使用子类）
List<Goods> list = new ArrayList<Book>();

// 稍微看下就行
ArrayList<Goods> goodsList = new ArrayList<>();
// 可向集合中添加任意类型的对象
goodsList.add(new Cloth());
goodsList.add(new Book());
// 需要强制类型转换，存在风险
Book book = (Book)goodsList.get(0);
```

```java
public void sellGoods(List<? extends Goods> goods)// ? 代表 Goods 的子类（是 Goods 的儿子）
public void sellGoods(List<? super Goods> goods) //  ? 代表 Goods 的超类（是 Goods 的爸爸）
```

## 线程

### 线程的创建

- 方式一：创建一个 Thread 类，或者一个 Thread 子类的对象；
  - `new MyThread().start();`
- 方式二：创建一个实现 Runnable 接口的类的对象（更常用）；
  - `new Thread(new MyRunable()).start();`
  - 为什么还需要方式二？
    - Java 不支持多继承（继承了 Thread 之后，就不能继承其他）；
    - 不打算重写 Thread 类的其他方法；

```java
/**
* 方式一：继承 Thread 来创建线程
**/
package com.mythread;

class MyThread extends Thread {
  @Override
  public void run() {
    System.out.println(this.getName() + "线程运行");
  }
}

public class ThreadTest {
  public static void main(String[] args) {
    Thread mt = new MyThread();
    mt.start();
    // mt.run();   // ❌ 直接执行 run，则相当于一个普通对象执行其中的方法一样，并没有开启线程；
    // mt.start(); // ❌ IllegalThreadStateException，一个线程不能启动两次；
  }
}

// 结果：Thread-0线程运行
```

```java
/**
* 方式二：实现 runnable 来创建线程
**/
package com.mythread;

class PrintRuner implements Runnable {
  int count;
  @Override
  public void run() {
    while(count < 10) {
      System.out.println(Thread.currentThread().getName() +  "线程运行" + "--" + count);
      count ++;
      try {
        Thread.sleep(1000);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
  }
}

public class ThreadTest {
  public static void main(String[] args) {
    Runnable pr = new PrintRuner();
    Thread t1 = new Thread(pr);
    t1.start();
    Thread t2 = new Thread(pr); // 使用同一个 runnable 对象进行实例化 Thread；共用了一个资源 count；
    t2.start();
  }
}

// 结果：总共只执行十次，因为 count 被共用；因为 Thread.sleep，所以一条一条地打印。
//  Thread-0线程运行--0
//  Thread-1线程运行--0
//  Thread-0线程运行--2
//  Thread-1线程运行--2
//  Thread-0线程运行--3
//  Thread-1线程运行--4
//  Thread-0线程运行--6
//  Thread-1线程运行--6
//  Thread-0线程运行--7
//  Thread-1线程运行--8
//  Thread-0线程运行--9

```

### 线程的状态和生命周期

创建线程后，通过 start 开启，此时状态变为”可运行“（而不是”正在运行“），等待时间片轮转执行。

![image.png](http://tva1.sinaimg.cn/large/006KtwDzgy1gyyyt7pznoj30tu0fjq7w.jpg)

### 线程的优先级

- Java 为线程类提供了 10 个优先级；
- 优先级可以用整数 1-10 表示，超过范围抛出异常；
- 主线程默认优先级为 5；
- getPriority() 获取线程优先级方法；setPriority() 设置线程优先级方法；
- `new Thread().join()`的作用：等待这个线程结束后才执行其他线程（抢占执行权）；

### 同步

Synchronized 关键字用在：成员**方法**、静态**方法**、语句块：

- `public synchronized void xx() {}`
- `public static synchronized void xx() {}`
- `Synchronized(obj) {}`

为了保证在存款或者取款的时候，**不允许其他线程对账户余额进行操作**，需要使用 `synchronized` 将 bank 对象里 saveAccount 和 drawAccount 方法进行锁定（保证方法不被打断）。

```java
Bank bank = new Bank("yqb", 1000); // 创建账户
// 创建存款 & 取款 runnable 的对象（内部调用 bank.xxAcount 的方法）
SaveAccount sa = new SaveAccount(bank);
DrawAccount da = new DrawAccount(bank);
// 创建线程
Thread save = new Thread(sa);
Thread draw = new Thread(da);
save.start();
draw.start();

try {
  draw.join();
  save.join();
} catch() {
  
}
```

### 死锁

- wait() 方法：中断方法的执行，使线程等待；
- notify() 方法：唤醒处于等待的某一个线程，使其结束等待；
- notifyAll() 方法：唤醒所有处于等待的线程，使它们结束等待；
- 死锁：两个线程都处在 wait 状态；

#### Test.java

```java
/**
* Test
**/
package com.producerconsumer;

public class Test {
  public static void main(String[] args) {
    Queue queue = new Queue();
    new Thread(new Producer(queue)).start();
    new Thread(new Consumer(queue)).start();
  }
}

```

#### Queue.java

```java
/**
* Queue
**/
package com.producerconsumer;

// 本队列只支持一生产一消费
public class Queue {
  private int num;
  private boolean hasAvailable = false; // ⭐️如果不加 wait 关键字，会出现"生产1 消费1 消费1"的情况，一次生产多次消费

  public synchronized void setNum(int num) { // ⭐️如果没有 synchronized 关键字，可能会出现"生产10 消费9"（用了上一次数据）的情况；
    if (this.hasAvailable) {
      try {
        wait();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    this.num = num;
    System.out.println("生产：" + this.num);
    this.hasAvailable = true;
    notifyAll();  // ⭐️如果没有 notifyAll 关键字，会出现"死锁"情况（程序没有终止，但是打印都停止了）（生产者、消费者都处于 wait）（什么时候会处于这种情况？？？）
  }
  public synchronized int getNum() {
    if (!this.hasAvailable) {
      try {
        wait();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    System.out.println("消费：" + this.num);
    this.hasAvailable = false;
    notifyAll();
    return this.num;
  }
}

```

#### Producer.java

```java
/**
* Producer
**/
package com.producerconsumer;

public class Producer implements Runnable {
  private Queue queue;
  Producer(Queue queue) {
    this.queue = queue;
  }

  @Override
  public void run() {
    int i = 0;
    while(true) {
      this.queue.setNum(i++);
      try {
        Thread.sleep(1000);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
  }
}

```

#### Consumer.java

```java
/**
* Consumer
**/
package com.producerconsumer;

public class Consumer implements Runnable {
  private Queue queue;
  Consumer(Queue queue) {
    this.queue = queue;
  }
  @Override
  public void run() {
    while(true) {
      this.queue.getNum();
      try {
        Thread.sleep(1000);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
  }
}

```

## 输入输出流

### File 类的使用

介绍了如何创建文件 createNewFile、创建文件夹 mkDir mkDirs、绝对路径相对路径编码。

### 字节流

#### 输入流 类的结构

InputStream（字节输入流，顶级父类）

- ⭐️FileInputStream（文件输入流）
- PipedInputStream（管道输入流）
- FilterInputStream（过滤器输入流）
  - PushbackInputStream（回压输入流）（无对应输出流）
  - ⭐️BufferedInputStream（缓冲输入流）
  - DataInputStream（数据输入流）
- ObjectInputStream（对象输入流）
- SequenceInputStream（顺序输入流）（无对应输出流）
- ByteArrayInputStream（字节数组输入流）
- StringBufferInputStream（缓冲字符输入流）（无对应输出流）

不适合字符操作（因为编码原因），更时候视频、图像之类的。

#### 输出流 类的结构

OutputStream(字节输出流，顶级父类)

- FileOutputStream
- PipedOutputStream
- FilterOutputStream
  - PrintStream（格式化输出流）
  - BufferedOutputStream
  - DataOutputStream
- ObjectOutputStream
- ByteArrayOutputStream

#### FileInputStream & FileOutputStream

用于从文件系统的某个文件获得输入字节流，用于读取诸如图片数据之类的原始字节流。

```java
/**
* 输入流
**/
// fileInputStream.read()
// fileInputStream.read(byte[] b)
// fileInputStream.read(byte[] b, int off, int len)
// fileInputStream.close()
public class FileInputStreamTest {
  public static void main(String[] args) throws IOException {
    FileInputStream fis = new FileInputStream("mooc.txt");
    int readRes;
    while((readRes = fis.read()) != -1) { // 读到 -1 即到文件底（其他时候，返回读到的一个字节）
      System.out.print((char)readRes + " ");
    }
    System.out.println();
//    System.out.println("-------------===============-------------");
//    byte[] bytes = new byte[100];
//    readRes = fis.read(bytes, 1, 3); // readRes = -1, 因为结束了；
//    System.out.println(readRes);
//    System.out.println(new String(bytes));
    fis.close();
//    char c = 65535; // char 的大小（两个字节）：0 ~ 65535
//    byte x = 127; // byte 的大小：-128~127
  }
}
```

```java
/**
* 输出流
**/
// fileOutputStream.write(int b) 将指定字节写入此文件输出流
// fileOutputStream.write(byte[] b)
// fileOutputStream.write(byte[] b, int off, len)
// fileOutputStream.close()
public class FileInputStreamTest {
  public static void main(String[] args) {
    try {
      FileInputStream fis = new FileInputStream("./pass.png"); // 读取原始图片
      FileOutputStream fos = new FileOutputStream("./pass-copy.png"); // append: true 向末尾添加数据（而不是覆盖
      byte[] bytes = new byte[1024];
      int readRes;
      while((readRes = fis.read(bytes)) != -1) {
//        fos.write(bytes); // 原图为 10kb，这样复制之后的图片为 11kb，因为每次都直接写入 1kb，最后一次多写了。
        fos.write(bytes, 0, readRes); // readRes 不等于 -1 时，即为读到的字节长度，这样就不会多写入数据
      }
    } catch (IOException e) {
      e.printStackTrace();
    }
  }
}
// 1 个字节可以用两位 十六进制表示，例如 2f = 2 * 16 + 15 = 47
// 1 个字节最大 255, 即 ff = 15 * 16 + 15 = 255
```

#### BufferedInputStream & BufferedOutputStream

从硬盘读取数据慢，从缓存读取快；**缓冲区减少了”程序“与”硬盘“直接读写的次数**（积累到一定大小，整体再读写一次），提高读取速度（需要结合其他 InputStream）。

⭐️ “缓冲区满的时候”**自动**执行写操作；“缓冲区不满的时候”需要执行清空 flush 写入。

```java
/**
* 图片拷贝示例（整体逻辑同上例的 FileInputStreamTest）
* 一张 17.5 MB 的照片，
*     在 FileInputStreamTest 中，要耗费 150~200 毫秒（不使用缓冲区）；
*     在 BufferedStreamTest  中，只要耗费 50-100 毫秒（快 1-2 倍）；
**/ 
package com.stream;

import java.io.*;

public class BufferedStreamTest {
  public static void main(String[] args) throws IOException {
    // 文件输入流 -> 缓冲输入流
    FileInputStream fileInputStream = new FileInputStream("123.jpg");
    BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
		// 文件输出流 -> 缓冲输出流
    FileOutputStream fileOutputStream = new FileOutputStream("123-copy.jpg");
    BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
		
    long startTime = System.currentTimeMillis(); // 记录起始时间

    // 缓冲输入流（感觉缓冲输入流没有减少读取次数，每次都按着 1024 去取？）
    byte[] bytes = new byte[1024];
    int readRes;
    while((readRes = bufferedInputStream.read(bytes)) != -1) {
      bufferedOutputStream.write(bytes, 0, readRes); // 输出流积攒，等会儿一次性写入文件（而不是每读一次就写一次）
    }
    bufferedOutputStream.flush(); // 将缓冲区剩下的字节全部写到文件

    long endTime = System.currentTimeMillis();  // 记录终止时间
    System.out.println("花费的时间是：" + (endTime - startTime)); // 计算花费时间

    bufferedOutputStream.close();
    fileOutputStream.close();
    bufferedInputStream.close();
    fileInputStream.close();
  }
}
```

### 字符流

感觉和字节流差不多，为什么需要字符流？答：二进制使用字节流、字符文字使用字符流（感觉是为了更加方便操作字符）。

#### 输入流 类的结构

Reader

- BufferedReader
  - LineNumberReader
- CharArrayReader
- StringReader
- InputStreamReader
  - FileReader
- PipedReader
- FilterReader
  - PushbackReader

#### 输出流 类的结构

Writer

- BufferedWriter
- CharArrayWriter
- StringWriter
- OutputStreamWriter
  - FileWriter
- PipedWriter
- FilterWriter

#### InputStreamReader &OutputStreamWriter

字节字符转换流，用于 stream 和 readerwriter 的转换。

```java
package com.stream;

import java.io.*;
import java.nio.charset.StandardCharsets;

public class StreamWriterReaderTest {
  public static void main(String[] args) throws IOException {
    // 创建 reader
    FileInputStream fileInputStream = new FileInputStream("mooc.txt");
    InputStreamReader inputStreamReader = new InputStreamReader(fileInputStream, "UTF-8"); // 将 stream 作为参数，生成 reader // 还可以传入编码（因为是"字符"流）
 // 上面两行创建 reader 的过程，等价于这一行代码:
 // FileReader fileReader = new FileReader("mooc.txt", StandardCharsets.UTF_8);
    BufferedReader bufferedReader = new BufferedReader(inputStreamReader); // 还可以再包一层 buffed

    // 创建 writer
    FileOutputStream fileOutputStream = new FileOutputStream("mooc-copy.txt");
    OutputStreamWriter outputStreamWriter = new OutputStreamWriter(fileOutputStream, "UTF-8"); // 编码要和 InputStreamReader 对应
    BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);

    char[] chars = new char[500];
    int readRes;
//    System.out.println((char)inputStreamReader.read()); // 返回文件的第一个”字“
    while((readRes = bufferedReader.read(chars)) != -1) {
      bufferedWriter.write(chars, 0, readRes);
    }
    bufferedWriter.flush();

    // 与下面等价，可以直接用 OutputStreamWriter 来输出
//    while((readRes = inputStreamReader.read(chars)) != -1) {
//      outputStreamWriter.write(chars, 0, readRes);
//    }
//    outputStreamWriter.flush();
  }
}

```

### 对象的序列化与反序列化

步骤：

1. 创建一个类，实现 Serializable 接口（并没有重写任何方法）；
2. 创建对象，将对象写入文件（涉及到 ObjectInputStream、ObjectOutputStream）；
3. 从文件读取对象信息；

```java
class Cat implements Serializable {// 不需要做其他实现方法 // 不写会抛错 NotSerializableException
  private String name;
  private int age;
  Cat(String name, int age) {
    this.name = name;
    this.age = age;
  }
  public String toString() {
    return this.name + "-" + this.age;
  }
}

public class ObjectStreamTest {
  public static void main(String[]args) throws IOException, ClassNotFoundException {
    // 写入对象
    FileOutputStream fileOutputStream = new FileOutputStream("obj.txt");
    ObjectOutputStream objectOutputStream = new ObjectOutputStream(fileOutputStream);

    Cat cat = new Cat("yqb", 12);
    objectOutputStream.writeObject(cat); // 注意是 writeObject
    objectOutputStream.writeBoolean(true);
    objectOutputStream.flush();

    // 读取对象
    FileInputStream fileInputStream = new FileInputStream("obj.txt");
    ObjectInputStream objectInputStream = new ObjectInputStream(fileInputStream);

    Cat cat1 = (Cat)objectInputStream.readObject();
    boolean bool = objectInputStream.readBoolean(); // 要按着写入的顺序
    System.out.println(cat1);
    System.out.println(bool);
  }
}

// 打开文件是乱码
// yqb-12
// true
```

## Java 反射

反射，是多个框架使用到的语言特性；窥探对象内部结构的方法就叫做反射；[JavaScript 到处都是反射](https://segmentfault.com/q/1010000002761696)，所以这个概念也就淡化了，例如 `window['resizeTo'](100, 200)` 就是反射，Java 得通过一堆 API 才能拿到 resizeTo 这个函数并调用。

记一些 API：

- `Class.forName("com.entity.Employee")`：静态方法，用于获取指定 Class 对象 classObj；
- `classObj.neweInstance()`：通过默认构造函数创建新的对象；
- `classObj.getConstructor(new Class[] { Integer.class, String.class, Float.class, String.class })`：获得指定 public 修饰构造方法 Constructor 对象，这个函数的参数是“需要的构造函数的参数”列表；
  - `constructorObj.newInstance(new Object[] {100, "韩梅梅", 3000f, "研发部"})`：通过对应的构造方法创建对象；
- `classObj.getMethod()`：获得指定的 public 修饰方法 Method 对象；
- `classObj.getField()`；

## Lambda 表达式

（JDK > 1.8）就是 JavaScript 的箭头函数的语法，主要是为了简化编码。来一个对比：

```java
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

// 传统代码
Collections.sort(names, new Comparator<string>() {
  @Override
  public int compare(String a, String b) {
    return b.compareTo(a);
  }
});

// Lambda 表达式：简化匿名类的编写
Collections.sort(names, (a, b) -> b.compareTo(a););

// ⭐️约束条件：Lambda 表达式只能实现有且只有一个抽象方法的接口（称为“函数式接口”）
```



