第2章 变量和基本类型
处理类型（Dealing with Types）
类型别名（Type Aliases）
类型别名是某种类型的同义词，传统方法是使用关键字typedef定义类型别名。

typedef double wages;   // wages is a synonym for double
typedef wages base, *p; // base is a synonym for double, p for double*
C++11使用关键字using进行别名声明（alias declaration），作用是把等号左侧的名字规定成等号右侧类型的别名。

using SI = Sales_item; // SI is a synonym for Sales_item
auto类型说明符（The auto Type Specifier）
C++11新增auto类型说明符，能让编译器自动分析表达式所属的类型。auto定义的变量必须有初始值。

// the type of item is deduced from the type of the result of adding val1 and val2
auto item = val1 + val2;    // item initialized to the result of val1 + val2
编译器推断出来的auto类型有时和初始值的类型并不完全一样。

当引用被用作初始值时，编译器以引用对象的类型作为auto的类型。

int i = 0, &r = i;
auto a = r;     // a is an int (r is an alias for i, which has type int)
auto一般会忽略顶层const。

const int ci = i, &cr = ci;
auto b = ci;    // b is an int (top-level const in ci is dropped)
auto c = cr;    // c is an int (cr is an alias for ci whose const is top-level)
auto d = &i;    // d is an int*(& of an int object is int*)
auto e = &ci;   // e is const int*(& of a const object is low-level const)
如果希望推断出的auto类型是一个顶层const，需要显式指定const auto。

const auto f = ci;  // deduced type of ci is int; f has type const int
设置类型为auto的引用时，原来的初始化规则仍然适用，初始值中的顶层常量属性仍然保留。

auto &g = ci;   // g is a const int& that is bound to ci
auto &h = 42;   // error: we can't bind a plain reference to a literal
const auto &j = 42;     // ok: we can bind a const reference to a literal
decltype类型指示符（The decltype Type Specifier）
C++11新增decltype类型指示符，作用是选择并返回操作数的数据类型，此过程中编译器不实际计算表达式的值。

decltype(f()) sum = x;  // sum has whatever type f returns
decltype处理顶层const和引用的方式与auto有些不同，如果decltype使用的表达式是一个变量，则decltype返回该变量的类型（包括顶层const和引用）。

const int ci = 0, &cj = ci;
decltype(ci) x = 0;     // x has type const int
decltype(cj) y = x;     // y has type const int& and is bound to x
decltype(cj) z;     // error: z is a reference and must be initialized
如果decltype使用的表达式不是一个变量，则decltype返回表达式结果对应的类型。如果表达式的内容是解引用操作，则decltype将得到引用类型。如果decltype使用的是一个不加括号的变量，则得到的结果就是该变量的类型；如果给变量加上了一层或多层括号，则decltype会得到引用类型，因为变量是一种可以作为赋值语句左值的特殊表达式。

decltype((var))的结果永远是引用，而decltype(var)的结果只有当var本身是一个引用时才会是引用。

自定义数据结构（Defining Our Own Data Structures）
C++11规定可以为类的数据成员（data member）提供一个类内初始值（in-class initializer）。创建对象时，类内初始值将用于初始化数据成员，没有初始值的成员将被默认初始化。

类内初始值不能使用圆括号。

类定义的最后应该加上分号。

头文件（header file）通常包含那些只能被定义一次的实体，如类、const和constexpr变量。

头文件一旦改变，相关的源文件必须重新编译以获取更新之后的声明。

头文件保护符（header guard）依赖于预处理变量（preprocessor variable）。预处理变量有两种状态：已定义和未定义。#define指令把一个名字设定为预处理变量。#ifdef指令当且仅当变量已定义时为真，#ifndef指令当且仅当变量未定义时为真。一旦检查结果为真，则执行后续操作直至遇到#endif指令为止。

#ifndef SALES_DATA_H
#define SALES_DATA_H
#include <string>
struct Sales_data
{
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};
#endif
在高级版本的IDE环境中，可以直接使用#pragma once命令来防止头文件的重复包含。

预处理变量无视C++语言中关于作用域的规则。

整个程序中的预处理变量，包括头文件保护符必须唯一。预处理变量的名字一般均为大写。

头文件即使目前还没有被包含在任何其他头文件中，也应该设置保护符。
