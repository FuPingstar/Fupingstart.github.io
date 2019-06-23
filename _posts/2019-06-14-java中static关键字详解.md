---
layout: post
title: java中static关键字详解
categories: [general, java]
tags: [java, java关键字, static]
comments: true
shortinfo: 全面分析static关键字
---

**编程的过程中，想必我们碰到static关键字的地方很多，相信很多人跟我一样，对static这个关键字只是初步了解，会用，没有做过全面透彻的分析，写这篇文章就是全面的分析一下这个关键字，以及在java中常见用法。如有不对之处还需指正，评论功能需挂vpn才可见，没办法，Disqus国内无法访问，国家网络的闭关锁国很是苦恼啊...不吐槽了，下面开始**

**首先，我们先来分析static关键字要解决的问题**

正如我们所了解的，只有用new语句来创建对象的时候，数据的存储空间才被分配，其方法才供外界调用。下面提出两种创建对象无法解决的问题：
  1. 只想为特定域分配单一存储空间，而不去考虑究竟要创建多少对象，甚至根本就不创建任何对象
  2. 希望某个方法不与包含它的任何类的任何对象关联在一起，也就是说，即使没有创建任何对象，也能够调用这个方法。

static关键字可以解决这两个问题，当声明一个事物是static时，意味着这个域或者方法不会与包含它的那个类的任何实例对象关联在一起，因此，即使从未创建某个类的任何对象，也可以调用static方法或者访问其static域。

static可以用来修饰类的成员方法、类的成员变量，另外可以编写static代码块来优化程序性能。

+ static方法

  &emsp;&emsp;static方法一般称作静态方法，由于静态方法不依赖于任何对象就可以进行访问，因此对于静态方法来说，是没有this的，。并且由于这个特性，在静态方法中不能访问类的非静态成员变量和非静态成员方法，因为非静态成员方法/变量都是必须依赖具体的对象才能够被调用，反过来，非静态成员方法是可以访问静态成员的变量和方法的。

  <font color="red">&emsp;&emsp;这里我们就清楚main方法为什么要是静态方法了，作为一个应用的入口方法，在执行的时候没有创建任何对象，只能通过类名去调用。</font>
+ static变量

  &emsp;&emsp;static变量也称作静态变量，静态变量和非静态变量的区别是：静态变量被所有的对象所共享，在内存中只有一个副本，它当且仅当在类初次加载时会被初始化。而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响。
+ static代码块

  &emsp;&emsp;static关键字还有一个比较关键的作用就是 用来形成静态代码块以优化程序性能。static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次，这也是static静态块可以优化代码的原因所在。

**这里列出几个java中static应该注意的地方**

1. java中的static关键字不会改变类中成员的访问权限
2. 可以通过this访问静态成员变量<static变量是被对象共享的>
3. java中static关键字不能作用于局部变量  

**接下来我们通过JVM类加载的过程来分析一下static静态变量初始化过程：**

&emsp;&emsp;类加载的过程一共分为5个阶段：加载，验证，准备，解析和初始化，而对类变量分配内存恐和初始化主要在准备阶段和初始化阶段。

&emsp;&emsp;准备阶段是正式为类变量（被static修饰的变量，不包括实例变量）设置初始值的阶段，这些变量使用的内存都将在方法区中进行分配。由于准备阶段尚未开始执行任何java代码，这里所说的初始值是数据类型的0值。

&emsp;&emsp;初始化阶段，才真正执行类中定义的java程序代码（或者说是字节码），在准备阶段，类变量已经赋过一次系统要求的初始值，而在初始化阶段，则根据程序员通过程序制定的主观计划去初始化变量和其他资源。

**接着我们补充一下在继承场景中，JVM类加载器对类变量（static修饰的变量，也可以说是静态变量）的优化**

&emsp;&emsp;首先我们先来看一下，虚拟机规范严格规定的对类立即进行初始化的5中情况：
1. 遇到new，getstatic，putstatic或者invokestatic这4条字节码指令的时，如果类没有进行初始化，则需先触发其初始化。生成这4条字节码指令最常见的场景：使用new关键字实例化对象的时候，<font color="red">读取或者设置一个类的静态字段（被final修饰，已经在编译器把结果放入常量池的静态字段除外），调用一个类的静态方法的时候。</font>

2. 使用java.lang.reflect包的方法对类进行反射调用的时候，如果类没有进行过初始化，则需要先触发其初始化。

3. <font color="red">当初始化一个类的时候，如果发现其父类还没有进行初始化，则需要先触发其父类的初始化。</font>

4. 当虚拟机启动的时候，用户需要指定一个要执行的主类（包含main方法的那个类），虚拟机会先初始化这个类。

5. 当时用动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果为REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，并且这个方法句柄所对应的类没有进行初始化，则需要先触发其初始化。

上面的5种情况，称为对一个类的主动引用，有且只有这5种场景的任何一种发生都会触发对类的初始化。<font color="red">作为一个补充知识点。</font>

**了解了类加载阶段初始化的发生场景，接下来，我们看几个继承场景中，使用静态字段的情况：**


```java
public class SuperClass {

    static {
        System.out.println("SuperClass init");
    }

    public static int value = 123;
}
public class SubClass extends SuperClass{

    static {
        System.out.println("SubClass init");
    }

}
public class NotInitiallization {

    public static void main(String[] args) {
        System.out.println(SubClass.value);
    }
}

运行结果：
SuperClass init
123

上面这种情况是子类引用父类的静态字段，不在导致类初始化的那五种情况中，属于被动引用，不会导致子类初始化。所以没有出现“SubClass init”
```

**改进一下上面的代码，就能很清楚的看到jvm类加载器对类变量的优化：**

```java
public class SubClass extends SuperClass{

    public static int high;
    static {
        System.out.println("SubClass init");
        high = 100;
        value = 456;
    }
}
public class NotInitiallization {

    public static void main(String[] args) {
        System.out.println(SubClass.value);
        System.out.println(SubClass.high);
        System.out.println(SuperClass.value);
    }
}

运行结果：
SuperClass init
123
SubClass init
100
456

调用SubClass.value，跟上面一样，是子类引用父类的静态变量，不会进行初始化子类，调用SubClass.high的时候，初始化子类，执行静态代码块，因为父类的value和子类的value在内存中只有一份，当输出SuperClass.value，一样是456.这就是jvm对静态变量的优化。
```

**我们再来看一种情况，用final修饰静态变量。**

```java
public class ContClass {

    static {
        System.out.println("ContClass init");
    }

    public static final String HELLOWORLD = "hello world";
}

class NotInitialization{
    public static void main(String[] args) {
        System.out.println(ContClass.HELLOWORLD);
    }
}

在编译阶段，已经通过常量传播优化，将常量值存储到类的常量池中，以后对常量HELLOWORLD的引用实际上被转化为ContClass类对自身常量池的引用。也就是说，NotInitialization的Class文件之中并没有ContClass类的符号引用入口。所以，被final的static变量也不会触发类的初始化。
```



**static关键字就介绍到这里了，如果有什么不对之处求留言指正，评论功能需要挂vpn哦，🤨有什么没有提及的地方也请致电**
<a href="mailto:{{ site.author.email }}">email</a>
