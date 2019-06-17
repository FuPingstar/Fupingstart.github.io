---
layout: post
title: java中final关键字详解
categories: [general, java]
tags: [java, java关键字, final]
comments: true
shortinfo: 全面分析final关键字
---


**编程的过程中，想必我们碰到final关键字的地方很多，相信很多人跟我一样，对final这个关键字只是初步了解，会用，没有做过全面透彻的分析，写这篇文章就是全面的分析一下这个关键字，以及在java中常见用法。如有不对之处还需指正，评论功能需挂vpn才可见，没办法，Disqus国内无法访问，国家网络的闭关锁国很是苦恼啊...不吐槽了，下面开始**

**首先，我们先来看java中使用到final的三种情况：数据，方法和类**

#### final数据

&emsp;&emsp;任何一种编程语言都使用某种方法来使得一块数据是恒定不变的，最典型的有两种情况，一个就是编译时常量，一个就是运行时被初始化的值，但是不能改变。java中使用final关键字来实现不变常量。下面我们从这两种情况分析final数据。

+ 对于编译期常量，编译期可以将该常量的值代入任何可能用到它的计算式中，可以在编译时执行计算式，减轻了运行时负担。<font color = "red">在java中，编译期常量必须是基本数据类型，并且被final修饰，在对这个常量进行定义的时候，必须对其赋值。</font>

+ 对于运行时初始化的常量，其类型既可以是基本类型，也可以是引用类型，当final修饰的是引用类型的时候，使得应用恒定不变。注意这里不变的含义：**一旦引用被初始化指向一个对象，就无法再把它改为指向另外一个对象，然而，对象其自身确实可以被修改的，java没有提供使得任何对象恒定不变的途径。**


**接下来我们用java编程思想中的一段代码具体分析final数据**

```java
public class FinalData {

    private static Random rand = new  Random(47);
    private String id;

    public FinalData(String id) {
        this.id = id;
    }

    private final int valueOne = 9;
    private static final int VALUE_TWO = 99;

    //valueOne 和 VALUE_TWO都是带有编译时数值的final基本类型，所以二者均可以作为编译期常量，没有太大区别，
    // 既是static又是final的域只占据一段不能改变的存储空间


//    定义常量的典型方式，public：可以被用于包之外，static：强调只有一份，final：则说明是一个常量
    public static final int VALUE_THREE = 39;

    private final int i4 = rand.nextInt(20);
    static final int INT_5 = rand.nextInt(20);
    private Value v1 = new Value(11);
    private final Value v2 = new Value(22);
    private static final Value V3 = new Value(33);
    private final int[] a = {1,2,3,4,5,6};


    public static void main(String[] args) {
        FinalData fd1 = new FinalData("fd1");
//        fd1.valueOne++;    不能改变编译器常量的值
        fd1.v2.i++;        //final修饰对象，对象自身可以改变，只是引用不能指向其他对象
        fd1.v1 = new Value(9);  // ok ,v1不是一个常量
        for (int i = 0; i < fd1.a.length; i++) {
            fd1.a[i]++;
        }
//        fd1.v2 = new Value(0);   //引用被final修饰，不能指向其他引用
//        fd1.a = new int[3];        数组也是一种引用，跟上面情况一样

    }
}

class Value{
    int i; //包访问权限

    public Value(int i) {
        this.i = i;
    }
}
```

**把final数值定义为静态和非静态的区别只有在运行时被初始化才会显现。既是static又是final的域只占据一段不能改变的存储空间**

##### 空白final

&emsp;&emsp;空白final指的是被声明为final但是又为给定初值的域。<font color = "red">无论什么情况，编译器都要确保空白final在使用前必须初始化，所以在域的定义处或者每个构造器用表达式对final进行赋值，这样做的话，一个类的final域可以做到根据对象而有所不同，却又保证了其恒定不变的特性，在final关键字的使用上提供了更大的灵活性。</font>

下面引用java编程思想的一段代码：
```java
class Poppet{
    private int i;

    public Poppet(int i) {
        this.i = i;
    }
}
/**
 * @author FuPingstar
 * @date 2019/6/17 13:54
 */
public class BlankFinalTest {

    private final int i = 9; //Initialized final

    private int j;    //Blank Final

    private final Poppet poppet;  //Blank final Reference


    //blank final must initialize in constructor

    public BlankFinalTest(){
        j = 1;                     // initialize blank final
        poppet = new Poppet(1); // initialzie blank final reference
    };

    public BlankFinalTest(int x) {
        j = x;                   // initialize blank final
        poppet = new Poppet(x);  // initialize blank final reference
    }
}

```


##### final参数

&emsp;&emsp;java允许在参数列表中以声明的方式将参数指明为final，这样你无法在方法中更改参数引用所指向的对象，当传递基本类型参数的时候，无法改变参数，只可以读参数。

```java
class Gizmo{
    public void spin(){

    }
}

/**
 * @author FuPingstar
 * @date 2019/6/17 14:19
 */
public class FianlArguments {

    void with(final Gizmo gizmo){
//        gizmo = new Gizmo();           //Illegal  -- g is final
    }

    void without(Gizmo gizmo){
        gizmo = new Gizmo();
        gizmo.spin();
    }

//    void f(final int i){i++}    Cant'change  i is final
    int f(final int i){
        return i+1;
    }

    public static void main(String[] args) {
        FianlArguments fianlArguments = new FianlArguments();
        fianlArguments.with(null);
        fianlArguments.without(null);
    }
}

```
#### final方法

&emsp;&emsp;使用final方法的原因由两个，一个是出于设计，一个是处于效率，现在效率问题已由jvm去解决，设计问题指的<font color = "red">是将该方法锁定，防止任何继承类修改他的含义.</font>

**下面，我们讲解一下private和final如何锁定方法，防止任何继承类修改它的含义**

&emsp;&emsp;类中的所有private方法都隐式的指定为final的，因为继承子类无法取用私有方法，所以也就无法覆盖了，可以给private方法添加final修饰词，但是并没有什么卵用，

```java
class WithFinals{

//    identical to private alone
    private final void f(){
        System.out.println("WithFinals.f()");
    }

//    alsl automatically "final"
    private void g(){
        System.out.println("WithFinals.g()");
    }
}

class OverdingPrivate extends WithFinals{
    private final void f(){
        System.out.println("OverdingPrivate.f()");
    }
    private void g(){
        System.out.println("OverdingPrivate.g()");
    }
}

class OverdingPrivate2 extends OverdingPrivate{
    public final void f(){
        System.out.println("OverdingPrivate2.f()");
    }
    public void g(){
        System.out.println("OverdingPrivate2.g()");
    }
}

/**
 * @author FuPingstar
 * @date 2019/6/17 14:36
 */
public class FianlMethodTest {

    public static void main(String[] args) {
        OverdingPrivate2 op2 = new OverdingPrivate2();

        op2.f();
        op2.g();
//   you can upcast
        OverdingPrivate op1 = op2;
//   you can't call methods.
//        op1.f();
//        op1.g();
        WithFinals wt = op2;
//        wt.f();
//        wt.g();
    }
}
```
#### final类

&emsp;&emsp;将某个类整体定义为final，该类将不能被继承，可以说是对该类的设计永不需要做任何改动，或者处于安全考虑，不希望它有子类。

&emsp;&emsp;final类禁止继承，所以final类中的所有方法都隐式的指定为final。


**最后，通过作者在学习过程中遇到的使用final关键字的问题来深刻认识final**

1. hibernate的懒加载的原理是使用cglib实现动态代理，cglib是针对类来实现代理的，原理是对目标类生成一个子类，并覆盖其中的方法实现增强，因为采用的是继承，所以不能对final实现的类进行代理。




**static关键字就介绍到这里了，如果有什么不对之处求留言指正，评论功能需要挂vpn哦，🤨有什么没有提及的地方也请致电**
<a href="mailto:{{ site.author.email }}">email</a>
