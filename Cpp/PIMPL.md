# Private Implementation (PIMPL)

私有实现。一种用来解耦的常用模式。在库的设计中，常常需要引用许多第三方库，很多时候，我们不希望将这些第三方库暴露给用户，但这些第三方库又需要被我们的类引用，PIMPL提供了一种解耦的方案。

如果存在下面的第三方类X：

```cpp
//x.h 第三方库
class X{
public:
  Fun();
private:
  int i;
};
```

```cpp
//x.cpp ... 省略
```

开发者在自定义库A中，使用了第三方类X：

```cpp
//a.h 自定义库
#include <x.h> //强耦合x
class A{
public:
  Fun();
private:
  X x;
};
```

```cpp
//a.cpp ... 省略
```

在上面代码中，一旦用户使用A类，必然引用第三方类X，用户需要再次编译X的头文件。

在PIMPL模式中，我们不在A类中使用类X。建立A类的一个“具体实现类AImpl”来引用X，而在暴露给用户的A类中，只使用AImpl的指针。

```cpp
//a_impl.h 自定义库的具体实现类（不暴露给用户）
#include <x.h> //强耦合x
class AImpl{
public:
  Fun();
private:
  X x;
};
```

```cpp
//a_impl.cpp ... 省略
```

```cpp
//a.h 自定义库（暴露给用户）
//#include <x.h>  不再引用 <x.h>
class AImpl; //不引用 <a_impl.h> 只声明这个类
class A{
public:
  Fun();
private:
  AImpl* pimpl; //指向具体实现类的指针,弱耦合x
};
```

```cpp
//a.cpp
#include <a_impl.h> // 在cpp文件中，引用<a_impl.h>

A::A():
  pimpl(new AImpl()){}

A::~A(){
  delete pimpl;
}
```

这样，相当于用指针pimpl给A穿上了一件衣服，用于隐藏内部的AImpl具体实现，因为具体实现中包含了对X的引用。

总结一下，PIMPL模式一共包含下面的文件：

* A.h 外壳头文件，只需要声明AImpl类，不需要定义，并在该头文件中使用短指针。
* A.cpp 外壳的实现 需要引用AImpl.h
* AImpl.h 包含A的具体实现，包含所有我们想要隐藏的第三方类。
* AImpl.cpp 函数功能的具体实现。



下列情况，需要考虑使用PIMPL模式:

* 自定义类A需要第三方类B作为成员变量，但开发者不希望将第三方头文件暴露给用户。
* 若用户引用第三方头文件，可能产生冲突。
