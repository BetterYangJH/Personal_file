# 基础

*   语句以分号结尾

*   编译

    ```shell
    g++ name.cpp -o new_name
    
    # c语言用gcc
    # c++用g++
    ```

*   执行

    ```shell
    ./name
    ```

*   标准库

    *   核心语言：提供所有构建块，包括变量、数据类型、常量等
    *   c++标准库：提供大量函数，用于操作文件、字符串等对象
    *   标准模板库STL：提供大量方法、数据结构，补充拓展c++的内容

# 命名空间

*   区分不同文件夹内相同名称的文件
*   定义：namespace name{}
*   调用内部方法或变量name::code
*   指明命名空间后调用不用加name：using namespace name

*   可嵌套可拆分

# 数据类型

*   基本类型

    | 类型        | 字节           | sign          | unsign  |
    | ----------- | -------------- | ------------- | ------- |
    | bool        | 和01不同       |               |         |
    | char        | 1              | -2^7到2^7-1   | 0到2^8  |
    | wchar_t     | 2              | -2^15到2^15-1 | 0到2^16 |
    | int         | 4              | -2^31到2^31-1 | 0到2^32 |
    | long int    | 8              |               |         |
    | float       | 4              |               |         |
    | double      | 8              |               |         |
    | long double | 16             |               |         |
    | void        | 无返回值无类型 |               |         |

*   枚举类型

    ```c++
    enum 枚举名｛
    标识符变量,
    标识符变量
    ....
    } 枚举变量
    ```

    默认第一个名称为0，第二个为1，如果哪个赋值则后面的递增

*   修饰符

    *   short用于int之前
    *   long用于int，double之前
    *   signed/unsigned用于int，char，short，long之前

*   类型限定

    *   const程序执行期间不能修改
    *   volatile编译器不需要优化
    *   restrict修饰的指针是唯一一种访问他所指向对象的方式

*   运算符

    *   算数运算符
    *   关系运算符
    *   逻辑运算符&&｜｜！
    *   位运算符&｜^（不同为1）～取反<<>>
    *   赋值运算符

*   其他

    *   sizeof
    *   ?:
    *   (...)把最后一个式子的值符给变量(类似js的_)
    *    .类/结构体成员
    *   (int)a强制准换
    *   &a取a地址(&&和，&与，&a取地址，int& a别名)
    *   *a按地址取对象

# 变量

*   声明：告诉编译器变量名称、类型、大小、函数名称、结构名称、大小等信息，但此时不会分配内存

    ```c++
    type name
    ```

*   定义：生命变量后分配内存，可以看作“定义=声明+分配内存”

*   局部变量：手动初始化
*   全局变量：自动初始化/手动初始化，自动的为0或\0或Null

*   常量：定义后不能修改

    *   整数：
        *   前缀0x/0/十进制
        *   后缀L有符号/U无符号

    *   浮点数：
        *   小数3.14
        *   指数3E5L

    *   布尔
        *   true
        *   false

    *   字符

        *   普通“”
        *   宽字符L“”

    *   定义方式

        *   使用#define预处理器，全局，编译时，不分配内存直接替换，可通过#undefine取消重新定义

            ```c++
            #define LINE 10
            ```

        *   使用const关键字，局部，分配内存，永久有效

            ```c++
            const int LINE=10
            ```

*   储存类：定义变量和函数的范围和生命周期

    *   static：在程序的生命周期内保持局部变量存在，不需要它每次进入离开域时创建和销毁

    *   extern：提供全局变量引用，所有程序文件可见，对于无法初始化的变量，会把变量名指向一个之前定义过的储存位置

    *   mutable：仅适用于类的对象，允许对象的成员代替常量，也就是说mutable成员可以通过const成员函数修改

    *   thread_local：声明的变量仅可以在其创建的线程上访问

        ```c++
        static int count = 10;
        ```

*   自定义结构

    *   结构体struct：可以储存不同类型变量数据

        ```c++
        struct name{
        	type1 name1,
        	type2 name2
        } objname;
        或
        #include <cstring>
        struct name{
        	type1 name1,
        	type2 name2
        } ;
        name obj1;
        strcpy(obj1.name1,Val)
        ```

    *   作为函数参数

        ```c++
        定义
            void func(struct typename name){name.key}
        调用
            func(obj)
        
        定义
            void func(struct typename *name){}
        调用
            func(&obj)
        ```

    *   使用指针

        ```c++
        struct typename *name
        name = &obj
        name->key  //必须用箭头访问成员
        ```

*   别名typedef：任意类型

    ```c++
    typedef struct type_name｛
    	type1 name1；
    ｝
    
    type_name name；
    ```

# 数据结构

*   数组：固定大小的相同类型元素顺序集合，支持多维

    *   声明

        ```c++
        type name[size]={val}
        或
        type name[size];name[index]=val
            
        type name[][]={{}{}}
        ```

        *   元素个数不能多于size
        *   不加size不限制个数
        *   索引赋值访问，索引从0开始

    *   形参
        *   void func（type *param）指针
        *   void func（type param[size]）定长
        *   void func（type param[]）不定长

    *   返回：int * func()
        *   c++不允许返回一个完整的数组作为函数的参数，通过指定不带索引的数组名来返回一个指向数组的指针
        *   c++不支持在函数外返回的局部变量地址，除非定义局部变量为static

*   字符串
    *   char greeting[6]={'H','E','L','L','O','\0'}
    *   char greeting[] ='hello' 自动结尾补\0

# 流程控制

*   循环
    *   for(;;){}
    *   while(){}
    *   do{}while{}

*   判断

    *   if(){}

    *   switch

        ```c++
        switch(expression){
            case condition:
                  break
            default:
        }
        ```

    *   ?:

*   控制

    *   break
    *   continue
    *   goto：label（任意纯文本）：任意语句

# 输入输出

*   输入：字节流从设备（磁盘驱动器，键盘，网络连接等）流向内存

*   输出：字节流从内存流向设备（显示屏，打印机，磁盘驱动器，网络连接等）

*   \<iostream>
    *   cin标准输入cin>>args输入值给参数
    *   cout标准输出args<<cout显示在显示屏
        *   cout.width设置域宽
        *   cout.fill（str）长度不足域宽用str填充
    *   cerr非缓冲标准错误流cerr<<str显示屏立即显示信息，常用语错误
    *   clog缓冲标准错误流clog<<str显示屏刷新缓存时显示信息，一般用于日志

*   \<iomanip>格式化输出
    *   setw设置域宽set(nun)<args不足用空格补在前面
    *   precision（int）显示小数位数
    *   setioflags（ios::flag）显示设置，加re前缀表示清除状态
        *   常见flag设置（可用｜同时连接多选项）
            *   boolalpha可以使用单词true和false表示布尔值
            *   oct用八进制显示
            *   dec用十进制显示
            *   hex用十六进制显示
            *   left左对齐
            *   right右对齐
            *   scientific科学计数法表示浮点数
            *   fixed普通方式表示浮点数
            *   showbase显示所有数值的基数
            *   showpoint显示小数点和额外的零
            *   showpos显示正负号
            *   skipws读取输入时跳过空白字符空格回车制表
            *   unitbuf每次插入后清空缓冲区
            *   internal将填充字符回到符号和数值之间
            *   uppercase16进制大写输出
            *   lowercase16进制小写输出

*   \<fstream>文件处理

# 类和函数

## 类

*   定义类

    ```c++
    #include <iostream>
    using namespace std;
    class Line //类定义
    {
        public: //公开的数据
            Line(); //构造函数声明
            Line(const Line &obj); //拷贝构造函数
            friend void printWidth(Box box); //友元函数
            ~Line(); //析构函数
        	type name；
            type fun() //普通成员函数声明
        private: //私有化数据防止访问       
            double length;
    }    
    
    // 成员函数定义
    //普通成员函数
    type Box::fun(){}
    // 构造函数 
    Line:Line(void){}
    // 拷贝函数
    Line:Line(const Line &obj)
    {
        ptr=new int;
        拷贝值
        *ptr=*obj.ptr    
    ｝
    // 友元函数
    void printWidth(Box box)
    // 析构函数
    Line:~Line(void){}    
    ```

*   成员类型

    *   公共成员publuc，可以通过对象.访问

    *   私有成员private

    *   保护成员protected

        | 访问      | public | protected | private |
        | --------- | ------ | --------- | ------- |
        | 同类/友元 | y      | y         | y       |
        | 派生类    | y      | y         | n       |
        | 外部类    | y      | n         | n       |

    *   静态成员static：只有一个副本，所有成员共享

        *   没有初始化时，创建一个对象默认设置0 
        *   不能在类内进行初始化 
        *   在类外通过classname::name进行初始化

    *   静态成员函数：static定义，没有类的对象也能通过classname::name调用

*   封装

    *   数据封装：捆绑数据和操作数据的函数
    *   数据抽象：仅向用户暴露接口，隐藏具体实现

*   继承

    ```c++
    class 子类 ：public/protected/private(默认) 父类1，xx 父类2
        
    //修饰符：成员前修饰决定是否暴露，类前修饰决定继承多少
    ```

    *   public：
        *   父类public子类public 
        *   父类protected子类protected 
        *   父类private不继承 
    *   protected
        *   父类public，protected子类protected
        *   父类private不继承
    *   private： 
        *   父类public，protected子类private 
        *   父类private不继承
    *   不继承的成员：private
    *   不继承的方法：
        *   构造函数，析构函数，拷贝构造函数
        *   重载运算符
        *   友元函数

*   多态

    ```c++
    class Shape
    {
        public:
            Shape(int a=0,int b=0){}
            int area(){}
    };
    class Rectangle:public Shape{
        public:
             Rectanle(int a=0,int b=0):Shape(a,b){}
        	// 构造函数后的冒号：分割作用，给成员变量赋值 
            virtual int area()
    }
    ```

*   调用类内方法

    *   内联：类名::方法
    *   对象：创建对象，对象.方法

## 函数

*   至少有main函数

*   定义：returntype name(type name1){}
*   传参：
    *   传值：内部可用不可变
    *   传指针：内部可用可改，创建时int name(int *a){*a}，使用时name(&a)
    *   传地址：内部可用可改，创建时int name(int &a){a}，使用时name(a)

*   匿名函数 [] (Parma){}

    *   []没有定义变量，使用未定义变量时报错
    *   [&]任何被使用到的外部变量隐式的引用传入
    *   [=]任何被使用到的外部变量隐式的传值传入
    *   [x，&y]x传值，y引用传入
    *   [&，x]x传值，其他引用传入
    *   [=，&y]x引用传入，其他传值
    *   使用\[=][&]的形式，lambda可直接使用this指针\[](){this->func()}()

    *   使用[]的形式，如果要用this指针必须显式传入\[this](){this->func()}()

*   虚函数

    *   动态链接/后期绑定：
        *   在基类中使用关键字virtual声明函数
        *   派生类中重新定义基类中定义过的虚函数时，不会静态链接到该函数。在程序任意点可以根据所调用的对象类型来选择调用的函数
    *   静态链接/前期绑定：
        *   在基类中使用virtual声明函数
        *   会使用父类定义过的函数，函数调用在程序执行前，编译器就准备好
    *   纯虚函数：先定义，无函数体  virtual int area()=0

*   构造函数

    *   每次创建类的新对象时执行
    *   用于为某些成员变量设置初始值，类似于init
    *   名称与类的名称完全相同
    *   不会返回任何值，也不会返回void

*   析构函数

    *   会在每次删除多创建的对象时执行
    *   名称与类相同，在前面加波浪号～name
    *   不会返回任何值，也不能带有任何参数
    *   析构函数有助于在跳出程序（比如关闭文件，释放内存等）前释放资源

*   拷贝构造函数

    *   创建对象时，使用同一类中之前创建好的对象来初始化新创建的对象
    *   使用情况：
        *   通过使用另一个同类型对象来初始化新对象
        *   复制对象把它作为参数传递给函数
        *   复制对象并从函数返回这个对象
    *   没有定义拷贝构造函数，编译器会自行创建一个
    *   如果类带有指针变量，并动态内存分配，则必须有拷贝构造函数

*   内联函数：

    *   在编译时，编译器就把该函数的代码副本放置在每个调用该函数的地方，而不是运行时调用，提高效率，空间换时间，所以一般内联函数都是比较短的需要复用的函数

    *   类的成员函数大多是内联函数

        ```c++
        inline int Max(int x,int y){}
        ```

*   友元类/函数：定义在类外部，但有权访问类的所有成员包括public，protected，private

# 指针

*   指针指向地址，本质是值为地址的特殊变量
*   每个变量占用一个内存位置，有唯一地址&获取
*   指针的值的实际数据类型为十六进制数，所指向的变量或常量的数据类型不同
*   声明type *name，空指针Null，值为0
*   使用name=&param
*   形参void func（type *name）
*   返回iny * func（）
*   支持多级指针，指向指针的地址的指针，可以用n+1级指针修改n级指针指向
*   指向数组地址int *p=int var []
*   指向类和结构class/struc *name=param
*   指针移动p++，p--
*   this：每个对象都能通过this指针访问自己的地址，this指针是所有成员函数的隐含参数，友元函数没有this指针，因为友元函数不是类的成员，只有成员函数才有

# 引用

*   给变量赋别名，用引用变量或变量名访问变量
*   和指针区别
    *   引用必须连接一块合法地址，指针可以为Null
    *   引用被初始化为一个对象，就不能改变指向，指针可以在任意时候改变指向
    *   引用必须在创建时被初始化，指针可以在任意时候初始化

*   语法：type& name=val

*   通过使用引用来代替指针，使程序更容易阅读维护

*   函数可以返回一个引用，方式与返回指针相同

*   当函数返回一个引用时，则返回一个指向返回值的隐式指针，函数就可以放在赋值语句左边当变量使用

*   被引用对象不能超出作用域，所以不能返回一个对局部变量的引用

*   可以返回一个对静态变量的引用

# 重载

*   重载决策：多次重载自动选择最合适的，不是后面覆盖前面

*   重载函数：参数个数类型或顺序必须不同

*   运算符重载

    ```c++
    class Name
    {
    	public：
    		Name operator+（const Name& b）｛｝
    }
    
    Name name1
    Name name2
    Name name3
    name3=name1+name2
    ```

*   可重载：
    *   双目算数运算符+-*/%
    *   关系运算符== != < > <= >=
    *   逻辑运算符|| && !
    *   单目运算符+-*&
    *   自增自减++--
    *   位运算符｜&～^<<>>
    *   赋值运算符= += -= *= /= %= |= ^= <<= >>=
    *   空间申请与释放new delete new[] delete[]
    *   其他运算符() -> , []

*   不可重载
    *   成员访问符.
    *   成员指针访问符->
    *   域运算符::
    *   长度运算符sizeof
    *   条件运算符?:
    *   预处理符号#

# 常用库

*   \<cmath>数学相关

    ```c++
    double cos/sin/tan/log/sort/fabs(double) 
    余弦，正弦，正切，对数，平方，绝对值 
    double pow/hypot(double,double) 
    指数，平方和的平方根 
    int abs(int) 
    绝对值 
    double floor(double)
    返回小等于传入参数的最大整数
    ```

*   \<cstdlib>随机数

    ```c++
    srand(unsigned int seed) j=rand() 随机数，不加srand生成的为伪随机数
    ```

*   \<cstring>字符串操作

    ```c++
    strcpy(str1,str2)复制s2到s1 
    strcat(str1,str2)连接字符串 
    strcmp(str1,str2)比较，相同0，1小返回负，2小返回正 
    strlen(str)字符串长度
    strchr(str,char)返回指针，指向str中char第一次出现位置 
    strstr(str1,str2)返回指针，指向str1中str2第一次出现位置
    ```

*   \<string>

    ```c++
    str3=str1+str2拼接 
    str1=str2复制 
    str.size()长度
    ```

*   \<crime>时间相关

    ```c++
    lock_t，size_t，time_t能把系统时间和日期表示为某种格式整数 tm把日期和时间以c结构的形式保存，tm结构如下 
    struct tm{    
        int tm_sec;秒    
        int tm_min;分
        int tm_hour;时
        int tm_mday;一月中第几天    
        int tm_mon;月    
        int tm_year;年，自1900算起    
        int tm_wday;一周中第几天    
        int tm_yday;一年中第几天    
        int tm_isdst;夏令时 
    ｝    
    ctime()转换成字符串 
    gmtime()转换成tm 
    time_t now =time(0)当前系统的日期时间 
    time_t time(time_t,*time)返回系统当前日历时间 
    char *ctime(const time_t *time)返回表示当地时间的字符串指针 
    struct tm *localtime(const time_t *time)返回tm格式的当地时间指针 
    clock_t clock(void)程序执行起，处理器所使用的时间 
    double difftime(time_t time2,time_t time1)time1和time2之间差的秒数 
    size_t strftime()格式化日期时间为指定模式  
    ```

*   \<fstream>文件操作

    ```c++
    数据类型 
        ofstream 输出文件流 用于创建文件并写入w 
        ifstream 输入文件流，用于从文件读取信息r 
        fstream 同时具备上面两种的文件流wr 
    成员函数 
    打开文件 
    ofstream outfile; 
    outfile.open(const char *path,mode1|mode2) 
    	ios::app 追加模式，所有写入追加到文件尾 
        ios::ate 文件打开后定位到末尾 
        ios::in  打开文件用于读 
        ios::out 打开文件用于写 
        ios::trunc 如果文件存在，其内容将在打开之后被截断，即把文件长度设为0
    关闭文件 
    putfile.close() 
    重定位指针 
        位置：
        ios::beg 开头 
        ios::end 结尾
        ios::cur 当前位置 
    file.seekg(n)定位到f开始往后的n字节 
    file.seekg(0,iOS::end)定位到文件末尾 
    file.seekg(n,iOS::cue)把读指针从当前位置后移n个字节 
    file.seekg(n,iOS::end)把文件指针从末尾往回移动n个字节
    ```

*   \<thread>线程操作

    ```c++
    std::this_thread::get_id()获取主线程id 
    std::this_thread::sleep_for()休眠 
    std::thread name(func)创建线程
    name.join()停止并回收
    ```

    

# 动态内存

*   内存结构
    *   栈：在函数内部声明的所有变量都占用栈内存
    *   堆：未使用的内存，程序运行时可动态分配

*   语法
    *   申请new
    *   删除delete

*   数组

    ```c++
    //一维
    分配
        int *array=new int [m]
    释放
        delete [] array
        
    //二维
    分配 
        int **array
        array=new int *[m]
    	for(int i=0;I<m;I++){
    		arrat[i]=new int [n]
    	}
    释放
        for(int i=0;I<m;I++){
    		delete [] array[i]
    	}
    	delete[] array
    
    //三维   
    分配
        int ***array
    	array =new int **[m]
    	for(int i=0;I<m;I++){
    		array[i] =new int *[n]
    		for(int j=0;j<n;j++){
    			array[i][j]= new int [h]
    		}
        }
    释放
        for(int i=0;i<m;I++){
    		for(int j=0;j<n;j++){
    			delete[] array[i][j]
    		}
    		delete[] array[i]
    	}
    	delete[] array
    ```

# 模板

*   泛型编程的基础，是创建泛型类或函数的蓝图和公式

*   使用户为类或函数声明一种一般模式，使得类中的某些数据成员或者成员函数的参数、返回值可以取得任意类型，编写逻辑时不限于类型

*   模版函数

    ```c++
    template <class T> void swap(T& a,T& b){}
    
    ab为int时swap(int &a,int &b)
    ab为str时swap(char &a,char &b)
    ```

*   模版类

    ```c++
    template <class T> class A
    {
        public:
    		T a;
    		T hy(T c,T &d)
    }
    template<class T1,class T2> void A<T1,T2>::h()
    ```

# 预处理器

*   #开头，编译之前需要完成的预处理
*   不是c++语句，没有分号结尾
*   #include把头文件包含到源文件中
*   #define创建符号常量
    *   #define NAME valu
    *   \#define NAME（args）（operation）

*   #if condition

    #endif

*   预定义宏（系统常量）
    *   \__LINE__行号
    *   \__FILE__文件名
    *   \__DATE日期
    *   \__TIME__时间

# SLTL标准模版库

## 容器

*   \<vector>向量
    *   push_back末尾插入，超过大小则扩容
    *   size大小
    *   begin返回开头的迭代器
    *   end返回末尾的迭代器

*   \<list>列表

*   \<map>映射

*   \<memory>

*   \<queue>队列

*   \<deque>双队列

*   <priority_queue>优先队列，元素次序是由作用于所储存的值对上的某种谓词决定的队列

*   \<set>集合，由节点组成的红黑树

*   \<multiset>多重集合

*   \<stack>栈

## 算法

*   \<numeric>
    *   accumulate(v.begin(),v.end(),0)从begin到end加到0上

*   \<functional>

*   \<algorithm>
    *   reverse(v.begin(),v.end())数组转置
    *   sort(v.begin(),v.end())排序

## 迭代器

*   \<iteration>
*   \<utility>

# Tips

*   显式隐式

    ```c++
    #include <iostream>
    using namespace std;
    class A{
    	int x;
    	public:
    }
    
    void main()
    {
    	A a1;隐式调用A()
    	A *a2=new A(10) 显式调用A(int a)重载后的A()
    	delete a2 隐式调用了析构函数~A()
    }
    程序结束隐式调用了析构函数~A()
    ```

*   &变量：引用地址

    *地址：访问地址的变量值

*   重写：相同名称参数相同，逻辑不同

    重载：相同名称参数不同

*   this相当于self，必须用->访问属性

*   需要隐藏private，除了自己谁都不能用

    需要半隐藏protected，子类友元可以用

    需要重写versual

*   只供使用void不返回数据

*   异常处理

    ```
    c++-python
    try-try
    catch-except
    throw-raise
    
    示例
    throw str
    try{}catch(){}
    try{}catch(){}
    ```

-   CONCAT(str1,str2)拼接字符串

-   指针定义*name，赋值或使用要存地址用&val或&name

-   函数只能返回一个对象，所以int/str单对象可以直接返回，数组/struct需要借助指针包装成一个元素

