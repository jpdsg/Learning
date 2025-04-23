# 静态成员函数对非静态成员的间接访问操作

在C++中，静态成员属于类，为所有属于该类的对象所共享，而不与特定对象绑定，因此没有隐含的this指针，无法直接对非静态数据成员进行访问操作。不过，可以通过以下几种方式间接访问操作。

### 1.通过对象引用传参

把对象作为参数传递给静态成员函数，通过该对象来访问和操作非静态成员。

**例：**

```c++
#include <iostream>
using namespace std;
class MyClass 
{
public:
    int nonStaticMember;
    MyClass(int value) : nonStaticMember(value) {}
    static void modifyNonStaticMember(MyClass& obj, int newValue)
    {
        obj.nonStaticMember = newValue;
    }
};

int main() 
{
    MyClass obj(10);
    cout << "Before modification: " << obj.nonStaticMember << endl;
    MyClass::modifyNonStaticMember(obj, 20);
    cout << "After modification: " << obj.nonStaticMember << endl;
    return 0;
}
```

在上述代码中，`modifyNonStaticMember` 是静态成员函数，它接收一个 `MyClass` 对象的引用作为参数，然后通过这个对象来修改非静态成员 `nonStaticMember`。

**运行结果：**

```c++
Before modification: 10
After modification: 20
```

### 2.通过全局对象访问

**例：**

```c++
#include <iostream>
using namespace std;
class MyClass 
{
public:
    int nonStaticMember;
    MyClass(int value) : nonStaticMember(value) {}
    static void modifyGlobalObject();  
};

MyClass globalObj(20);  

void MyClass::modifyGlobalObject()
{
        globalObj.nonStaticMember = 30;
}

int main()
{
    cout << "Before modification: " << globalObj.nonStaticMember << endl;
    MyClass::modifyGlobalObject();
    cout << "After modification: " << globalObj.nonStaticMember << endl;
    return 0;
}
```

 **运行结果：**

```c++
Before modification: 20
After modification: 30
```

**注意：**c++还是比较讲究的...从上至下声明、定义。这里静态成员函数必须在类外实现，且在全局对象定义之后，否则会报错`未声明的标识符`，也不能使用前向声明，因为前向声明类不能定义对象。

### 3.静态指针法

**注意！！**仅适用于单例类，即该类在整个程序的生命周期内只有一个实例（此处可理解为一个对象。对象都是实例，但是只有在类中的实例才是对象）。

**例：**

```c++
#include <iostream>
using namespace std;
class Person 
{
private:
    // 非静态成员变量，记录人的年龄
    int age;
    // 静态指针，指向某个 Person 对象
    static Person* m_gPerson;
    
public:
    // 构造函数，将静态指针 m_gPerson 指向当前对象
    Person(int initAge) : age(initAge) 
    {
        m_gPerson = this;
    }

    // 静态成员函数，用于增加 m_gPerson 所指向对象的年龄
    static void increaseAge() 
    {
        if (m_gPerson) 
        {
            m_gPerson->age++;
        }
    }

    // 成员函数，用于输出当前对象的年龄
    void showAge() 
    {
        cout << "The person's age is: " << age << endl;
    }

};

// 初始化静态指针
Person* Person::m_gPerson = nullptr;

int main()
{
    // 创建一个 Person 对象，初始年龄为 20
    Person person(20);

    cout << "Before increasing age: ";
    person.showAge();

    // 调用静态成员函数增加年龄
    Person::increaseAge();

    cout << "After increasing age: ";
    person.showAge();

    return 0;
}    
```

**运行结果：**

```c++
Before increasing age: The person's age is: 20
After increasing age: The person's age is: 21
```

在实例化的同时，静态指针已指向当前构造的对象。为可视化理解，将main函数改为：

```c++
int main()
{  
    Person p2(20);                         //静态指针 m_gPerson 指向当前对象p2        
	Person p1(30);                         //静态指针 m_gPerson 指向当前对象p1
    cout << "Before increasing age: ";
    p2.showAge();
	cout << "Before increasing age: ";
    p1.showAge();
    p2.increaseAge();                      //即p1.age++;
	p1.increaseAge ();                     //即p1.age++;
    std::cout << "After increasing age: ";
    p2.showAge();
	p1.showAge ();
    return 0;
}   

```

**运行结果：**

```c++
Before increasing age: The person's age is: 20
Before increasing age: The person's age is: 30
After increasing age: The person's age is: 20
The person's age is: 32
```

PS:

关于单例模式，此处仅略述。

> 单例模式（Singleton Pattern）是一种常见的设计模式，用于确保一个类只有一个实例，并提供全局访问点。
>
> 在单例模式中，类的构造函数被私有化，确保外部无法直接创建对象实例。同时，类内部定义一个静态成员变量用于保存唯一实例，并提供一个公共的静态方法用于获取该实例。
>

以下是一个简单的单例模式的示例代码：

```c++
#include <iostream>
using namespace std;
class Singleton 
{
private:
    // 静态成员变量，用于存储单例实例
    static Singleton* instance;

    // 私有化构造函数，防止外部实例化
    Singleton() {}

    // 私有化拷贝构造函数，防止拷贝
	Singleton(const Singleton&) {};

    // 私有化赋值运算符，防止赋值
	Singleton& operator=(const Singleton&) {};

public:
    // 静态成员函数，用于获取单例实例
    static Singleton* getInstance()
    {
        if (instance == nullptr) 
        {
            instance = new Singleton();
        }
        return instance;
    }

    // 示例成员函数
    void doSomething()
    {
        cout << "Singleton is doing something." << endl;
    }
};

// 初始化静态成员变量
Singleton* Singleton::instance = nullptr;

int main() 
{
    // 获取单例实例
    Singleton* singleton1 = Singleton::getInstance();
    Singleton* singleton2 = Singleton::getInstance();

    // 验证两个实例是否相同
    if (singleton1 == singleton2) 
    {
        cout << "Both instances are the same." << endl;
    }

    // 调用示例成员函数
    singleton1->doSomething();

    return 0;
}    
```

**运行结果：**

```c++
Both instances are the same.
Singleton is doing something.
```



### 4.通过对象指针传参

把对象的指针传递给静态成员函数，然后利用指针访问和操作非静态成员。

**例：**

```c++
#include <iostream>
using namespace std;
class MyClass
{
public:
    int nonStaticMember;
    MyClass(int value) : nonStaticMember(value) {}
    static void modifyNonStaticMember(MyClass* obj, int newValue) 
    {
        obj->nonStaticMember = newValue;
    }
};

int main() 
{
    MyClass obj(10);
    cout << "Before modification: " << obj.nonStaticMember << endl;
    MyClass::modifyNonStaticMember(&obj, 20);
    cout << "After modification: " << obj.nonStaticMember << endl;
    return 0;
}
```

**运行结果：**

```c++
Before modification: 10
After modification: 20
```



或使用void指针传参，在函数内部强制转换为类类型（任何类型指针都可以直接赋值给void类型指针，后者需通过强制转换赋值给其他类型的指针）。

**例：**

```c++
#include <iostream>
using namespace std;
class A 
{
public:
    // 静态成员函数，接收一个 void 指针并将其转换为 A 类指针，然后对成员变量 m_a 加 1
    static void test(void *pData)
    {
    A *a = (A*)(pData);
        if (a)
        {
            a->m_a += 1;
        }
    }
    // 成员函数，用于输出成员变量 m_a 的值
    void print()
    {
        cout << "m_a: " << m_a << endl;
    }

private:
    static int m_staticA;
    // 非静态成员变量，记录数据
    //C++11允许在声明非静态成员变量的同时用'='或'{}'初始化，而VS2010支持C++11之前的标准（C++98/C++03），只有静态常量整型数据成员才可以在类中初始化
    int m_a = 0;
};

// 初始化静态成员变量
int A::m_staticA = 0;

int main()
{
    // 创建一个 A 类对象
    A a;
    cout << "Before calling test: ";
    a.print();

    // 调用静态成员函数 test，传入对象 a 的地址
    A::test(&a);

    cout << "After calling test: ";
    a.print();

    return 0;
}    
```

**运行结果：**

```c++
Before calling test: m_a: 0
After calling test: m_a: 1
```



### 应用

将类的静态成员函数作为回调函数时，遇到静态成员函数无法访问类的非静态成员变量的问题。（关于回调函数本文不介绍）







