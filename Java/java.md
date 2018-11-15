Java语言是面向对象的：
Java语言提供类、接口和继承等原语，为了简单起见，只支持类之间的单继承，但支持接口之间的多继承，并支持类与接口之间的实现机制（关键字为implements）。Java语言全面支持动态绑定，而C++语言只对虚函数使用动态绑定。总之，Java语言是一个纯的面向对象程序设计语言。

Java作为一种面向对象语言。支持以下基本概念：
多态
继承
封装
抽象
类
对象
实例
方法
重载

对象：对象是类的一个实例（对象不是找个女朋友），有状态和行为。例如，一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等。
类：类是一个模板，它描述一类对象的行为和状态。

软件对象也有状态和行为。软件对象的状态就是属性，行为通过方法体现。
在软件开发中，方法操作对象内部状态的改变，对象的相互调用也是通过方法来完成。

局部变量：在方法、构造方法或者语句块中定义的变量被称为局部变量。变量声明和初始化都是在方法中，方法结束后，变量就会自动销毁。
成员变量：成员变量是定义在类中，方法体之外的变量。这种变量在创建对象的时候实例化。成员变量可以被类中方法、构造方法和特定类的语句块访问。
类变量：类变量也声明在类中，方法体之外，但必须声明为static类型。

构造方法
每个类都有构造方法。如果没有显式地为类定义构造方法，Java编译器将会为该类提供一个默认构造方法。
在创建一个对象的时候，至少要调用一个构造方法。构造方法的名称必须与类同名，一个类可以有多个构造方法。
下面是一个构造方法示例：
public class Puppy{
    public Puppy(){
    }
 
    public Puppy(String name){
        // 这个构造器仅有一个参数：name
    }
}

创建对象
对象是根据类创建的。在Java中，使用关键字new来创建一个新的对象。创建对象需要以下三步：
声明：声明一个对象，包括对象名称和对象类型。
实例化：使用关键字new来创建一个对象。
初始化：使用new创建对象时，会调用构造方法初始化对象。


下面是一个创建对象的例子：
public class Puppy{
   public Puppy(String name){
      //这个构造器仅有一个参数：name
      System.out.println("小狗的名字是 : " + name ); 
   }
   public static void main(String []args){
      // 下面的语句将创建一个Puppy对象
      Puppy myPuppy = new Puppy( "tommy" );
   }
}
编译并运行上面的程序，会打印出下面的结果：
小狗的名字是 : tommy


当在一个源文件中定义多个类，并且还有import语句和package语句时，要特别注意这些规则。
一个源文件中只能有一个public类
一个源文件可以有多个非public类
源文件的名称应该和public类的类名保持一致。例如：源文件中public类的类名是Employee，那么源文件应该命名为Employee.java。
如果一个类定义在某个包中，那么package语句应该在源文件的首行。
如果源文件包含import语句，那么应该放在package语句和类定义之间。如果没有package语句，那么import语句应该在源文件中最前面。
import语句和package语句对源文件中定义的所有类都有效。在同一源文件中，不能给不同的类不同的包声明。

Java包
包主要用来对类和接口进行分类。当开发Java程序时，可能编写成百上千的类，因此很有必要对类和接口进行分类。
Import语句
在Java中，如果给出一个完整的限定名，包括包名、类名，那么Java编译器就可以很容易地定位到源代码或者类。Import语句就是用来提供一个合理的路径，使得编译器可以找到某个类。
例如，下面的命令行将会命令编译器载入java_installation/java/io路径下的所有类
import java.io.*;
一个简单的例子
在该例子中，我们创建两个类：Employee 和 EmployeeTest。
首先打开文本编辑器，把下面的代码粘贴进去。注意将文件保存为 Employee.java。
Employee类有四个成员变量：name、age、designation和salary。该类显式声明了一个构造方法，该方法只有一个参数。
Employee.java 文件代码：
import java.io.*;
 
public class Employee{
   String name;
   int age;
   String designation;
   double salary;
   // Employee 类的构造器
   public Employee(String name){
      this.name = name;
   }
   // 设置age的值
   public void empAge(int empAge){
      age =  empAge;
   }
   /* 设置designation的值*/
   public void empDesignation(String empDesig){
      designation = empDesig;
   }
   /* 设置salary的值*/
   public void empSalary(double empSalary){
      salary = empSalary;
   }
   /* 打印信息 */
   public void printEmployee(){
      System.out.println("名字:"+ name );
      System.out.println("年龄:" + age );
      System.out.println("职位:" + designation );
      System.out.println("薪水:" + salary);
   }
}
程序都是从main方法开始执行。为了能运行这个程序，必须包含main方法并且创建一个实例对象。
下面给出EmployeeTest类，该类实例化2个 Employee 类的实例，并调用方法设置变量的值。
将下面的代码保存在 EmployeeTest.java文件中。
EmployeeTest.java 文件代码：
import java.io.*;
public class EmployeeTest{
 
   public static void main(String args[]){
      /* 使用构造器创建两个对象 */
      Employee empOne = new Employee("RUNOOB1");
      Employee empTwo = new Employee("RUNOOB2");
 
      // 调用这两个对象的成员方法
      empOne.empAge(26);
      empOne.empDesignation("高级程序员");
      empOne.empSalary(1000);
      empOne.printEmployee();
 
      empTwo.empAge(21);
      empTwo.empDesignation("菜鸟程序员");
      empTwo.empSalary(500);
      empTwo.printEmployee();
   }
}
编译这两个文件并且运行 EmployeeTest 类，可以看到如下结果：
$ javac EmployeeTest.java
$ java EmployeeTest 
名字:RUNOOB1
年龄:26
职位:高级程序员
薪水:1000.0
名字:RUNOOB2
年龄:21
职位:菜鸟程序员
薪水:500.0


常量在程序运行时，不会被修改的量。
在 Java 中使用 final 关键字来修饰常量，声明方式和变量类似：
final double PI = 3.1415927;

实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名

protected 访问修饰符不能修饰类和接口，方法和成员变量能够声明为 protected，但是接口的成员变量和成员方法不能声明为 protected。


访问控制和继承
请注意以下方法继承的规则：
父类中声明为 public 的方法在子类中也必须为 public。
父类中声明为 protected 的方法在子类中要么声明为 protected，要么声明为 public，不能声明为 private。
父类中声明为 private 的方法，不能够被继承。

非访问修饰符
为了实现一些其他的功能，Java 也提供了许多非访问修饰符。
static 修饰符，用来创建类方法和类变量。
final 修饰符，用来修饰类、方法和变量，final 修饰的类不能够被继承，修饰的方法不能被继承类重新定义，修饰的变量为常量，是不可修改的。
abstract 修饰符，用来创建抽象类和抽象方法。
synchronized 和 volatile 修饰符，主要用于线程的编程。

final 方法
类中的 final 方法可以被子类继承，但是不能被子类修改。
声明 final 方法的主要目的是防止该方法的内容被修改。
如下所示，使用 final 修饰符声明方法。
public class Test{
    public final void changeName(){
       // 方法体
    }
}
final 类
final 类不能被继承，没有类能够继承 final 类的任何特性。
实例
public final class Test {
   // 类体
}

abstract 修饰符
抽象类：
抽象类不能用来实例化对象，声明抽象类的唯一目的是为了将来对该类进行扩充。
一个类不能同时被 abstract 和 final 修饰。如果一个类包含抽象方法，那么该类一定要声明为抽象类，否则将出现编译错误。
抽象类可以包含抽象方法和非抽象方法。
实例
abstract class Caravan{
   private double price;
   private String model;
   private String year;
   public abstract void goFast(); //抽象方法
   public abstract void changeColor();
}


抽象方法
抽象方法是一种没有任何实现的方法，该方法的的具体实现由子类提供。
抽象方法不能被声明成 final 和 static。
任何继承抽象类的子类必须实现父类的所有抽象方法，除非该子类也是抽象类。
如果一个类包含若干个抽象方法，那么该类必须声明为抽象类。抽象类可以不包含抽象方法。
抽象方法的声明以分号结尾，例如：public abstract sample();。
实例
public abstract class SuperClass{
    abstract void m(); //抽象方法
}
 
class SubClass extends SuperClass{
     //实现抽象方法
      void m(){
          .........
      }
}


volatile 修饰符
volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。
一个 volatile 对象引用可能是 null。

public class MyRunnable implements Runnable
{
    private volatile boolean active;
    public void run()
    {
        active = true;
        while (active) // 第一行
        {
            // 代码
        }
    }
    public void stop()
    {
        active = false; // 第二行
    }
}
通常情况下，在一个线程调用 run() 方法（在 Runnable 开启的线程），在另一个线程调用 stop() 方法。 如果 第一行 中缓冲区的 active 值被使用，那么在 第二行 的 active 值为 false 时循环不会停止。
但是以上代码中我们使用了 volatile 修饰 active，所以该循环会停止。


所以继承需要符合的关系是：is-a，父类更通用，子类更具体。

 继承 - 维护性也提高，代码也更加简洁，提高代码的复用性（复用性主要是可以多次使用，不用再多次写同样的代码）

继承的特性
    子类拥有父类非private的属性，方法。
    子类可以拥有自己的属性和方法，即子类可以对父类进行扩展。
    子类可以用自己的方式实现父类的方法。
    Java的继承是单继承，但是可以多重继承，单继承就是一个子类只能继承一个父类，多重继承就是，例如A类继承B类，B类继承C类，所以按照关系就是C类是B类的父类，B类是A类的父类，这是java继承区别于C++继承的一个特性。
    提高了类之间的耦合性（继承的缺点，耦合度高就会造成代码之间的联系）。


继承关键字
继承可以使用 extends 和 implements 这两个关键字来实现继承，而且所有的类都是继承于 java.lang.Object，当一个类没有继承的两个关键字，则默认继承object（这个类在 java.lang 包中，所以不需要 import）祖先类。

implements关键字
使用 implements 关键字可以变相的使java具有多继承的特性，使用范围为类继承接口的情况，可以同时继承多个接口（接口跟接口之间采用逗号分隔）。
implements 关键字
public interface A {
    public void eat();
    public void sleep();
}
 
public interface B {
    public void show();
}
 
public class C implements A,B {
}



public class SuperDemo {
    public static void main(String []args) {
        new SubClass().showMessage();
    }
}
 
class SuperClass {
    int i = 50;
}
 
class SubClass extends SuperClass {
    int i =100;
    public void showMessage() {
        System.out.printf("super.i = %d, this.i = %d\n", super.i, this.i);
    }
}



final关键字
final 关键字声明类可以把类定义为不能继承的，即最终类；或者用于修饰方法，该方法不能被子类重写：
声明类：
final class 类名 {//类体}
声明方法：
修饰符(public/private/default/protected) final 返回值类型 方法名(){//方法体}
注:实例变量也可以被定义为 final，被定义为 final 的变量不能被修改。被声明为 final 类的方法自动地声明为 final，但是实例变量并不是 final



构造器
子类不能继承父类的构造器（构造方法或者构造函数），但是父类的构造器带有参数的，则必须在子类的构造器中显式地通过super关键字调用父类的构造器并配以适当的参数列表。
如果父类有无参构造器，则在子类的构造器中用super调用父类构造器不是必须的，如果没有使用super关键字，系统会自动调用父类的无参构造器。


重写(Override)
重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。即外壳不变，核心重写！
重写的好处在于子类可以根据需要，定义特定于自己的行为。 也就是说子类能够根据需要实现父类的方法。
重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常。例如： 父类的一个方法申明了一个检查异常 IOException，但是在重写这个方法的时候不能抛出 Exception 异常，因为 Exception 是 IOException 的父类，只能抛出 IOException 的子类异常。
在面向对象原则里，重写意味着可以重写任何现有方法。实例如下：
TestDog.java 文件代码：
class Animal{
   public void move(){
      System.out.println("动物可以移动");
   }
}
 
class Dog extends Animal{
   public void move(){
      System.out.println("狗可以跑和走");
   }
}
 
public class TestDog{
   public static void main(String args[]){
      Animal a = new Animal(); // Animal 对象
      Animal b = new Dog(); // Dog 对象
 
      a.move();// 执行 Animal 类的方法
 
      b.move();//执行 Dog 类的方法
   }
}
以上实例编译运行结果如下：
动物可以移动
狗可以跑和走

方法的重写规则
    参数列表必须完全与被重写方法的相同；
    返回类型必须完全与被重写方法的返回类型相同；
    访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为public，那么在子类中重写该方法就不能声明为protected。
    父类的成员方法只能被它的子类重写。
    声明为final的方法不能被重写。
    声明为static的方法不能被重写，但是能够被再次声明。
    子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为private和final的方法。
    子类和父类不在同一个包中，那么子类只能够重写父类的声明为public和protected的非final方法。
    重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。
    构造方法不能被重写。
    如果不能继承一个方法，则不能重写这个方法。

当需要在子类中调用父类的被重写方法时，要使用super关键字。
TestDog.java 文件代码：
class Animal{
   public void move(){
      System.out.println("动物可以移动");
   }
}
 
class Dog extends Animal{
   public void move(){
      super.move(); // 应用super类的方法
      System.out.println("狗可以跑和走");
   }
}
 
public class TestDog{
   public static void main(String args[]){
 
      Animal b = new Dog(); // Dog 对象
      b.move(); //执行 Dog类的方法
 
   }
}
以上实例编译运行结果如下：
动物可以移动
狗可以跑和走


重载(Overload)
重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。
    每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。
    最常用的地方就是构造器的重载。
    重载规则
    被重载的方法必须改变参数列表(参数个数或类型或顺序不一样)；
    被重载的方法可以改变返回类型；
    被重载的方法可以改变访问修饰符；
    被重载的方法可以声明新的或更广的检查异常；
    方法能够在同一个类中或者在一个子类中被重载。
    无法以返回值类型作为重载函数的区分标准。

重写与重载之间的区别
区别点	        重载方法	    重写方法    
参数列表	   必须修改	       一定不能修改
返回类型	   可以修改	       一定不能修改
异常	        可以修改	    可以减少或删除，一定不能抛出新的或者更广的异常
访问	        可以修改	    一定不能做更严格的限制（可以降低限制）
总结
方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。



Java 多态
    多态是同一个行为具有多个不同表现形式或形态的能力。
    多态就是同一个接口，使用不同的实例而执行不同操作
    多态性是对象多种表现形式的体现。

多态的优点
1. 消除类型之间的耦合关系
2. 可替换性
3. 可扩充性
4. 接口性
5. 灵活性
6. 简化性
多态存在的三个必要条件
    继承
    重写
    父类引用指向子类对象; 比如：Parent p = new Child();


多态的实现方式
方式一：重写/重载
方式二：接口
方式三：抽象类和抽象方法


Java 抽象类
在面向对象的概念中，所有的对象都是通过类来描绘的，但是反过来，并不是所有的类都是用来描绘对象的，如果一个类中没有包含足够的信息来描绘一个具体的对象，这样的类就是抽象类。
抽象类除了不能实例化对象之外，类的其它功能依然存在，成员变量、成员方法和构造方法的访问方式和普通类一样。
由于抽象类不能实例化对象，所以抽象类必须被继承，才能被使用。也是因为这个原因，通常在设计阶段决定要不要设计抽象类。
父类包含了子类集合的常见的方法，但是由于父类本身是抽象的，所以不能使用这些方法。
在Java中抽象类表示的是一种继承关系，一个类只能继承一个抽象类，而一个类却可以实现多个接口。




抽象方法
如果你想设计这样一个类，该类包含一个特别的成员方法，该方法的具体实现由它的子类确定，那么你可以在父类中声明该方法为抽象方法。
Abstract关键字同样可以用来声明抽象方法，抽象方法只包含一个方法名，而没有方法体。
抽象方法没有定义，方法名后面直接跟一个分号，而不是花括号。
public abstract class Employee
{
   private String name;
   private String address;
   private int number;
   
   public abstract double computePay();
   
   //其余代码
}
声明抽象方法会造成以下两个结果：
如果一个类包含抽象方法，那么该类必须是抽象类。
任何子类必须重写父类的抽象方法，或者声明自身为抽象类。



抽象类总结规定
1. 抽象类不能被实例化(初学者很容易犯的错)，如果被实例化，就会报错，编译无法通过。只有抽象类的非抽象子类可以创建对象。
2. 抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。
3. 抽象类中的抽象方法只是声明，不包含方法体，就是不给出方法的具体实现也就是方法的具体功能。
4. 构造方法，类方法（用static修饰的方法）不能声明为抽象方法。
5. 抽象类的子类必须给出抽象类中的抽象方法的具体实现，除非该子类也是抽象类。



Java 封装
在面向对象程式设计方法中，封装（英语：Encapsulation）是指一种将抽象性函式接口的实现细节部份包装、隐藏起来的方法。
封装可以被认为是一个保护屏障，防止该类的代码和数据被外部类定义的代码随机访问。
要访问该类的代码和数据，必须通过严格的接口控制。
封装最主要的功能在于我们能修改自己的实现代码，而不用修改那些调用我们代码的程序片段。
适当的封装可以让程式码更容易理解与维护，也加强了程式码的安全性。
封装的优点
1. 良好的封装能够减少耦合。
2. 类内部的结构可以自由修改。
3. 可以对成员变量进行更精确的控制。
4. 隐藏信息，实现细节。


实现Java封装的步骤
1. 修改属性的可见性来限制对属性的访问（一般限制为private），例如：
public class Person {
    private String name;
    private int age;
}
这段代码中，将 name 和 age 属性设置为私有的，只能本类才能访问，其他类都访问不了，如此就对信息进行了隐藏。
2. 对每个值属性提供对外的公共方法访问，也就是创建一对赋取值方法，用于对私有属性的访问，例如：
public class Person{
    private String name;
    private int age;
​
    public int getAge(){
      return age;
    }
​
    public String getName(){
      return name;
    }
​
    public void setAge(int age){
      this.age = age;
    }
​
    public void setName(String name){
      this.name = name;
    }
}
采用 this 关键字是为了解决实例变量（private String name）和局部变量（setName(String name)中的name变量）之间发生的同名的冲突。



Java 接口
接口（英文：Interface），在JAVA编程语言中是一个抽象类型，是抽象方法的集合，接口通常以interface来声明。一个类通过继承接口的方式，从而来继承接口的抽象方法。
接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。
除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。
接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。另外，在 Java 中，接口类型可用来声明一个变量，他们可以成为一个空指针，或是被绑定在一个以此接口实现的对象。



接口与类的区别：
    接口不能用于实例化对象。
    接口没有构造方法。
    接口中所有的方法必须是抽象方法。
    接口不能包含成员变量，除了 static 和 final 变量。
    接口不是被类继承了，而是要被类实现。
    接口支持多重继承。


接口特性
    接口中每一个方法也是隐式抽象的,接口中的方法会被隐式的指定为 public abstract（只能是 public abstract，其他修饰符都会报错）。
    接口中可以含有变量，但是接口中的变量会被隐式的指定为 public static final 变量（并且只能是 public，用 private 修饰会报编译错误。
    接口中的方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法。
抽象类和接口的区别
    1. 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
    2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的。
    3. 接口中不能含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
    4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。


接口的实现
当类实现接口的时候，类要实现接口中所有的方法。否则，类必须声明为抽象的类。
类使用implements关键字实现接口。在类声明中，Implements关键字放在class声明后面。
实现一个接口的语法，可以使用这个公式：
Animal.java 文件代码：
...implements 接口名称[, 其他接口, 其他接口..., ...] ...
实例
MammalInt.java 文件代码：
/* 文件名 : MammalInt.java */
public class MammalInt implements Animal{
 
   public void eat(){
      System.out.println("Mammal eats");
   }
 
   public void travel(){
      System.out.println("Mammal travels");
   } 
 
   public int noOfLegs(){
      return 0;
   }
 
   public static void main(String args[]){
      MammalInt m = new MammalInt();
      m.eat();
      m.travel();
   }
}
以上实例编译运行结果如下:
Mammal eats
Mammal travels

在实现接口的时候，也要注意一些规则：
    一个类可以同时实现多个接口。
    一个类只能继承一个类，但是能实现多个接口。
    一个接口能继承另一个接口，这和类之间的继承比较相似。

接口的多重继承
    在Java中，类的多重继承是不合法，但接口允许多重继承，。
    在接口的多重继承中extends关键字只需要使用一次，在其后跟着继承接口。 如下所示：
    public interface Hockey extends Sports, Event



标记接口
最常用的继承接口是没有包含任何方法的接口。
标识接口是没有任何方法和属性的接口.它仅仅表明它的类属于一个特定的类型,供其他代码来测试允许做一些事情。
标识接口作用：简单形象的说就是给某个对象打个标（盖个戳），使对象拥有某个或某些特权。
例如：java.awt.event 包中的 MouseListener 接口继承的 java.util.EventListener 接口定义如下：
package java.util;
public interface EventListener
{}
没有任何方法的接口被称为标记接口。标记接口主要用于以下两种目的：
建立一个公共的父接口：
正如EventListener接口，这是由几十个其他接口扩展的Java API，你可以使用一个标记接口来建立一组接口的父接口。例如：当一个接口继承了EventListener接口，Java虚拟机(JVM)就知道该接口将要被用于一个事件的代理方案。
向一个类添加数据类型：
这种情况是标记接口最初的目的，实现标记接口的类不需要定义任何接口方法(因为标记接口根本就没有方法)，但是该类通过多态性变成一个接口类型。



Java 包(package)
为了更好地组织类，Java 提供了包机制，用于区别类名的命名空间。
包的作用
1、把功能相似或相关的类或接口组织在同一个包中，方便类的查找和使用。
2、如同文件夹一样，包也采用了树形目录的存储方式。同一个包中的类名字是不同的，不同的包中的类的名字是可以相同的，当同时调用两个不同包中相同类名的类时，应该加上包名加以区别。因此，包可以避免名字冲突。
3、包也限定了访问权限，拥有包访问权限的类才能访问某个包中的类。
Java 使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。


开发者可以自己把一组类和接口等打包，并定义自己的包。而且在实际开发中这样做是值得提倡的，当你自己完成类的实现之后，将相关的类分组，可以让其他的编程者更容易地确定哪些类、接口、枚举和注释等是相关的。
由于包创建了新的命名空间（namespace），所以不会跟其他包中的任何名字产生命名冲突。使用包这种机制，更容易实现访问控制，并且让定位相关类更加简单。


让我们来看一个例子，这个例子创建了一个叫做animals的包。通常使用小写的字母来命名避免与类、接口名字的冲突。
在 animals 包中加入一个接口（interface）：
Animal.java 文件代码：
/* 文件名: Animal.java */
package animals;
 
interface Animal {
   public void eat();
   public void travel();
}
接下来，在同一个包中加入该接口的实现：
MammalInt.java 文件代码：
package animals;
 
/* 文件名 : MammalInt.java */
public class MammalInt implements Animal{
 
   public void eat(){
      System.out.println("Mammal eats");
   }
 
   public void travel(){
      System.out.println("Mammal travels");
   } 
 
   public int noOfLegs(){
      return 0;
   }
 
   public static void main(String args[]){
      MammalInt m = new MammalInt();
      m.eat();
      m.travel();
   }
}
然后，编译这两个文件，并把他们放在一个叫做animals的子目录中。 用下面的命令来运行：
$ mkdir animals
$ cp Animal.class  MammalInt.class animals
$ java animals/MammalInt
Mammal eats
Mammal travel


import 关键字
为了能够使用某一个包的成员，我们需要在 Java 程序中明确导入该包。使用 "import" 语句可完成此功能。
在 java 源文件中 import 语句应位于 package 语句之后，所有类的定义之前，可以没有，也可以有多条，其语法格式为：
import package1[.package2…].(classname|*);
如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略。


例子
下面的 payroll 包已经包含了 Employee 类，接下来向 payroll 包中添加一个 Boss 类。Boss 类引用 Employee 类的时候可以不用使用 payroll 前缀，Boss类的实例如下。
Boss.java 文件代码：
package payroll;
 
public class Boss
{
   public void payEmployee(Employee e)
   {
      e.mailCheck();
   }
}
如果 Boss 类不在 payroll 包中又会怎样？Boss 类必须使用下面几种方法之一来引用其他包中的类。
使用类全名描述，例如：
payroll.Employee
用 import 关键字引入，使用通配符 "*"
import payroll.*;
使用 import 关键字引入 Employee 类:
import payroll.Employee;
注意：
类文件中可以包含任意数量的 import 声明。import 声明必须在包声明之后，类声明之前。


## 设计原则
DRY - Don't repeat yourself

Encapsulate What Changes - 封装经常修改的代码

打开/关闭设计原则
OpenClosed Design Principle
    类、方法/函数应当是对扩展(新功能)开放，对修改闭合。这是另外一个优雅的SOLID 设计原则，以防止有人修改通过测试的代码。理想情况下假如您添加了新功能，那么您的代码要经过测试，这就是打开/关闭设计原则的目标。顺便说一句，SOLID中的字母“O”指的是打开/关闭设计原则。

单一职责原则
Single Responsibility Principle(SRP)
    单一职责原则是另外一个SOLID设计原则，SOLID中的字母“S”指的就是它。按照SRP，一个类修改的原因应当有且只有一个，或者一个类应当总是实现单一功能。如果您在Java中的一个类实现了多个功能，那么这些功能之间便产生了耦合关系；如果您修改其中的一个功能，您有可能就打破了这种耦合关系，那么就要进行另一轮测试以避免产生新的问题。


优先使用组合而非继承
Favor Composition over Inheritance
    如果可以的话，要优先使用组合而非继承。你们中的一些人可能为此争论，但我发现组合比继承更有灵活性。组合允许在运行时通过设置属性修改一个类的行为，通过使用多态即以接口的形式实现类之间的组合关系，并且为修改组合关系提供了灵活性。甚至 Effective Java也建议优先使用组合而非继承。


[Object Oriented Design Principles](https://www.codeproject.com/Articles/567768/Object-Oriented-Design-Principles)
[Object Oriented Design Principles](https://scotch.io/bar-talk/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)


Bob大叔提出并发扬了S.O.L.I.D五大原则，用来更好地进行面向对象编程，五大原则分别是：

The Single Responsibility Principle（单一职责SRP）
The Open/Closed Principle（开闭原则OCP）
The Liskov Substitution Principle（里氏替换原则LSP）
The Interface Segregation Principle（接口分离原则ISP）
The Dependency Inversion Principle（依赖反转原则DIP）

S.O.L.I.D五大原则之依赖倒置原则DIP
依赖倒置原则的描述是：

A. High-level modules should not depend on low-level modules.  Both should depend on abstractions.
   高层模块不应该依赖于低层模块，二者都应该依赖于抽象

B. Abstractions should not depend upon details.  Details should depend upon abstractions.
   抽象不应该依赖于细节，细节应该依赖于抽象
依赖倒置原则的最重要问题就是确保应用程序或框架的主要组件从非重要的底层组件实现细节解耦出来，这将确保程序的最重要的部分不会因为低层次组件的变化修改而受影响。

该原则的第一部分是关于高层模块和低层模块之间的耦合方式，在传统的分成架构中，高层模块（封装了程序的核心业务逻辑）总依赖于低层的一些模块（一些基础点）。当应用依赖倒置原则的时候，关系就反过来了。和高层模块依赖于低层模块不同，依赖倒置是让低层模块依赖于高层模块里定义的接口。举例来说，如果要给程序进行数据持久化，传统的设计是核心模块依赖于一个持久化模块的API，而根据依赖倒置原则重构以后，则是核心模块需要定义持久化的API接口，然后持久化的实现实例需要实现核心模块定义的这个API接口。

该原则的第二部分描述的是抽象和细节之间的正确关系。理解这一部分，通过了解C++语言比较有帮助，因为他的适用性比较明显。

不像一些静态类型的语言，C++没有提供一个语言级别的概念来定义接口，那类定义和类实现之间到底是怎么样的呢，在C++里，类通过头文件的形式来定义，其中定义了源文件需要实现的类成员方法和变量。因为所有的变量和私有方法都定义在头文件里，所以可以用来抽象以便和实现细节之前解耦出来。通过定只定义抽象方法来实现（C++里是抽象基类）接口这个概念用于实现类来实现。

DIP and JavaScript
因为JavaScript是动态语言，所以不需要去为了解耦而抽象。所以抽象不应依赖于细节这个改变在JavaScript里没有太大的影响，但高层模块不应依赖于低层模块却有很大的影响。

在当静态类型语言的上下文里讨论依赖倒置原则的时候，耦合的概念包括语义（semantic）和物理（physical）两种。这就是说，如果一个高层模块依赖于一个低层模块，也就是不仅耦合了语义接口，也耦合了在底层模块里定义的物理接口。也就是说高层模块不仅要从第三方类库解耦出来，也需要从原生的低层模块里解耦出来。

为了解释这一点，想象一个.NET程序可能包含一个非常有用的高层模块，而该模块依赖于一个低层的持久化模块。当作者需要在持久化API里增加一个类似的接口的时候，不管依赖倒置原则有没有使用，高层模块在不重新实现这个低层模块的新接口之前是没有办法在其它的程序里得到重用的。

在JavaScript里，依赖倒置原则的适用性仅仅限于高层模块和低层模块之间的语义耦合，比如，DIP可以根据需要去增加接口而不是耦合低层模块定义的隐式接口。

为了来理解这个，我们看一下如下例子：

$.fn.trackMap = function(options) {
    var defaults = {
        /* defaults */
    };
    options = $.extend({}, defaults, options);

    var mapOptions = {
        center: new google.maps.LatLng(options.latitude,options.longitude),
        zoom: 12,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    },
        map = new google.maps.Map(this[0], mapOptions),
        pos = new google.maps.LatLng(options.latitude,options.longitude);

    var marker = new google.maps.Marker({
        position: pos,
        title: options.title,
        icon: options.icon
    });

    marker.setMap(map);

    options.feed.update(function(latitude, longitude) {
        marker.setMap(null);
        var newLatLng = new google.maps.LatLng(latitude, longitude);
        marker.position = newLatLng;
        marker.setMap(map);
        map.setCenter(newLatLng);
    });

    return this;
};

var updater = (function() {
    // private properties

    return {
        update: function(callback) {
            updateMap = callback;
        }
    };
})();

$("#map_canvas").trackMap({
    latitude: 35.044640193770725,
    longitude: -89.98193264007568,
    icon: 'http://bit.ly/zjnGDe',
    title: 'Tracking Number: 12345',
    feed: updater
});
在上述代码里，有个小型的JS类库将一个DIV转化成Map以便显示当前跟踪的位置信息。trackMap函数有2个依赖：第三方的Google Maps API和Location feed。该feed对象的职责是当icon位置更新的时候调用一个callback回调（在初始化的时候提供的）并且传入纬度latitude和精度longitude。Google Maps API是用来渲染界面的。

feed对象的接口可能按照装，也可能没有照装trackMap函数的要求去设计，事实上，他的角色很简单，着重在简单的不同实现，不需要和Google Maps这么依赖。介于trackMap语义上耦合了Google Maps API，如果需要切换不同的地图提供商的话那就不得不对trackMap函数进行重写以便可以适配不同的provider。

为了将于Google maps类库的语义耦合翻转过来，我们需要重写设计trackMap函数，以便对一个隐式接口（抽象出地图提供商provider的接口）进行语义耦合，我们还需要一个适配Google Maps API的一个实现对象，如下是重构后的trackMap函数：

$.fn.trackMap = function(options) {
    var defaults = {
        /* defaults */
    };

    options = $.extend({}, defaults, options);

    options.provider.showMap(
        this[0],
        options.latitude,
        options.longitude,
        options.icon,
        options.title);

    options.feed.update(function(latitude, longitude) {
        options.provider.updateMap(latitude, longitude);
    });

    return this;
};

$("#map_canvas").trackMap({
    latitude: 35.044640193770725,
    longitude: -89.98193264007568,
    icon: 'http://bit.ly/zjnGDe',
    title: 'Tracking Number: 12345',
    feed: updater,
    provider: trackMap.googleMapsProvider
});
在该版本里，我们重新设计了trackMap函数以及需要的一个地图提供商接口，然后将实现的细节挪到了一个单独的googleMapsProvider组件，该组件可能独立封装成一个单独的JavaScript模块。如下是我的googleMapsProvider实现：

trackMap.googleMapsProvider = (function() {
    var marker, map;

    return {
        showMap: function(element, latitude, longitude, icon, title) {
            var mapOptions = {
                center: new google.maps.LatLng(latitude, longitude),
                zoom: 12,
                mapTypeId: google.maps.MapTypeId.ROADMAP
            },
                pos = new google.maps.LatLng(latitude, longitude);

            map = new google.maps.Map(element, mapOptions);

            marker = new google.maps.Marker({
                position: pos,
                title: title,
                icon: icon
            });

            marker.setMap(map);
        },
        updateMap: function(latitude, longitude) {
            marker.setMap(null);
            var newLatLng = new google.maps.LatLng(latitude,longitude);
            marker.position = newLatLng;
            marker.setMap(map);
            map.setCenter(newLatLng);
        }
    };
})();
做了上述这些改变以后，trackMap函数将变得非常有弹性了，不必依赖于Google Maps API，相反可以任意替换其它的地图提供商，那就是说可以按照程序的需求去适配任何地图提供商。

何时依赖注入？
有点不太相关，其实依赖注入的概念经常和依赖倒置原则混在一起，为了澄清这个不同，我们有必要来解释一下：

依赖注入是控制反转的一个特殊形式，反转的意思一个组件如何获取它的依赖。依赖注入的意思就是：依赖提供给组件，而不是组件去获取依赖，意思是创建一个依赖的实例，通过工厂去请求这个依赖，通过Service Locator或组件自身的初始化去请求这个依赖。依赖倒置原则和依赖注入都是关注依赖，并且都是用于反转。不过，依赖倒置原则没有关注组件如何获取依赖，而是只关注高层模块如何从低层模块里解耦出来。某种意义上说，依赖倒置原则是控制反转的另外一种形式，这里反转的是哪个模块定义接口（从低层里定义，反转到高层里定义）



五大原则之里氏替换原则LSP - 派生类型必须可以替换基类型

S.O.L.I.D五大原则之接口隔离原则ISP - Clients should not be forced to depend on methods they do not use.
不应该强迫客户依赖于它们不用的方法。