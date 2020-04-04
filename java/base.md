# Java基础

## 1.编码

* Java使用Unicode字符集
    * Java 7 Unicode 6.0
    * Java 8 Unicode 6.2
* 默认编码方式为UTF-8
    * 单字节符号
        * 字节的第一位设为0
        * 后面7位为这个符号的 Unicode 码
    * n字节的符号（n > 1）
        * 第一个字节的前n位都设为1
        * 第n + 1位设为0
        * 后面字节的前两位一律设为10
        * 剩下的位表示这个符号的 Unicode 码

Unicode符号范围|UTF-8编码方式
--------------|-------------
十六进制|二进制
0000 0000-0000 007F | 0xxxxxxx
0000 0080-0000 07FF | 110xxxxx 10xxxxxx
0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx

* 此外还有UTF-16编码、UTF-32编码
* 参考：[http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)

## 2.常量

### 2.1 整数常量

- 0b11 二进制
- 1111 十进制
- 0111 八进制
- 0x11 十六进制

*下划线 _ 可以用于划分位，不记作实际的位*

### 2.2 浮点数常量

- float

`1e1f    2.f    .3f    0f    3.14f    6.022137e+23f`

- double

`1e1    2.    .3    0.0    3.14    1e-9d    1e137`

### 2.3 转义常量

转义符|含义
-----|-----
\ b |(backspace BS, Unicode \u0008) 
\ t |(horizontal tab HT, Unicode \u0009) 
\ n |(linefeed LF, Unicode \u000a) 
\ f |(form feed FF, Unicode \u000c) 
\ r |(carriage return CR, Unicode \u000d) 
\ " |(double quote ", Unicode \u0022) 
\ ' |(single quote ', Unicode \u0027) 
\ \ |(backslash \, Unicode \u005c) 

## 3. 数据类型

### 3.1 基础类型

- byte 1个字节
- char 2个字节
- short 2个字节
- int 4个字节
- long 8个字节 
- float 4个字节
- double 8个字节 

> int i = 0b_00000000_00000000_00000000_00000001;
> int j = -i;
> j = 0b_11111111_11111111_11111111_11111111;
> 补码（负数） = 正数的反码(0b_11111111_11111111_11111111_11111110) + 1

### 3.1.1 操作符

* 比较
    * <, >, <=, >=
    * ==, !=
* 数字操作
    * +, -
    * *, /, %
    * ++, --
* 位操作
    * & (与), | (或), ^ (异或)
    * ~ (位取反)
    * << (左移，低位补0)
    * >> (右移，正数高位补0，负数高位补1)
    * >>> (右移，高位补0)
* 布尔型
    * ==, !=
    * &, |, ^
    * &&, ||
    * ? :
    * \+ (转成String再相加 true/false)
    * cast
        * 整形或浮点型，转为布尔型时，非零值为true
        * 对象转为布尔型时，非null为true

### 3.2 引用类型

* Object
    * equals
    * clone
    * hashcode
* String
    * 不可变的`final class`
        * 用作hash值
        * 线程安全
    * Java8之前使用char[]
    * Java8之后使用byte[] + coder
    * String Pool
        * 保存着所有字符串字面量（literal strings），编译时期确定
        * 使用 String 的 intern() 方法
        * 运行时可以将字符串添加到 String Pool 中。


### 3.3 类型转换

* 向下转换
* 向上转换
* 装箱转换
* 拆箱转换
* 字符串转换
* rawType转换

### 3.4 缓存池

* boolean values true and false
* all byte values
* short values between -128 and 127
* int values between -128 and 127
* char in the range \u0000 to \u007F

new Integer(123) 与 Integer.valueOf(123) 的区别在于：
* new Integer(123) 每次都会新建一个对象；
* Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。
* -XX:AutoBoxCacheMax=\<size\> 指定Integer缓存池大小

## 4. 包、接口、类

### 4.1 访问权限

* 包总是可以访问的
* public的类和接口可以被任何代码访问
* 类和接口如果声明为包的访问权限，则只有在其声明的包内才能访问
* 没有声明修饰符则隐式的有包的访问权限
* 数组类型的访问权限由其元素决定
* 引用类型的成员或者类的构造器 ：
    * 默认为public有外部访问权限
    * private只有在内部有访问权限
    * protected在相同包内有访问权限，具体见细节

### 4.2 protected细节

* 类或者子类可以在类内部访问protected成员
* protected构造器 `super()`或者`E.super()`可以访问
* 匿名内部类实例`new C(...){...}`或`E.new C(...){...}`可以访问
* 其余不行

### 4.3 import语句

```
import java.util.Vector;
import java.util.*;
import static TypeName . Identifier ;
import static TypeName . * ;
```


