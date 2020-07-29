# 基础

*   cargo：获取包、打包，操作包和工程

    ```shell
    $cargo new projectname cargo build cargo run
    ```

*   输出

    ```rust
    println!(字符串，参数)  自动换行 
    print!()  参数对应占位符为{num} 
    
    num对应位置，从0开始，一个参数可以对应多个占位 
    {{}}转义输出{}
    其他转义\
    ```

*   变量

    *   常量const

    *   可变变量let mut

    *   不可变变量let

        ```rust
        const x = val;  
        常量不可改 
        不可重新赋值 
        
        let x = val; 
        重影：变量的名称可以被重新使用let a；let a 
        不可重新赋值let a；a= 
        
        let mut x=val; 
        重影：变量的名称可以被重新使用let a；let a 
        可重新赋值let a；a= 
        
        指定类型let a：type=
        ```

*   数据类型
    *   整数：u/i8/16/32/64/128

        *   整数中间可以用下划线分割，不影响解析，方便阅读大数

    *   浮点数f32/64

    *   布尔

    *   字符

    *   复合类型，类型不同，每个声明类型

        ```rust
        let x:(i32,f64,u8)=(500,6.4,1) 
        let (x,y,z) = tuple
        ```

    *   数组[]，类型相同，声明类型和个数

        ```rust
        let x:[i32,5]=[,,,,] 
        let x=[3;5]; 等同于[3,3,3,3,3] 
        x[2] 索引 
        x[0..3] 切片
        ```

    *   字符串

        ```rust
        原生let s=""
        标准库let s=String;;from("")功能更完善
        切片一定是引用类型
        ```

*   结构体struct Name{}

    ```rust
    struct Site{    
        domain:String,    
        name:String,    
        nation:String,    
        found:u32 
    } 
    let s=Site{    
        domain:String::from()    
        .... 
    } 
    
    部分修改：必须至少一处修改 
    let site= Site{    
        domain:String::from(), 修改的    
        .. 未变的 
    }    
    ```

    与c++比较：只能定义struct Name不能实例struct obj

    *   元组结构体struct Color(u8,u8,u8)

    *   输出结构体

        ```rust
        #[derive(debug)]  //调试库
        println!('{:?}',s) 
        输出格式 
        obj is StructName{k:v,k:v} 
        println!('{:#?}',s) 
        输出格式 
        obj is StructName{    
            k:v,   
            k:v 
        }    
        ```

*   运算
    
*   支持+=不支持++
    
*   语句：分号结尾

    表达式：没有分号，单独的算式或数字

*   注释
    *   开头///
    *   中间//或/*

*   内部并列，

    外部语句；

    表达式什么都不加

*   循环判断函数体实例化{}

    声明()

*   判断
    *   if condition {} else if condition {} else {} 不需要加()
    *   match xx{condition=>,_=>}

*   循环
    *   while condition {} 不需要()
    *   for和python相同，不能三元(;;)，只能for xx in yy
    *   无限循环loop{break}

*   枚举类

    ```rust
    #[derive(Debug)] 
    enum Book{    
        Papery,    
        Electronic(String),    
        Electronic(url:String), 
    } 
    
    fn main(){    
        let book = Book::Papery{index:1001};    
        let ebook = Book::Electronic{String::from("")};   
        let book = Book::Electronic{url:""};    
        // 访问属性：结构体用.，枚举类用match列举   
        match book{       
            Book::Papery{index}=>{},       
            Book::Electronic{url}=>{}   
        } 
    }    
    ```

*   match

    *   对枚举类分支

    *   对非枚举类分支选择处理，用_表示其他情况

        ```rust
        fn main(){    
            let t="abc";    
            match t{        
                "abc"=>println!(),        
                _ => println!()    
            } 
        }       
        ```

## 所有权

*   垃圾回收
    *   c/c++ ：手动回收
    *   java：JVM自动回收
    *   python：计数自动回收
    *   rust：所有权

*   rust中每个值都有一个变量，称其为所以者（变量）

*   一次只能有一个所有者（赋值）

*   当所有者不在程序运行范围时，该值将被删除（作用域）

*   确定内存-复制：栈 - 所有基本数据类型，x指向一份，y复制一份

    ```rust
    let x=5； let y=x； xy均可用
    ```

*   动态内存-移动：堆-字符串，x指向一份，移动给y

    ```rust
    let x=5； let y=x； 转移，x不能再用，释放时只剩y一个资源 
    let y=x.clone() 单纯的复制可以借助克隆，释放时有xy两个资源，都可用 
    ```

*   引用：指针，x指向一份，y指向x，

    ```rust
    let x=5； 
    let y=&x；     //相当于租借，只能使用不能修改
    let y=&mut x; //想要修改mut可变引用
    ```

*   所有数据类型
    *   本身
        *   基本数据类型  =都可用（复制）              
        *   字符串  
            *   =后可用（移动）                    
            *   .clone()都可用（复制）  
    *   地址
        *   =&  可访问不可用（指针）             
        *   -=&mut  可访问可用（复制）    

*   参数传入函数：移动所有权到形参
*   函数返回值：移动所以权到调用函数处

*   切片：start..end部分引用/复制
*   部分引用时原始字符串禁止修改

# 数据结构

*   向量：类似数组，类型相同 Vec\<T>

    ```rust
    //创建空向量 
    let vector:Vec<i32>=Vec::new(); 
    //通过数组创建向量 
    let mut vector = vec![1,2,4,8]; 
    //追加 
    vector.push(val); 
    //拼接 
    v1.append(&mut v2); 
    //取值 
    v[index] 
    或 
    match v.get(index){    
        Some(val)=>val.toString(),    
        None=>"None".toString() 
    } 
    //遍历 
    {:?},vector  
    ```

*   字符串

    ```rust
    let string=String::new()// 空 
    let mut hello=String::from("")// 非空 
    //转换 
    let one=1.to_string() //整数到字符串 
    let float=1.3.to_string() // 浮点数到字符串 
    let slice="slice".to_string()// 切片 
    //追加 
    s.push_str()//追加字符串 
    s.push()//追加字符 
    //拼接 
    let s3=s1+&s2； 
    let s=format!("{}{}",s1,s2); 
    //长度 
    s.len();//字节长度 
    s.chars().count()// 字符长度 
    //取字符 
    let a=s.chars()nth(num); 
    let sub=&s[1..3];
    ```

*   映射表

    ```rust
    use std::collections::HashMap; 
    let mit map=HashMap::new(); 
    //插入 
    map.insert(key,val)//存在覆盖 
    map.entry(key).or_insert(val)//不存在插入 
    //获取 
    map.get(key).unwrap() 
    //迭代器 
    for p in map.iter(){    
        println!("{:?}",p); 
    }   
    ```

# 函数

*   fn 名(参:类){体}

*   fn main自动执行

*   以表达式结尾，可返回表达式的值

*   函数体表达式，返回值给参数

    ```rust
    let y={    
        let x=1;    
        x+1 无分号，是表达式，不是语句 
    }    
    ```

*   无返回值：过程

    ```rust
    fn name(){}
    ```

    有返回值：函数

    ```rust
    fn name()->returnType{表达式或return结尾}
    ```

*   结构体方法：创建实例，调用方法，参数self

    第一个参数必须是&self

    ```rust
    struct Rectangle{    
        width:u32,    
        heigh:u32, 
    } 
    
    impl Rectangle{    
        fn area(&self)->u32{        
            self.width * self.heigh    
        }    
        fn wider(&self,rect:&Rectangle)->bool{       
            self.width>rect.width    
        } 
    } 
    
    fn main(){    
        let rect1=Rectangle{width:30,heigh:50};    
        let rect2=Rectangle{width:40,heigh:20};    
        println!("{}",rect1.wider(&rect2)); 
    }    
    ```

*   结构体关联函数：不用创建实例，impl::func

    ```rust
    #[derive(Debug)] 
    struct Rectangle{    
        width:u32,    
        heigh:u32, 
    } 
    impl Rectangle{    
        fn create(width:u32,heigh:u32)->Rectangle{       
            Rectangle{width,heigh}    
        } 
    } 
    
    fn main(){ 
        let rect =Rectangle::create(30,50); 
        println!("{:#?}",rect)   
    }
    ```

# 文件io

*   接收命令集参数std::env::args()

  得到字符串数组Args{inner,}

  inner表示绝对路径

  launch.json中arga可以设置默认参数

*   输出pingtln!

*   输入stdin().read_line(&x)

    ```rust
    use std::io::stdin; 
    stdin().read_line(&mut str_buf).except(""); 
    println!(str_buf);
    ```

*   读取文件

    ```rust
    use std::fs; 
    let text=fs::read_to_string(path).unwrap(); 
    
    // 大文件-buffer-流式     
    use std::io::prelude::*; 
    use std::fs; 
    let mut buffer=[0u8;5]; 
    let mut file=fs::File::open(path).unwrap(); // 循环本句循环读取file.read(&mut buffer).unwrap();
    ```

*   写入文件

    ```rust
    use std::fs; 
    //覆盖
    fs::write(abspath,content).unwrap(); 
    //追加
    fs::OpenOptions::new().append(true).open(path); 
    //OpenOptions设置打开的格式权限等设置 
    
    //大文件-buffer-流式 
    use std::fs; 
    use std::io::prelude::*; 
    let mut file=fs::File::create(path).unwrap(); file.write(content).unwrap();
    ```

# 面向对象

```rust
//second.js 
pub struct CalssName{    
    field:i32; 
} 
impl ClassName{    
    pub fn new(value:i32)->ClassName{        
        ClassName{            
            field:value        
        }   
    }   
    pub fn public_method(&self){       
        self.private_method();   
    }   
    pub fn private_method(&self){}        
}     

//main.js 
mod second; 
use second::ClassName; 
fn main(){    
    let object=ClassName::new(1024);    
    object.public_method(); 
} 
```

# 并发

*   创建线程

    ```rust
    let handle=thread::spawn(fun)
    
    //无参数fun推荐闭包|args|->返回值类型{}
    thread::spawn(||{})
    ```

*   休眠

    ```rust
    thread::sleep(Duration::from_millis(1))
    ```

*   等待子进程结束再停止程序

    ```rust
    handle.join.unwrap()
    ```

*   消息传递

    ```rust
    use std::stnc::mpsc; 
    let (tx,rx) = mpsc::channel(); 
    tx.send(val).unwrap(); 
    ...
    rx.recv().unwrap();
    ```

# 结构

*   包
    
*   有Cargo.toml文件管理文件夹，包含基本信息好人依赖项
    
*   箱Crate
    *   二进制程序文件或库文件，存在于包中
    *   树状结构，根是编译器开始运行时编译的源文件所编译的程序
    *   包含一个库“箱”，0个或多个二进制“箱”，至少包含一个箱（库或二进制）
    *   src目录下main.rs源文件，默认为二进制箱的根，编译后的二进制箱与包名相同

*   模块mod

    *   文件内最外层，包含函数

        ```rust
        mod modname
        {    
            public fn fun(){} 
        } 
        fn main(){    
            modname::fun::方法属性(); 
        }    
        ```

*   绝对引用从crate开始

    相对引用从super/mod开始

# 权限

*   公共public

    *   pub修饰的模块成员

        ```rust
        pub fn name(){}
        ```

*   私有private

    *   默认模块成员访问权为私有

        ```rust
        fn name(){}
        ```

    *   只有在与其平级的位置或下级的位置才能访问，不能从其外部访问

*   作用域use：讲内容引入到当前作用域，相当于import

    ```rust
    mod nation
    {    
        pub mod government{        
            pub fn govern(){}    
        } 
    } 
    
    use crate::nation::government::govern as nation_gavern; 
    fn main(){    
        nation_govern(); 
    }   
    ```

# 生命周期

*   以花括号区分生命周期边界

*   '生命周期注释，两个引用的位置生命周期一致

    ```rust
    fn main(){    
        struct Str<'a>{        
            content:&'a str    
        }    
        let s =Str{        
            content:"String"    
        };    
        println!(s.content)  
    }    
    ```

*   静态声明周期

    ```rust
    &'static str
    ```

    生命周期从程序运行开始到运行结束

*   泛型+特性+生命周期

    ```rust
    use std::fmt::Display; 
    fn longest_with_an_announcement<'a,T>(x:&'a str,y:&'a str,ann:T)->&'a str where T:Display {    
        println!("Announcement!{}",ann);    
        if x.len()>y.len(){        
            x    
        } else {       
            y   
        }     
    }  
    ```

# 泛型

*   类似c++模板，生命函数/结构体时不需要确定参数类型

    ```rust
    fn max<T>(array:&[T])->T{} 
    
    struct Point<T>{} 
    
    impl<T> Point<T>{    
        fn x(&self)->&T{        
            &self.x    
        } 
    }    
    ```

*   可以使用不同名字T1、T2等，在同一位置设置多个泛型

    也可以通过名称，对同一泛型多次使用

    ```rust
    fn name<T,U>(arg1:T,arg2:U) 
    fn name<T:Point>(arg1:T,arg2:T)
    ```

# 特性

*   类似接口，作为抽象模板

    ```rust
    trait Descriptive{    
        fn Describe(&self)->String{        
            String::form("[Object]") //表示接口可以有默认实现   
        } 
    } //Descriptive规定了实现者必须有describe方法    
    ```

*   实现结果体

    ```rust
    struct Person{} 
    impl Descriptive for Person{        
        fn Describe(&self)->String{        
            String::form("[Object]") //表示接口可以有默认实现
        }
    }
    ```

*   作为参数传递

    ```rust
    fn output(object:impl Descriptive+多个特性){    
        println!(object.describe); 
    }
    //任何实现了Descriptive特征的对象可以作为output的参数 
    //语法糖 fn output<T:Descriptive>(arg1:T,arg2:T) 
    fn func<T,U>(t:T,u:U)->i32 
    where T:Display+Clone,      
          U:Clone+Debug
    ```

# 错误

*   可恢复错误

*   不可恢复错误：访问数组末尾以外的位置

    *   panic!处理

        ```rust
        panic!("")
        ```

*   自定义错误

    ```rust
    use std::io; 
    use std::io::Read; 
    use std::fs::File; 
    
    fn read_text_from_file(path:&str)->Result<String,io::Error> {    
        let mut f=File::open(path)?;    
        let mut s=String::new();    
        f.read_to_string(&mut s);    
        OK(s) 
    } 
    
    fn main(){    
        let str_file=read_text_from_file("hello.txt");    
        match str_file{        
            OK(s)=>println!("{}",s),        
            Err(e)=>{            
                match e.kind(){                
                    io::ErrorKind::NotFound=>{                    
                        println!("No such file");                
                    },                
                    _=>{                    
                        println!("Cannot read the file");               
                    }            
                }                        
            }        
        } 
    }       
    ```