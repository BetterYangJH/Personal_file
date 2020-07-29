# 基础
* windows环境变量
  * JAVA_HOME:C:\Program Files(x86)\Java\jdk1.8.0_91
  * CLASSPATH:.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
  * Path:%JAVA_HOME%\bin;%JAVA_HOME\jre/bin;
  
* 编辑器：idea

* 基本实例

  ```java
  public class void HelloWorld{
    public static void main(String[] args){
        System.out.println();
    }
  }
  ```

* 运行：程序从main开始运行

  ```shell
  // 单文件编译
  $ javac name.java # 将.java
  // 包一起编译，会自动生成文件夹，源文件在test文件夹，包名为test
  $ javac -d . *.java
  // 源文件编译成字节码文件 .class
  // 单文件运行
  $ java name
  // 包运行，在包外
  $ java -cp pathto/test test.Run
  ```

# 语法
* 命名
  * 文件名：每个首字母大写
  * 类：每个首字母大写，和文件同名
  * 方法：小驼峰
  * 主方法：public static void main(Strin[] args)
* 源文件规则
  * 一个源文件只能有一个public类，可以有多个非publiv类
  * 源文件的名称应该和piblic类名一致
  * 顺序package - import - public
* 导入包：import java.io.*
* 修饰符
  * 访问控制修饰符：default，public，protected，private
  * 非访问控制修饰符：final，static，abstract，synchronized
* 标准输出
  * System.out.print普通输出
  * System.out.println结尾换行
  * System.out.printf格式化输出
## 变量
### 分类

*   局部变量
    *   在方法、构造方法或者语句块中定义，声明和初始化
    *   方法结束后，变量自动销毁
    *   访问修饰符不能用于全局变量

*   类变量(静态变量)
    *   定义在类中方法体外，必须声明为static类型
    *   public static final String XXX=val； 
    *   本类访问 XXX 其他类访问 类.XXX

*   实例变量(非静态变量)
    *   定义在类中方法体外，创建对象时实例化，可以被类中方法、构造方法和特定类的语句访问
    *   实例变量的值应该至少被一个方法、构造方法或者语句块引用，使得外部能够通过这些方式获取实例变量信息
    *   访问修饰符可以修饰实例变量
    *   实例变量对于类中的方法，构造方法或者语句块是可见的，一般情况下应该把实例变量设为私有，通过使用访问修饰符可以使变量对子类可见
    *   obj.name

*   常量：程序运行时不能修改，声明：final type NAME=val

*   运算符

    *   算数运算符+-*/%++--
    *   关系运算符==!=><>=<=
    *   位运算符&|^~>><<>>>按位后移补0
    *   逻辑运算符&&||!短路运算
    *   赋值运算符(+-*/%&|^>><<)=
    *   其他运算符
        *   条件运算符?:
        *   instanceof运算符:检查该对象是否是一个特定类型obj instanceof class

    *   优先级https://www.runoob.com/java/java-operators.html最下面

### 修饰符

用来定义类、方法或者变量，通常放在语句最前端

*   访问修饰符：保护对类变量方法构造方法的访问

    *   不写：同一文件可见

    *   private：类内可见

    *   public：包内可见

    *   protected：同一包内类子类可见

    *   范围

        | 范围           | 类       | 同包子孙 | 同包    | 不同包子孙       | 不同包 |
        | -------------- | -------- | -------- | ------- | ---------------- | ------ |
        | 标识符最大范围 | private  |          | default | protected        | public |
        | 继承           | 不能继承 |          |         | protected/public | public |

    *   对象

        | 接口   | 类     | 方法      | 变量      | 备注               |
        | ------ | ------ | --------- | --------- | ------------------ |
        | public | public | public    | public    | 全部               |
        |        |        | private   | private   | 类接口不能设置权限 |
        |        |        | protected | protected | 类接口不能设置权限 |
        |        |        | static    | static    | 类变量             |
        |        | final  | final     | final     | 最终               |

    *   default

        *   接口里的变量都隐式声明为public static final
        *   接口里的方法都默认访问权限为public

    *   private

        *   隐藏类的实现细节和，保护类的数据

        *   被声明为private的方法变量构造方法只能类内访问

        *   外部访问只能通过公共的getter方法访问

            ```java
            public class Logger{    
                private String format;    
                public String getFormat(){        
                    return this.format;    
                }    
                public void setFormat(String format){        
                    this.format = format;    
                } 
            }   
            ```

    *   protect

        *   基类的protected成员
            *   包内可见
            *   包内子类可见所有
            *   包外子类可见继承过的

    *   父类public子类public

    *   父类protected子类public或protected

    *   父类private不能被继承

*   非访问限制控制符

    见表2

    *   static
        *   变量：类变量，不能声明在方法体内
        *   方法：不能使用类的成员变量

    *   final终止于此
        *   变量：常量
        *   方法：可被继承不可被重写
        *   类：不可被继承
        *   常和static连同static final type name=val

    *   abstract

        *   类：不能实例化对象，只作为模板进行扩充

        *   一个类不能同时被abstract和final修饰

        *   有抽象方法必须抽象类，抽象类不一定有抽象方法

        *   任何继承抽象类的子类必须实现所有抽象方法，除非子类也是抽象类

            ```java
            普通方法 public void name(){}; 
            抽象方法 abstract void name();
            ```

    *   synchronized：声明的方法同一时间只能被同一线程访问

    *   volatile：保证线程同步性
        *   每次线程访问时，都强制从共享内存取成员变量最新值
        *   每次成员变量更新时，强制讲变化写入共享内存

    *   transient

        *   序列化的对象包含transient修饰的实例变量时，java虚拟机JVM跳过该特定变量

        *   该修饰符包含在定义的语句中，用来预处理类和变量的数据类型

            ```java
            public transient int name=val;//不会持久化 
            public int name;//持久化
            ```

## 数据类型

*   内置数据类型：六种数字类型+一种字符串+一种布尔

    | 类型    | 字节 | 有无符号 | 最小  | 最大   | 默认  | 类        | 备注             |
    | ------- | ---- | -------- | ----- | ------ | ----- | --------- | ---------------- |
    | byte    | 8    | 有       | -2^7  | 2^8-1  | 0     | Byte      | 大型数组代替整数 |
    | short   | 16   | 有       | -2^15 | 2^16-1 | 0     | Short     |                  |
    | int     | 32   | 有       | -2^31 | 2^32-1 | 0     | Integer   |                  |
    | long    | 64   | 有       | -2^63 | 2^64-1 | 0L    | Long      |                  |
    | float   | 32   | 单精度   | -2^31 | 2^32-1 | 0.0f  | Float     |                  |
    | double  | 64   | 双精度   | -2^63 | 2^64-1 | 0.0d  | Double    |                  |
    | boolean | 1    | 无       | false | true   | false | Boolean   | 不是字符串或整型 |
    | char    | 16   | 无       | -2^15 | 2^16-1 | 无    | Character | 字符             |
    | string  |      | 无       |       |        | null  | String    | 字符串           |

    *   MAX/MIN_VALUE最值，SIZE字节

    *   byte short int long float double char boolean
    *   byte short int long可以用80，10，160x进制表示

*   引用数据类型（实例化）
    *   引用类型的变量：引用变量，类似于指针，指向一个对象
    *   声明后类型不可变
    *   常见：对象，数组
    *   默认值null

*   自动类型转换
    *   不同数据可以混合计算
    
    *   byte,short,char>int>long>float>double
    
    *   不能对boolean类型进行类型转换
    
    *   把容量大的类型转换为容量小的类型时必须使用强制转换
    
    *   浮点数到整数的转换是截取整数部分，不是四舍五入
    
        ```java
        char a='a';
        int i =a;
        ```
    
*   强制类型转换
    
    ```java
    int i=123;
    byte b=(byte)i
    ```
## 数据结构

*   数组：
    *   值为相同type的数组type[]
*   声明
    *   type[] name={,,}
    *   type[] name=new type[size]
*   参数：public staitc void func(type[] name)
*   传参：func(new type[]{})
*   返回：public static type[] func()
*   多维度type\[][]
*   枚举Enumeration
*   位集合Bitset
*   向量Vector
*   栈stack：vector的子类，先进后出
*   字典Dictionaty
*   字典Map
*   哈希表Hashtable
*   属性Properties键值都为字符串
*   ArrayList可以动态修改得数组，没有固定大小限制
*   LinledList链表
*   HashMap字典
*   HashSet值不重复的字典

## 包

*   功能相似的接口和类的集合

*   每个源文件只能有一个包声明packet，可以多个引用import，有相同声明的可以互相引用

    ```java
    packet pkg1，pkg2
    ```

*   使用过某一个包的成员需要在java程序中明确导入该包

    ```java
    import pkg1，pkg2.class.fun
    ```

*   如果在同一个包中，一个类想使用本包的另一个类，那么该包名可以省略

## 类和方法

*   类.对象.方法 Sysrem.out.print

*   必须有main方法作为入口

*   构造方法：名称必须与类同名

    *   每个类都有构造方法，一个类可以有多个构造方法重载

    *   没有显式定义构造方法时编译器提供默认构造方法

    *   创建对象实例时至少调用一个构造方法

        ```java
        public class Puppy{    
            public Puppy(){};    
            public Puppy(String mame){};
        }
        ```

*   创建对象：classname name=new classname(args)

*   访问变量或方法：实例.变量或方法()

*   继承：class Base extends Sub{}

*   返回非void类型返回值的方法称为函数，返回void类型返回值的方法称为过程

*   支持方法重载，包括构造函数

*   获取命令行参数args[]

*   传参
    *   普通位置传参
    *   可变个数传参：必须在最后，格式为type... name

*   finalize()：垃圾收集器之前调用

### 默认方法 - defult

```java
public interface Name{
    default void name(){xxx}
}
可以继承
public class Name1 implements Name{
    public void name(){
        Name.super.name()
    }
}
可以覆盖
public class Name2 implements Name{
    public void name(){yyy}
}   
```

### 匿名方法 - lambda

```java
(参数)->单句表达式
或
(参数)->方法体

Collections.sort(names,(s1,s2)->s1.compareTo(s2))
```

*   一个参数不需要()

*   一个表达式不需要{}和return

* 只能引用标记了final的外层局部变量，不能内部修改定义在域外的局部变量

*   声明的局部变量隐式final

### 常用方法

*   xxxValue()数据类型转为xxx
*   compareTo()比较大小
*   equals()是否相等
*   valueOf()返回原生number值
*   toString()返回字符串表示的number值
*   parseInt()转为有符号十进制整数

## 接口

*   抽象的方法的集合
*   通过interface声明
*   接口和类
    *   接口语法和类大致相同，但接口不是类
    
    *   类描述对象的方法和属性，接口则包含类要实现的方法
    
*   一个实现接口的类，必须实现接口内描述的所有方 法，除非是抽象类
    
    *   接口中的成员变量只能是public static final，抽象类中的成员可以是各种类型
    
    *   不能实现：普通方法，只能void name(args)声明
    
        可以实现：defult默认方法，static静态方法
    
        如果要实现其他方法，用抽象类
    
*   接口-抽象类-实现类-实现子类-实例
*   接口没有构造方法，不能实例化
*   接口中所有方法必须是抽象方法，不能包含成员变量，除了static和final变量
*   接口不是被类继承，而是被类实现
*   接口之间支持多继承，类不能多继承
*   特殊
    *   方法：隐式指定为public abstract
    *   变量：隐式指定为public static final
    *   接口中方法不能在接口中实现，只能在接口的类中实现

*   声明和实现

    ```java
    interface Name{} 
    punlic class Classname implements Name{}
    ```

*   继承：接口继承和类相似，但支持多继承

    ```java
    public interface Sports {    
        public void setHomeTeam(String name); 
    } 
    public interface Hockey extends Sports {    
        public void homeGoalScores(); 
    } // 实际为两个方法，一个继承来的，一个自己实现的   
    ```

## 流程控制

*   循环
    *   while(){} 
    *   do{}while(); 
    *   for(;;){} 
    *   增强for：for(type x:iterative){}以此取数组元素

*   条件
    
*   if(){} else if(){} else
    
    *   switch
    
        ```java
        switch(expression){
            case value:
                xxx
                break;
            default:
                yyy
        }   
        ```
    
    *   枚举
    
        ```java
        enum Color
        {
            red,blue
        } 
        ```
    
*   控制
    
    *   break 
    *   continue

## 常用类

*   装箱：由编译器特别支持的包装行为
    *   内置数据类型被当作对象使用的时候，编译器会把内置类型装箱为包装类
    *   编译器也可以把一个对象拆箱为内置类型
    *   需要对类型操作时转化为对象
    *   声明type name=new type()
    *   使用属性或方法java.util

*   Number类/Math类
    *   对数字操作/执行基本数学计算的属性和方法
    *   compareTo，equals，toString，parseInt
    *   sin，cos，tan，PI，toDegrees
    *   https://www.runoob.com/java/java-number.html

-   Character类
    -   对单字符操作
    -   char c='A'
    -   Character ch=new Character(c)
    -   IsLetter,isDigit,isWhitespace,isUpperCase,isLowerCase,toUpperCase,toLowerCase,toString

-   string类
    -   对字符串操作，不可改，修改则生成新的
    -   length(),str1.concat(str2),format("%",args)
    -   https://www.runoob.com/java/java-strong.html

-   StringBuffer/StringBuilder(不是线程安全的，不能同步访问，但速度快)
    -   对字符串修改，不生成新的
    -   append，reverse，delete，insert，replace
    -   https://www.runoob.com/java/java-stringbuffer.html

-   Array类
    -   对数组修改
    -   https://www.runoob.com/java/java-array.html

-   Data/SimpleDataFormat/Calendar/GregorianCalendar

    -   对时间操作/解析时间/精确操作/公历日历

    *   https://www.runoob.com/java/java-data-time.html

## 错误和异常

*   异常：程序员无法预见，例如打开不存在的文件

*   错误：脱离程序员控制的问题，比如栈溢出

*   Throwable

    *   Error
    *   Exception
        *   IOException
        *   RuntimeException

    https://www.runoob.com/java/java-exception.html

*   捕获

    ```java
    try{} catch(){} catch(){} finally()
    ```

*   抛出

    ```java
    import java.io.* 
    public class classname {    
        public void fun(type args) throws XxxError,,{        
            throw new XxxError    } 
    }    
    ```

*   自定义异常

    *   所有异常必须是Throwable子类

    *   自定义检查性异常继承Exception类

    *   自定义运行时异常继承RuntimeException类

        ```java
        class MyException extends Exception{}
        ```

## 面向对象

### 继承

*   子类只能继承自一个父类，一个父类可以有多个子类

*   子类拥有父类非private属性和方法

*   拓展：子类可以拥有自己得属性和方法

*   重写：子类可以用自己的方式实现父类的方法

*   提高了类之间的耦合性，独立性差

*   实现

    *   继承类extends

        ```java
        public class A{} 
        public class B extends A{}  
        ```

    *   继承接口implements（通过继承多个接口实现类不支持的多继承功能）

        ```java
        public interface A{} 
        public interface B{} public class C implements A,B{}
        ```

*   this本类

    super父类

*   子类不会继承父类的构造器，需要super显式调用，若父类构造器无参数则不用super

*   重写：参数返回值不变，内部逻辑拓展或改变
    *   声明static的方法不能被重写，可以重复声明
    *   声明final的方法不能被重写
    *   子类和父类不在同一个包中，只能重写oublic和protected的非private方法
    *   构造方法不能重写
    *   如果不能继承一个方法，则不能重写

*   重载：方法名字相同，参数不同，返回值可以相同可以不相同，不同情况的参数使用不同版本的方法

### 封装

*   修改属性的可见性来限制对属性的访问(一般限制为private)

*   对每个值属性提供对外的公共访问接口，即一对取赋值方法，用于操作私有属性

    ```java
    public class Person{    
        private String name;    
        public String getName(){        
            return name;    
        }    
        public void setName(String name){        
            this.name = name;    
        } 
    }    
    ```

### 多态

*   同一个接口，使用不同的实例而执行不同的操作

*   父类引用指向子类对象

    ```java
    abstract class Animal{    
        abstract void eat(){} 
    }     
    class Cat extends Animal{    
        public void eat(){xxx} 
    }     
    public class Test{    
        punlic static void show(Animal a){        
            a.eat();        
            if(a instanceof Cat){            
                Cat c=(Cat)a;            
                c.work();        
            }    
        }   
        public static void main(String[] args){        
            show(new Cat());//以cat对象调用show方法         
            Animal a=new Cat();//向上转型        
            a.eat();//调用的是Cat.eat                
            Cat c=(Cat)a;//向下转型        
            c.eat();//调用的是Cat.eat        
    ```

*   实现:  接口/抽象类/重写

# 序列化

*   一个对象可以被表示为一个字节序列，该字节序列包括该对象的数据、有关对象的类型的信息和存储在对象中数据的类型
*   将序列化对象写入文件之后，可以从文件中读取出来，并且对它进行反序列化，可以用来在内存中新建对象
*   一个类的对象要想序列化成功，必须满足两个条件：
    *   该类必须实现 java.io.Serializable 接口
    *   该类的所有属性必须是可序列化的。如果有一个属性不是可序列化的，则该属性必须注明是短暂的

*   检验一个类的实例是否能序列化： 只需要查看该类有没有实现 java.io.Serializable接口

*   序列化：序列化对象： .ser 

    ```java
    import java.io.*;
     
    public class SerializeDemo
    {
       public static void main(String [] args)
       {
          Employee e = new Employee();
          FileOutputStream fileOut =new FileOutputStream("emp.ser");
          ObjectOutputStream out = new ObjectOutputStream(fileOut);
          out.writeObject(e);
          out.close();
          fileOut.close();
       }
    }
    ```

*   反序列化：

    ```java
    import java.io.*;
    
    public class DeserializeDemo
    {
       public static void main(String [] args)
       {
          Employee e = null;
          FileInputStream fileIn = new FileInputStream("emp.ser");
          ObjectInputStream in = new ObjectInputStream(fileIn);
          e = (Employee) in.readObject();
          in.close();
          fileIn.close();
        }
    }
    ```

    

# 泛型

*   提供编译时的类型安全检查机制，本质是把数据类型作为参数

*   声明时需要包含一个类型参数声明（尖括号），在返回类型之前

*   可以包含一个或多个不同的类型参数，根据名字区分，参数间逗号隔开，一个泛型参数，也被称为一个类型变量

    ```java
     public static <E> void name(E[] arg)
    ```

*   可以用于声明返回值

*   泛型类：在类名后面添加参数声明

    ```java
    public class Name<T>{    
        private T t;    
        public static void main(String[] args){        
            Name<Integer> iName=new Name<Integer>();    
        } 
    }   
    ```

*   有类型种类范围限制的类型参数为有界类型参数，例如，一个操作数字的方法只能是Number类或者子类的实例

    ```java
    public static <T extends Comparable<T>> T maximum(T x);
    ```

*   类型通配符：一般用？代替具体类型参数
    *   通配符上限List<? extends Number>data
    *   通配符下限List<? super Number>data

# 多线程

*   每一个线程都有一个优先级，其取值范围是1-10的整数，默认会分配一个优先级5
*   具有较高优先级的线程对程序更重要，并且应该在低优先级的线程之前分配处理器资源。但是，线程优先级不能保证线程执行的顺序，而且非常依赖于平台。

## 创建方法

*   通过实现 Runnable 接口：需要通过start调用run

    ```java
    class RunnableDemo implements Runnable {
       private Thread t;
       RunnableDemo( String name) {} 
       public void run() {}   
       public void start () {
             t = new Thread (this, threadName);
             t.start ();
       }
    }
     
    public class TestThread {
       public static void main(String args[]) {
          RunnableDemo R1 = new RunnableDemo( "Thread-1");
          R1.start(); 
       }   
    }
    ```

*   通过继承 Thread 类本身：必须重写run，调用start，本质上也是实现runnable

    ```java
    class ThreadDemo extends Thread {
       private Thread t;
       ThreadDemo( String name) {}
       public void run() {}  
       public void start () {
          if (t == null) {
             t = new Thread (this, threadName);
             t.start ();
          }
    }
     
    public class TestThread {
       public static void main(String args[]) {
          ThreadDemo T1 = new ThreadDemo( "Thread-1");
          T1.start();
       }   
    }
    ```

*   通过 Callable 和 Future 创建线程

    *   创建 Callable 接口的实现类，并实现 call() 方法，该 call() 方法将作为线程执行体，并且有返回值。
    *   创建 Callable 实现类的实例，使用 FutureTask 类来包装 Callable 对象，该 FutureTask 对象封装了该 Callable 对象的 call() 方法的返回值。
    *   使用 FutureTask 对象作为 Thread 对象的 target 创建并启动新线程。
    *   调用 FutureTask 对象的 get() 方法来获得子线程执行结束后的返回值。

    ```java
    public class CallableThreadTest implements Callable<Integer> {
        public static void main(String[] args)  
        {  
            CallableThreadTest ctt = new CallableThreadTest();  
            FutureTask<Integer> ft = new FutureTask<>(ctt);  
            new Thread(ft,"有返回值的线程").start();  
        }
    }
    ```

*   对比

    *   采用实现 Runnable、Callable 接口的方式创建多线程时，线程类只是实现了 Runnable 接口或 Callable 接口，还可以继承其他类。
    *   使用继承 Thread 类的方式创建多线程时，编写简单，如果需要访问当前线程，则无需使用 Thread.currentThread() 方法，直接使用 this 即可获得当前线程。

# 数据库

*   下载jar库文件

*   连接

    ```java
    import java.sql.* 
    // 加载驱动 
    Class.forName("com.mysql.cj.jdbc.Driver") coon=DriverManager.getConnection(
        "jdbc:mysql://localhost:3306/DBName",    
        "root",
        "password" 
    ) 
    ```

*   操作

    ```java
    // 创建对象 
    stmt=coon.createStatement(); 
    // 原生命令 
    ResultSet rs=stmt.executeQuery(sql); 
    // 获取字段 
    rs.getInt(name)/getString(name)
    ```

*   关闭

    ```java
    rs.close(); 
    stmt.close(); 
    coon.close();
    ```

# Tips

*   休眠

    ```java
    import java.util.*; 
    Thread.sleep(num);
    ```

*   测量时间间隔

    ```java
    import java.util.* 
    System.currenTimeMillis()    
    xx 
    System.currentTimeMillis()    
    ```

*   正则

    ```java
    import java.util.regex.Matcher; 
    import java.util.regex.Pattern; 
    String pattern='.*'; 
    Pattern r=Patrern.compile(pattern);
    Matcher m=r.matcher(liner)
    ```

    https://www.runoob.com/java/java-regular-expressions.html
    
*   Optional类：可以保存null，解决空指针

*   时间https://www.runoob.com/java/java8-datetime-api.html

*   Base64 https://www.runoob.com/java/java8-base64.html

*   引用方法类：函数/对象::函数

*   流：连续

*   函数式接口：有且仅有一个抽象方法，没有或有若干个非抽象方法的接口，可以被隐式转换为Lambda表达式

    ```java
    // 定义
    @FunctionalInterface
    interface Name{
        void name(String arg);
    }
    // 实现    
    Name.name=arg->{}
    ```

    https://www.runoob.com/java/java8-functional-interfaces.html