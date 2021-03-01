# Notes

## 指针

### const 修饰指针

记忆方法： 指针常量(指针是常量)  ； 常量指针(常量的指针)。 

​                    const 修饰谁，谁就不能更改

#### 常量指针

- 指针的指向**可以**修改，指针指向的指**不可以**修改

  ``` cpp
  int a = 10;
  int b = 20;
  const int *p = &a;
  /*可以*/     p = &b; 
  /*不可以*/// *p = 20；
  ```

  

#### 指针常量

- 指针的指向**不可以**修改，指针指向的值**可以**修改

  ``` cpp
  int a = 10;
  int b = 20;
  int * const p = &a;
  /*不可以*///  p = &b; 
  /*可以*/     *p = 20；
  ```



#### const 即修饰指针，又修饰常量

- 指针的指向**不可以**修改，指针指向的值**不可以**修改

  ``` cpp
  int a = 10;
  int b = 20;
  int * const p = &a;
  /*不可以*///  p = &b; 
  /*不可以*/// *p = 20；
  ```



## C++核心编程

本阶段主要对c++面向对象详细讲解

### 1. 内存分区模型

执行程序时，**4个区域**

- 代码区： 存放函数体的二进制代码，由操作系统进行管理
- 全局区：存放 全局变量、静态变量 和 常量
- 栈区：    由编译器自动分配和释放，存放函数的参数值、局部变量等
- 堆区：    由程序员分配和释放，若程序员不释放，程序结束时由操作系统回收。

**意义**：不同区域存放的数据，赋予不同生命周期，更灵活





#### 1.1 程序运行前

程序编译后生成可执行文件，未执行该可执行程序前，分为两个区域:

##### 代码区

- 存放CPU执行的机器指令

- 代码区是**共享**的：内存中只有一份代码即可

- 代码区是**只读**的

- 理解:

  代码区 是一张贴墙上的告示，谁都可以看(共享)，但是谁都不能改(只读)。

  共享是为了一张告示让谁都可以看见要做什么，而不需要给每个人都发一封私信告知(减少多次运行消耗)；

  只读是为了防止别人篡改(安全与防止误操作)

##### 全局区

- 包含

  - **全局变量** 

  - **静态变量** (static)

  - **常量区**： 

    - **字符串常量**

    - **其他常量** - (const修饰的**全局变量**) 

      [**注意**] ==const修饰的局部变量 **不在** 全局区==

- 该区域数据 在程序结束后，==由操作系统释放==



#### 1.2 程序运行后

##### 栈区

- 编译器自动分配释放
- 存放: **局部变量** 、 **函数形参** 



##### 堆区

- 程序员分配释放，若程序员不释放，程序结束时由操作系统回收
- 用**new**关键字开辟内存

```cpp
#include <iostream>
int * func()  
{
    //指针p 是局部变量，放在栈上。指针指向的new出来的地址(存放着数据10)，是放在堆区的
    int *p = new int(10);
    return *p;
}
int main()
{
    //ptr 执行func中在堆区new的地址
    int *ptr = func();
    return 0;
}
```



#### 1.3 new 操作符

- new 在堆区开辟数据，返回该数据对应类型的指针
- delete 释放
- `new 数据类型`
- 注意 delete数组时，需要加 []



``` cpp
int * func()
{
    //new 返回该数据对应类型的指针
    
    //new一个int空间，存个整型数 5 
    int * p_num = new int(5);
    //delete 它
    delete p_num;
    
    //new一个长度为10的int数组空间，返回首地址
    int * arr =  new int[10];
    for(int i=0; i<10; i++)
    {
        arr[i] = i+100； 
    }
    
    //释放数组，需要加[]
    delete[] arr;
    
}
```







### 2. 引用

#### 2.1 引用的基本使用

**作用**：给变量起别名

**语法**： `数据类型 &别名 = 原名`

#### 2.2 引用注意事项

- 引用**必须初始化**

- 引用在初始化后，**不可以**改变它所绑定的变量

  

  ``` cpp
  int a = 10;
  int &b = a;  //引用必须初始化 之后也不能改，这一步就把 b 和 a 绑死了
  ```

  



#### 2.3 引用做函数参数

**作用**：函数传参时，可以利用引用让形参修饰实参 （更安全、开销小）

**优点**：简化指针修改实参

``` cpp
//引用传递
void test_func(int &a, int &b)
{
    int temp = a;
    a = b;
    b = temp
}
int main()
{
    int m_a = 10;
    int m_b = 20;
    test_func(m_a, m_b);
    
    return 0;
}
```







#### 2.4 引用做函数返回值(特定情况下)

引用可以做为函数的返回值

**注意**： **不可以**返回局部变量的引用

用法： 如果函数的返回值是引用，则这个函数调用作为左值。 (换言之，如果函数做左值，那函数的返回值必须是引用)

``` cpp
int& test()
{
    static int a = 20;  //静态变量 - 存放在 全局区
    int b = 30;         //局部变量 - 存放在 栈区
    return a;
}

int main()
{
    int& ref2 = test();
    cout << "[1]ref2 is " << ref2 << endl;
    cout << "[2]ref2 is " << ref2 << endl;
    
    test() = 123; //此时 test() 是 a的引用(函数返回值就是个引用)，所以可以被赋值
    
    cout << "[3]ref2 is " << ref2 << endl;
    cout << "[4]ref2 is " << ref2 << endl;

    return 0;
}
/*  output：
 *  [1]ref2 is 20
 *  [2]ref2 is 20
 *  [3]ref2 is 123
 *  [4]ref2 is 123
*/
```



#### 2.5 引用的本质 - 指针常量

==引用在C++内部实现是一个**指针常量**==

``` c++
int a = 10;

int& ref = a;  //自动转换为 int * const ref = &a; 
               //指针常量 的 指针指向不可修改 (所以引用不可更改)
ref = 20;      //内部发现ref是引用，自动转换为: *ref = 20；

```





#### 2.6 常量引用

**作用**：常量引用**const 修饰形参**，防止误操作 (禁止函数内部修改)

``` cpp
void showValue(const int& v)  
{
    cout << v << endl;
}
```



**其它**

```cpp
//[非法操作-不能给一个数起别名] int & ref = 10;
```

```cpp
const int & ref = 10; 
//上面的语句 被编译器转换为: 
int temp = 10;  const int & ref = temp;   //也就相当于 const int * const ref = temp
//此时ref只读，不可修改
```





### 3. 函数提高

#### 3.1 函数默认参数

在 C++中，函数的形参列表中的形参 可以有默认值

语法: `返回值类型 函数名 (参数= 默认值) { 函数体; }`



##### **注意事项**： 

1. **默认实参必须在形参列表的结尾** （有默认参数之后的 都必须有默认参数）

```cpp
//有默认实参
int func_1(int a, int b=20, int c=30)
{
    return a + b + c;
}
//无默认实参
int func_2(int a, int b, int c)
{
    return a + b + c;
}
int main()
{
    int sum_1 = func_1(10);  //有默认实参，可以缺省有实参的参数
    int sum_2 = func_2(10, 20, 30);
    int sum_3 = func_1(10, 50, 60); //此时没有缺省，使用实际传入的值
    return 0;
}
```



2. **函数声明 和 函数实现 只能有一个有默认参数**

``` cpp
//函数声明
int test_a(int a, int b=20, int c=30)；
//函数定义
int test_a(int a, int b, int c)
{
    return a + b + c;
}

[或者]

//函数声明
int test_b(int a, int b, int c)；
//函数定义
int test_b(int a, int b=20, int c=30)
{
    return a + b + c;
}
```





#### 3.2 函数占位参数

递增运算符重载会用到占位函数（后面章节）



**语法**：`返回值类型 函数名(数据类型 ){}`

- 函数占位参数需要传参， 见 `func_1(...)` 

- 占位参数 可以有默认参数, 见 `func_2(...)`

```c++
void func_1(int a, int)
{
    //......    
}
void func_2(int a, int  = 10)
{
    //......
}
int main()
{
    func_1(10, 20);
    func_2(10);
    func_2(10, 20);
    
    return 0;
}
```





#### 3.3 函数重载

##### 3.3.1 函数重载概述

**作用**：函数名可以相同，提高复用性。

**函数重载的条件**：

- 同一个作用域下
- 函数名称相同
- 函数参数 **类型不同** 或 **个数不同** 或 **顺序不同**



**示例**：

```c++
void func(int a, int b)  //1
{
    cout << "int a, int b " << a << endl;
}
void func(int a, double b)  //2
{
    a = a + b;
    cout << "int a, double b " << a << endl;
}
void func(double a, int b)  //3
{
    a = a + b;
    cout << "double a, int b " << a << endl;
}

int main()
{
    func(10, 20);    //调用1 - output [int a, int b 10]
    func(10, 20.1);  //调用2 - output [int a, double b 30]
    func(20.1, 30);  //调用3 - output [double a, int b 50.1]

    return 0;
}
    
```





##### 3.3.2 函数重载注意事项

- 引用作为函数重载条件
- 函数使用重载时，尽量避免使用默认参数，防止出现二义性报错



```cpp
#include <iostream>
using namespace std;

//函数重载的注意事项 - 引用作为重载的条件
void func(int& a)    //此时如果传入一个常量10，即int &a = 10，不合法。
{
    cout << "func(int& a) " << a << endl;
}
void func(const int& a)
{
    cout << "func(const int& a) " << a << endl;
}
int main()
{
    int a = 10;
    func(a);   //传入 变量 调用 无const 的
    func(10);  //传入 常量 调用 有const 的

    return 0;
}
```







### 4. 类和对象

C++ 面向对象的**三大特性**：==**封装**==、==**继承**==、==**多态**==

C++ 认为 ==**万事万物皆为对象**==，对象上有其属性和行为



- **实例化**是指通过一个类，创建一个对象的过程。

- 类的成员一般加 "m_" 前缀(member)，可以清晰的表示它是一个类的成员。

- 类中的[属性]和[行为]，统一称为 **成员**；

  [属性] 称为 **成员属性** 或 **成员变量**

  [行为] 称为 **成员函数** 或 **成员方法**

- 





**例如**：

​    人可以作为对象，属性有姓名、年龄、身高、体重......, 行为有走、跑、跳、吃饭......

​    车也可以作为对象，属性有轮胎、方向盘、车灯......，行为有载人、放音乐......

​    ==具有相同性质的对象，我们可以抽象为类==，人属于人类，车属于车类。



#### 4.1 封装

##### 4.4.1 封装的意义

封装是C++面向对象三大特性之一

封装的意义：

- 将**属性**和**行为**作为一个整体，表现生活中的事物
- 将属性和行为加以权限控制



###### **封装意义一**：

在设计类的时候，将==属性==和==行为==写在一起，表现事物。

**语法**： `class 类名{ 访问权限：属性 / 行为 };`



**示例1**：设计一个圆类，求圆的周长

**示例代码**：

``` c++
#include <iostream>
using namespace std;
const double PI = 3.14;
class Circle
{
    //访问权限： 公共权限
public:
    //属性： 半径
    int m_r;
    //行为： 获取圆的周长
    double claculateZC() {
        return 2 * PI * m_r;
    }
};
int main()
{
    //通过圆类 创建具体的圆（对象）
    Circle c1;  //实例化
    c1.m_r = 10;
    cout << "周长: " << c1.claculateZC() << endl;

    return 0;
}
```



**示例**：设计一个学生类，属性有姓名和学号，可以给姓名和学号赋值，可以显示学生的姓名和学号。

**示例代码**：

``` c++
#include <iostream>
using namespace std;

class Student
{
public:  //公共权限
    //属性
    string m_Name;  //姓名
    int m_Id;       //学号

public:  //公共权限
    //行为
    void showStudent() {  //显示姓名和学号
        cout << "姓名: " << m_Name << "学号: " << m_Id << endl;
    }
    void setName(string name) {  //set name
        m_Name = name;
    }
    void setID(int id) {     //set ID
        m_Id = id;
    }
};
int main()
{
    Student s1;  //实例化
    
    s1.setName("张三");
    s1.setID(123);

    s1.showStudent();

    return 0;
}
```



###### **封装意义二**：

类在设计时，可以把属性和行为放在不同的权限下，加以控制。

三种访问权限：

- public         公共权限

  类内成员类内**可以**访问，类外也**可以**访问。

- protected  保护权限

  类内成员类内**可以**访问，类外也**不可以**访问；儿子**可以**访问。

- private       私有权限

  类内成员类内**可以**访问，类外也**不可以**访问；儿子**不可以**访问。



##### 4.1.2 struct 和 class 区别

C++中，struct和class唯一**区别** 是 **默认的访问权限不同:

- struct  默认权限为公共
- class    默认权限为私有



##### 4.1.3 成员属性设置为私有

**优点**：

- 将所有成员属性设置为私有，可以自己**控制读写权限**。
- 对于写权限，可以**检测数据的有效性**（写入前 增加限定条件 进行判定）。



**示例**：

```c++
#include <iostream>
#include <string>
using namespace std;

class Person
{
public:
    //设置 m_Age   可读可写  并限制写入年龄在 [0~150] 之间
    void setAge(int age)
    {
        if (age < 0 || age > 150)
        {
            cout << "输入有误" << endl;
            return;
        }
        m_Age = age;
    }
    int getAge()
    {
        return m_Age;
    }

    //设置 m_Name 只读
    string getName()
    {
        m_Name = "我是名字";
        return m_Name;
    }

    //设置 m_Addr 只写
    void getAddr(string addr)
    {
        m_Addr = addr;
    }

private:
    int m_Age;      //可读可写 并限制写入年龄在 (0~100] 之间
    string m_Name;  //只读
    string m_Addr;  //只写
};


int main()
{
    Person p1;

    p1.setAge(999); //超出限制范围([0,150])，设置失败，输出错误提示

    p1.getAddr("我是地址");
    p1.setAge(9);
    cout << "name " << p1.getName() << endl;
    cout << "age  " << p1.getAge() << endl;

    return 0;
}
```



**例** 设计立方体

https://www.bilibili.com/video/BV1et411b73Z?p=104&spm_id_from=pageDriver





#### 4.2 对象的初始化和清理



#### 4.3 C++ 对象模型 和 this指针

#### 4.4 友元

#### 4.5 运算符重载

#### 4.6 继承

#### 4.7 多态



### 5. 文件操作





### 5. 文件操作































# 参考课程 - bilibili

https://www.bilibili.com/video/BV1et411b73Z?p=84