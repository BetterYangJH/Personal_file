# java.io.*

*   该包包含所有操作输入输出需要的类

## 字符操作

*   输入

    *   创建buffer

        ```java
        BufferedReader b=new BefferedReader(    
            new InputStreamReader(
                System.in
            ) 
        );      
        ```

    *   读取多字符

        ```java
        import java.io.* 
        public class BRRead{    
            public static void main(String[] args) throws IOException{        
                BufferedReader br=new BufferedReader(            
                    new InputStreamReader(System.in)        
                ));        
                char c;        
                do{            
                    c=(char) br.read();          
                } while(c!='q');    
            } 
        }
        ```
        *   每次调用read()方法，从输入流读取一个字符并作为整数值返回
        *   当流结束时返回-1
        *   该方法抛出IOException

    *   读取字符串

        ```java
        import java.io.* 
        public class BRReadLines{    
            public static void main(String[] args) throws IOException{        
                BufferedReader br=new BufferedReader(            
                    new InputStreamReader(System.in)        
                ));        
                String str;        
                do{            
                    str= br.reaine();          
                } while(!str.equals("end"));   
            } 
        }      
        ```

*   输出

System.out.write/print/println/printf

![img](https://www.runoob.com/wp-content/uploads/2013/12/iostream2xx.png)

## 文件操作

https://www.runoob.com/java/java-file.html

*   读取

    ```java
    InputStream f=new FileInputStream(path) 
    或 
    File f=new File(path) 
    InputStream out=new FileInputStream(f) 
        
    方法 
    close() 关闭文件输入流，释放资源 
    finalize() 清除与文件链接，确保不再引用文件输入流时close 
    read(int r) 从输入流对象读取指定字节数据，返回下一字节数据，如果是结尾返回-1 
    read(byte[] r) 从输入流对象读取r.length长度子节，返回读取字节数，如果是结尾返回-1 
    available() 不受阻塞地从输入流对象读取的字节数，返回整数值
    ```

*   写入

    ```java
    不存在则创建，存在则打开 
    OutputStream f=new FileOutputStream(path) 
    或 
    File f=new File(path) OutputStream 
    out=new FilePutputStream(f,'UTF-8') 
        
    方法 
    close() 关闭文件输出流，释放资源 
    finalize() 清除与文件链接，确保不再引用文件输出流时close 
    write(int w) 把指定字节写到输出流 
    write(byte[] w) 把指定数组中w.length长度的字节写到输出流
    ```

*   其他文件IO

    *   创建文件夹

        ```java
        import java.io.File 
        String dirname=path 
        File d=new File(dirname) 
        d.midirs()/mkdir()
        ```

    *   读取目录

        ```java
        import java.io.File 
        String dirname=path 
        File d=new File(dirname) 
        String s[]=d.list()
        ```

    *   判断是否是目录

        ```java
        import java.io.File 
        String dirname=path 
        File d=new File(dirname) 
        d.isDirectory()
        ```

    *   删除目录

        ```java
        import java.io.File 
        File d=new File(dirname) 
        删除文件 
        delete(d) 
        删除目录 
        deleteFolder(d)
        ```

        