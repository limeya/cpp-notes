# Reference和Pointer


> 💡 本篇的内容来自C++ Primer中的2.3节

在C++中除了基本数据类型，还包括复杂数据类型，主要有2种：

- reference 引用
- pointer 指针

> A compound type is a type that is deﬁned in terms of another type.
> 

# Reference

> Technically speaking, when we use the term reference, we mean “lvalue reference.”
> 

## 含义

- A reference deﬁnes an **alternative name** for an **object**;
- A reference type “refers to” another type;

## 使用

### 基本使用

```cpp
int ival = 1024;
int &refVal = ival; // right, refVal refers to(is another name for) ival
int &refVal2;       // error, 引用必须被初始化
```

### 定义过程

- 引用只能是object的别名；
    
    ```cpp
    int &refVal4 = 10; // 10 不是变量
    ```
    
- 引用的类型与其所指向的对象的类型要匹配；
    
    ```cpp
    double dval = 3.14;
    int &refVal5 = dval; // 错误，类型不匹配
    ```
    

## 内部机制

对于基本类型的变量而言，当初始化一个变量时，用于初始化的对象的值被拷贝到创建的对象上。但是当创建reference时，不拷贝该值，而是将reference与该对象绑定。

这一点对于需要性能的场景很重要，不希望拷贝数据，只希望引用同样的数据，这样避免了拷贝操作。

## 注意事项

- 所有对象引用的声明都必须要初始化；
- 引用一旦初始化，没有任何方式能够解绑其引用另外一个对象，即整个生命周期和初始化对象“死磕”；
- 当引用与初始化对象绑定后，所有针对该引用操作引起的变化都会反映到初始化对象上；
    
    ```cpp
    refVal2 = 2; // 此时，ival也是2；
    ```
    
- Reference is not an object, and it is just another name for an already existing object.
    
    因此我们不会定义reference的reference，因为reference只能定义object的别名；
    
- 一个技巧：由于reference使用的&后续的获取变量的地址时也会使用，因此会让人困惑，
    
    因此一种读法是，如下：
    
    ```cpp
    int a = 1;
    int &val = a; //val refers to a int variable a. 从右向左读，&换成refer to 
    ```
    

# Pointer

## 含义说明

> A pointer is a compound type that “points to” another type.
> 

与reference对比：

- a pointer is an object in its own right.
    - reference is not an object;
- Pointers can be assigned and copied;
- a single pointer can point to several different objects over its lifetime;
- a pointer need not be initialized at the time it is deﬁned;

## 声明和使用方式

- 使用`*`
    
    ```cpp
    // dp1是指针，dp是浮点数
    double dp, *dp1;
    
    ```
    
- the types of the pointer and the object to which it points must match;
    
    ```cpp
    double dval;
    double * pd = &dval; // ok: initializer is the address of a double 
    double * pd2 = pd; // ok: initializer is a pointer to double 
    int * pi = pd; // error: types of pi and pd differ 
    pi = &dval; // error: assigning the address of a double to a pointer to int
    ```
    
- use the dereference operator (the * operator) to access that object;
    
    ```cpp
    int ival = 42;
    int *p = &ival;
    cout << * p;
    ```
    
- 同时定义多个变量时的误解
    
    ```cpp
    int *i, j; // i为指向int的指针，j为int变量，不是指针； 
    ```
    
    这种写在一起时会引发误解，两种方法可以避免：
    
    1. 写成 int  *i, 而不是 int* i;
    2. 写在两个声明语句中，就不会误解；

## Pointer指向对象地址

- A pointer holds the address of another object;
- Because references are not objects, they don’t have addresses. Hence, **we may not deﬁne a pointer to a reference**.
- Because a pointer is an object, we can deﬁne a reference to a pointer.
    
    ```cpp
    int i = 10;
    int *p;
    int *&r = p; // r is a reference to the pointer p
    r = &i; // 更改r所引用对象的值
    ```
    
- we can store the address of a pointer in another pointer as pointers are also objects.
    
    ```cpp
    int val = 10;
    int *p = &val; // p is a pointer to val;
    int **pp = &p; // pp is a pointer to pointer to p.
    
    cout << "val is " << val << " by val" << endl;
    cout << "val is " << *p << " by *p" << endl;
    cout << "val is " << **pp << " by **pp" << endl;
    ```
    
    对于存在多种符号，如&， *等涉及reference或者pointer的声明时，遵守以下原则就不会出错；
    
    - The easiest way to understand the type of r is to read the deﬁnition right to left.
    - The symbol closest to the name of the variable (in this case the & in &r) is the one that has the most immediate effect on the variable’s type.
    
    将* 称为pointer to, & 称为reference to.
    

## Pointer Value

指针在实践中很容易出错，但是pointer的value只能是下面4种之一：

1. point to an object; （正常，我们希望的状态）
2. point to the location just immediately past the end of an object;
    
    这种情况如何理解呢？比如在一个数组中，最后一个元素之后的位置，在循环和迭代器中，我们会有指向最后元素之后的元素的指针，可以阅读[这篇文章](https://stackoverflow.com/questions/21850108/what-is-pointer-past-the-end-of-an-object-means)；
    
3. null pointer: not bound to any object; （注重注意的情况）
4. invalid: 除了上述三者之外的所有情况；（极力避免）
    - It is an error to copy or otherwise try to access the value of an invalid pointer.
    - The result of accessing an invalid pointer is undeﬁned.

## Null Pointers

### 具体含义

> A null pointer does not point to any object.
> 

### 获取null pointers

```cpp
// method 1
int *p1 = nullptr;
// method 2
int *p2 = 0;
// method 3
int *p3 = NULL; // 必须#include <cstdlib>
```

在这三种方法中：

- 法1最被推荐使用，新的标准，可以转换为任意其他的指针类型；
- 法2是之前的方法；
    
    一种“骚操作”：
    
    ```cpp
    int *p;
    int zero = 0;
    pi = zero;  // 错误，不允许将int赋值给pointer
    ```
    
- 法3等价于法2，继承自C语言，NULL是一个preprocessor variable，在compiler编译之前，会将其替换为0；

<aside>
💡 强烈建议：在pointer声明时，最好初始化，以免出现未定义的pointer；如果不定义，最好使用nullptr赋值。

</aside>

## Pointer的其他操作

### 条件语句中

> if the pointer is 0, then the condition is false, any nonzero pointer evaluates as true.
> 

只要pointer为null pointer, 其条件判定即为0；

```cpp
int *p1 = 0;
int *p2 = nullptr;
if(p1){
    cout << "p is null pointer" << endl;
}
else{
    cout << "p is not null poiner" << endl;
}
// 输出：p is not null poiner
```

### 比较操作

Given two valid pointers of the same type, we can compare them using the equality (==) or inequality (!=) operators.

- Two pointers are equal if they hold the same address and unequal other-wise.
- Two pointers hold the same address (i.e., are equal)
    - if they are both null,
    - if they address the same object,
    - or if they are both pointers one past the same object.
- Using an invalid pointer as a condition or in a comparison is undeﬁned. 这种情况指的是，pointer values中的第四种情况。

## void * Pointers

- The type void * is a special pointer type that can hold the address of any object.
- a void * pointer holds an address, but the type of the object at that address is unknown:

<aside>
💡 之前我一直以为这是指向一个void地址，这个起名太奇怪了。

</aside>

虽然这种指针类型可以指向任意类型的指针，但是其只支持有限的操作。

- We can compare it to another pointer,
- we can pass it to or return it from a function,
- and we can assign it to another void * pointer.

但是不可以：

- use a void * to operate on the object it addresses
    
    ```cpp
    int i = 10;
    int *p = &i;
    void *vp = p;
    cout << "p points to " << *p << endl;
    cout << "vp points to " << *vp << endl; // 错误
    ```

# 对比Reference和Pointer

## 相同点

- Both pointers and references give indirect access to other objects.

> 💡 在Reference和Pointer中，&和*出现的频率很高，但是在不同context中代表的含义不同，这一点尤其需要注意。

```cpp
int i = 42;
int &r = i;   // &代表r refers to i
int *p = &i;  // *代表p points to i, &代表取i的地址
int &r2 = *p; // &代表r2 refers to the object which p points to.
```

## 不同点

|  | Reference | Pointer |
| --- | --- | --- |
| 内存地址 | 不是独立对象，也就没有独立的内存地址，而是附属在特定的对象上。 | 作为独立的对象，像基本类型的对象一样存在内存地址。 |
| 可变性 | 作为引用本身，无法改变成针对另一个对象的引用；但是可以改变引用对象本身的值。 | 可以改变指向对象的值，也可以改变指向的对象。 |
| 初始化 | 声明时，必须初始化。 | 声明时，不必初始化，但是如果不初始化，则会有一个undefined的值。 |