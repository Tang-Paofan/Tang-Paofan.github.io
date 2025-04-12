---
title: C++特性
date: 2025-04-12 23:02:31
tags: 
 - 芝士
categories: 
 - C++
 - C++特性
---

## C++11
### 语言核心
#### `auto`
1. 编译期类型推断
2. 在C++11之前，auto关键字用来指定存储期。在新标准中，它的功能变为**类型推断**。auto现在成了一个类型的占位符，通知编译器去根据初始化代码推断所声明变量的真实类型，**在编译器时进行类型推导**

需要注意的是，**C++11中的auto不能用来声明函数的返回值**。但如果函数有一个尾随的返回类型时，auto是可以出现在函数声明中返回值位置。这种情况下，auto并不是告诉编译器去推断返回类型，而是指引编译器去函数的末端寻找返回值类型
```cpp
template <typename T1, typename T2>
auto compose(T1 t1, T2 t2) -> decltype(t1 + t2)
{
    return t1+t2;
}
auto v = compose(2, 3.14); // v's type is double
```

#### decltype关键字
decltype实际上有点像auto的反函数，auto可以让你声明一个变量，而decltype则可以从一个变量或表达式中得到类型

```cpp
int x = 3;  
decltype(x) y = x;
```
#### `nullptr`
1. 避免空指针的二义性
2. 引入`nullptr`是为了解决`NULL`的诟病，在之前的c++中，`NULL`可以被定义为`int`0或者一个0值的指针
3. 以前都是用0来表示空指针的，但由于0可以被隐式类型转换为整型，这就会存在一些问题。关键字nullptr，用来指代空指针。nullptr和任何指针类型以及类成员指针类型的空值之间可以发生隐式类型转换，同样也可以隐式转换为bool型（取值为false）。但是不存在到整型的隐式类型转换。

```cpp
void fun(int);
void fun(void *);
 
fun(NULL);  //无法确定使用的是哪一个重载函数，需要视NULL的定义而定 
```

```cpp
void F(int a){  
    cout << a << endl;  
}  
 
void F(int *p){  
    assert(p != NULL);
    cout << p << endl;  
}

int main(){
    int *p = nullptr;  
    int *q = NULL;  
    bool equal = (p == q); // equal的值为true，说明p和q都是空指针  
    int a = nullptr; // 编译失败，nullptr不能转型为int  
    F(0); // 在C++98中编译失败，有二义性；在C++11中调用F（int）  
    F(nullptr);  
 
    return 0;  
}
```

#### Range-based for loops（基于范围的for循环）
1. 为了在遍历容器时支持“foreach”用法，C++11扩展了for语句的语法。用这个新的写法，可以遍历C类型的数组、初始化列表以及任何重载了非成员的begin()和end()函数的类型。
```cpp
std::map<std::string, std::vector<int>> map;
std::vector<int> v;
v.push_back(1);
v.push_back(2);
v.push_back(3);
map["one"] = v;
for (const auto& kvp : map)
{
    std::cout << kvp.first << std::endl;
    for (auto v : kvp.second)
    {
        std::cout << v << std::endl;
    }
}
int arr[] = {1, 2, 3, 4, 5};
for(int& e : arr)
{
    e = e * e;
}
```

#### constexpr（编译期常量类型）
[constexpr](constexpr.md)
1. 编译时期常量`constexpr`，及常量表达式`（const expression）`
2. 即在函数表达式前面加上`constexpr`关键字即可。有了常量表达式这样的声明，编译器可以在编译时期对GetConst()表达式进行值计算（evaluation），从而将其视为一个编译时期的常量。在C++11中，常量表达式实际上可以左右的实体不限于函数，还可以作用于数据声明，以及类的构造函数等
3. 常量函数表达式
    1. 函数体只有单一的return返回语句。  
    2. 函数必须有返回值（不能是void函数）。  
    3. 在使用前必须已有定义。  
    4. return返回语句表达式中不能使用非常量表达式的函数、全局数据，且必须是一个常量表达式。（C++14放宽了这些要求）
```cpp
constexpr int GetConst() { return 1; }
```

`const`缺点
以前的const修饰符并不能保证产生编译期常量，因此某些用法，比如用const常量声明数组长度不能保证可行。相比之下，constexpr类型具有更强的编译期检查，可以在编译阶段就发现常量能否在编译阶段确定

#### 模板别名
在C++11中，定义别名已经不再是typedef的专属能力，使用using同样也可以定义类型的别名，而且从语言能力上看，using丝毫不比typedef逊色

```cpp
#include <iostream>
#include <type_traits>

using namespace std;

using uint = unsigned int;
typedef unsigned int UINT;
using sint = int;

int main() {
    cout << is_same<uint, UINT>::value << endl; // 1
}
```
在上面的例子中，使用了C++11标准库的is_same模板类来帮助我们判断2个类型是否一致。在使用模板编程的时候，using的语法甚至比typedef更加灵活
```cpp
template <typename T> using MapString = std::map<T, char*>;
MapString<int> numberedString;
```

#### lamda
[Lambda](Lambda.md)
#### 成员函数控制：=delete和=default

#### 列表初始化语法
C++11中，集合（列表）的初始化已经成为C++语言的一个基本功能，这种初始化的方法被称为“初始化列表”（initializer list）

在引入C++11之前，只有数组能使用初始化列表，其他容器想要使用初始化列表，只能用以下方法
```cpp
int arr[3] = {1, 2, 3}  
vector<int> v(arr, arr + 3); 
```
在C++11中，我们可以使用以下语法来进行替换
```cpp
int arr[3]{1, 2, 3};  
vector<int> iv{1, 2, 3};  
map<int, string>{{1, "a"}, {2, "b"}};  
string str{"Hello World"}; 
```

#### Strongly-typed enums（强类型枚举）
在C++11中通过引入了一个称为强类型枚举的新类型，避免命名冲突。强类型枚举由关键字enum class标识。它不会将枚举常量暴露到外层作用域中，也不会隐式转换为整形，并且拥有用户指定的特定类型
```cpp
enum class Options {None, One, All};
Options o = Options::All;
```

#### 右值引用、移动构造、移动赋值和完美转发

#### noexcept修饰符与noexcept操作符
noexcept形如起名，表示其修饰的函数不会抛出异常（在C++11中如果noexcept修饰的函数抛出了异常，编译器可以选择直接调用std::terminate()函数来终止程序的运行）

#### 继承构造函数和委托构造函数
继承构造函数允许子类声明一个和父类一样的构造函数；委托构造函数允许不同参数类型的构造函数之间进行复用。这两个特性需要被使用的场景并不多见，以前程序员普遍习惯了通过父类构造函数显式调用和缺省参数来达到同样的效果，很难说新特性写法的可读性是否更好

### 标准库
#### unordered_set和unordered_map
1. C++11中出现了两种新的关联容器:unordered_set和unordered_map，其内部实现与set和map大有不同
2. set和map内部实现是基于RB-Tree（红黑树）
3. unordered_set和unordered_map内部实现是基于哈希表(hashtable)，unordered_set和unordered_map内部实现的公共接口大致相同

>unordered_set和unordered_map是基于哈希表，因此要了解unordered_set/unordered_map，就必须了解哈希表的机制。哈希表是根据关键码值而进行直接访问的数据结构，通过相应的哈希函数（也称散列函数）处理关键字得到相应的关键码值，关键码值对应着一个特定位置，用该位置来存取相应的信息，这样就能以较快的速度获取关键字的信息。面对哈希冲突时，unordered_set/unordered_map内部解决冲突采用的是——链地址法，当用冲突发生时把具有同一关键码的数据组成一个链表

```cpp
#include <iostream>
#include <string>
#include <unordered_set>
 
int main ()
{
    std::unordered_set<std::string> myset = {"red", "green", "blue"};

    std::string input;
    std::cout << "color?";
    getline(std::cin,input);
 
    std::unordered_set<std::string>::const_iterator got = myset.find(input);
 
    if (got == myset.end())
        std::cout << "not found in myset";
    else
        std::cout << *got << " is in myset";
 
    std::cout << std::endl;
    return 0;
}
```

#### std::tuple
类模板std::tuple是固定大小的异类值汇集，它是std::pair的推广

```cpp
#include <tuple>
#include <iostream>
#include <string>
#include <stdexcept>

std::tuple<double, char, std::string> get_student(int id)
{
    if (id == 0) return std::make_tuple(3.8, 'A', "Lisa Simpson");
    if (id == 1) return std::make_tuple(2.9, 'C', "Milhouse Van Houten");
    if (id == 2) return std::make_tuple(1.7, 'D', "Ralph Wiggum");
    throw std::invalid_argument("id");
}

int main()
{
    auto student0 = get_student(0);
    std::cout << "ID: 0, "
              << "GPA: " << std::get<0>(student0) << ", "
              << "grade: " << std::get<1>(student0) << ", "
              << "name: " << std::get<2>(student0) << '\n';
 
    double gpa1;
    char grade1;
    std::string name1;
    std::tie(gpa1, grade1, name1) = get_student(1);
    std::cout << "ID: 1, "
              << "GPA: " << gpa1 << ", "
              << "grade: " << grade1 << ", "
              << "name: " << name1 << '\n';
}
```

#### std::tie
std::tie通常结合std::pair或std::tuple来使用。以前，C++一个函数要返回多个值，或者将多个值一对一的赋值/比较都是很麻烦的事，std::tie可以很有效的简化代码。

#### std::array
std::array 是封装固定大小数组的容器。此容器是一个聚合类型，其语义等同于保有一个C风格数组T[N]作为其唯一非静态数据成员的结构体。不同于 C 风格数组，它不会自动退化成 T* 。
```cpp
#include <string>
#include <iterator>
#include <iostream>
#include <algorithm>
#include <array>
 
int main()
{
    // 用聚合初始化构造
    std::array<int, 3> a1{ {1, 2, 3} }; // C++11中要求双花括号（ C++14 中不要求）
    std::array<int, 3> a2 = {1, 2, 3};  // 在 = 后都不要求双花括号
    std::array<std::string, 2> a3 = { std::string("a"), "b" };
 
    // 支持容器操作
    std::sort(a1.begin(), a1.end());
    std::reverse_copy(a2.begin(), a2.end(), 
                      std::ostream_iterator<int>(std::cout, " "));
 
    std::cout << '\n';
 
    // 支持带范围 for 循环
    for(const auto& s: a3)
        std::cout << s << ' ';
}
```

std::array是个值得大力推广用来替代数组的特性。相比于数组，其at成员函数带有越界检查，可以杜绝一大类数组越界隐患（但是要注意真的越界后产生的std::out_of_range异常的处理方式）。另外，std::array不能自动转换成指针的特点也让它避免了被开发人员滥用，并杜绝了数组形参退化成指针导致的一大类常见问题。back、fill、swap等成员函数比起手写的数组操作也更加简洁

#### std::bind
很多算法接口要求提供一个函数对象，但程序员手头有可能只有一个参数并不匹配的现成函数，这时可以通过函数适配器或者lamda表达式来进行两个接口之间的适配。在过去，C++提供了std::bind1st，std::bind2st等函数来指定绑定哪一个参数。但显然这些工具用起来太麻烦。C++11废除了以前这些适配器，提供了一个统一的bind函数模板


```cpp
#include <random>
#include <iostream>
#include <memory>
#include <functional>
 
void f(int n1, int n2, int n3, const int& n4, int n5)
{
    std::cout << n1 << ' ' << n2 << ' ' << n3 << ' ' << n4 << ' ' << n5 << '\n';
}
 
int g(int n1)
{
    return n1;
}
 
struct Foo {
    void print_sum(int n1, int n2)
    {
        std::cout << n1 + n2 << '\n';
    }
    int data = 10;
};
 
int main()
{
    using namespace std::placeholders;  // 对于 _1, _2, _3...
 
    // 演示参数重排序和按引用传递
    int n = 7;
    // （ _1 与 _2 来自 std::placeholders，并表示将来会传递给 f1 的参数）
    auto f1 = std::bind(f, _2, _1, 42, std::cref(n), n);
    n = 10;
    f1(1, 2, 1001); // 1 为 _1 所绑定， 2 为 _2 所绑定，不使用 1001
                    // 进行到 f(2, 1, 42, n, 7) 的调用
 
    // 嵌套 bind 子表达式共享占位符
    auto f2 = std::bind(f, _3, std::bind(g, _3), _3, 4, 5);
    f2(10, 11, 12); // 进行到 f(12, g(12), 12, 4, 5); 的调用
 
    // 常见使用情况：以分布绑定 RNG
    std::default_random_engine e;
    std::uniform_int_distribution<> d(0, 10);
    std::function<int()> rnd = std::bind(d, e); // e 的一个副本存储于 rnd
    for(int n=0; n<10; ++n)
        std::cout << rnd() << ' ';
    std::cout << '\n';
 
    // 绑定指向成员函数指针
    Foo foo;
    auto f3 = std::bind(&Foo::print_sum, &foo, 95, _1);
    f3(5);
 
    // 绑定指向数据成员指针
    auto f4 = std::bind(&Foo::data, _1);
    std::cout << f4(foo) << '\n';
 
    // 智能指针亦能用于调用被引用对象的成员
    std::cout << f4(std::make_shared<Foo>(foo)) << '\n'
              << f4(std::make_unique<Foo>(foo)) << '\n';
}
```

#### Smart Pointers（智能指针）：unique_ptr、shared_ptr、weak_ptr
[shared_ptr](shared_ptr.md)
[unqiue_ptr](unqiue_ptr.md)
[weak_ptr](weak_ptr.md)

## C++14
### 语言核心

#### Lambda 返回值推断
C++11允许lambda函数根据return语句的表达式类型推断返回类型，C++14为一般的函数也提供了这个能力。C++14还拓展了原有的规则，使得函数体并不是{return expression;}形式的函数也可以使用返回类型推导。

为了启用返回类型推导，函数声明必须将auto作为返回类型，但没有C++11的后置返回类型说明符
```cpp
// C++11
template <typename T, typename U>
auto sum(T x, U y) -> decltype(x + y)  // 尾置返回类型
{
    return x + y;
}

// C++14
template <typename T, typename U>
auto sum(T x, U y)
{
    return x + y;
}
```

这样的函数中可以存在递归，但递归调用必须在函数定义中的至少一个return语句之后
```cpp
auto Correct(int i)
{
    if (i == 1)
        return i;// 返回类型被推断为int
    else
        return Correct(i-1)+i;// 正确，可以调用
}

auto Wrong(int i)
{
    if(i != 1)
        return Wrong(i-1)+i;  // 不能调用，之前没有return语句
    else
        return i;             // 返回类型被推断为int
}
```

#### 变量模板
1. 变量模板是C++14的一个新的语法特性。C++新标准引入变量模板的主要目的是为了简化定义（simplify definitions）以及对模板化常量（parameterized constant）的支持
```cpp
template<typename T>
T var;                  // not allowed in pre-C++14
var<int> = 5;           // == (int var = 5;)
var<double> = 3.14;
var<char> = '$';
var<string> = "这是一个字符串";
cout << var<int> << endl << var<double> << endl << var<char> << endl << var<string> << endl;
```

变量模板是个较为生僻的特性，在一般的场景下较少会用到

#### 二进制数字和数字分隔符
1. 使用单引号分割
```cpp
#include <iostream>

int main()
{
    intval = 0b11110000;
    std::cout << "Output mask: "
              << 0b1000'0001'1000'0000
              <<"\n";
    std::cout << "Proposed salary: $"
              << 300'000.00
              <<"\n";
    return0;
}
```

### 标准库
#### std::make_unique

C++11标准中有std::make_shared可以让智能指针shared_ptr的初始化和对象构造在一条语句中完成，现在std::make_unique也被加入了标准，让unique_ptr也可以用同样的方式初始化

## C++ 17
### 语言特性 
#### Structured Binding（结构化绑定）
C++11提供的std::tie虽然好用，但是只能用于已经被声明的变量。有时为了解析一个函数返回的元组，不得不先声明很多个临时变量。结构化绑定允许用auto []自动声明很多个对应类型的变量
```cpp
#include <cstdlib>
#include <iostream>
#include <set>
#include <string>
#include <iterator>

#include <tuple>

struct S {
    int n;
    std::string s;
    float d;
    bool operator<(const S& rhs) const
    {
        // compares n to rhs.n,
        // then s to rhs.s,
        // then d to rhs.d
        return std::tie(n, s, d) < std::tie(rhs.n, rhs.s, rhs.d);
    }
};

int main()
{
    std::set<S> mySet;
 
    // pre C++17:
    {
        S value{42, "Test", 3.14};
        std::set<S>::iterator iter;
        bool inserted;
 
        // unpacks the return val of insert into iter and inserted
        std::tie(iter, inserted) = mySet.insert(value);
        if (inserted)
            std::cout << "Value was inserted\n";
    }
    
    // with C++17:
    {
        S value{100, "abc", 100.0};
        const auto [iter, inserted] = mySet.insert(value);
        if (inserted)
            std::cout << "Value(" << iter->n << ", " << iter->s << ", ...) was inserted" << "\n";
    }
}
```

结构化绑定可以被用于std::pair、std::tuple、初始化列表（std::initializer_list）、数组、只包含简单成员的结构体、以及所有定义了get的自定义类型
```cpp
std::map<MyKey, MyValue> myMap;    
for (const auto& [k,v] : myMap) 
{  
    // k - key
    // v - value
} 
```

#### Init Statement for if/switch（if/switch语句中的初始化语句）
我们知道C++的for语句头部是可以声明变量的，该变量作用域只限定于for循环体内。现在if/switch语句也支持同样的用法了，这可以有效地避免临时变量之间重名，对于判断条件包含函数返回值时尤其有用

```cpp
#include <iostream>
#include <string>

int main()
{
    const std::string myString = "Hello World";

    // C++98/03 每次必须取不同的变量名
    auto it = myString.find("Hello");
    if (it != std::string::npos)
        std::cout << it << " Hello\n";
    
    auto it2 = myString.find("World");
    if (it2 != std::string::npos)
        std::cout << it2 << " World\n";

    // additional enclosing scope so 'it' doesn't 'leak'
    {
        auto it = myString.find("Hello");
        if (it != std::string::npos)
            std::cout << "Hello\n";
    }
    
    {
        auto it = myString.find("World");
        if (it != std::string::npos)
            std::cout << "World\n";
    }

    // C++17 with init if:
    if (const auto it = myString.find("Hello"); it != std::string::npos)
        std::cout << it << " Hello\n";

    if (const auto it = myString.find("World"); it != std::string::npos)
        std::cout << it << " World\n";
}
```

```cpp
#include <iostream>
#include <set>
#include <string>
#include <tuple>
#include <utility>
 
using namespace std;

int main()
{
    set<pair<string, int>> mySet;
    pair<string, int> itemsToAdd[3] { {"hello", 1}, { "world", 2 }, { "world", 2 } };
    for (auto &p : itemsToAdd)
    {        
        if (const auto [iter, inserted] = mySet.insert(p); inserted)
            cout << "Value(" << iter->first << ", " << iter->second << ") was inserted" << "\n"; 
        else
            cout << "Value(" << iter->first << ", ...) not inserted!" << "\n"; 
    }
}
```

#### Nested namespace definition（嵌入名字空间定义）
```cpp
namespace A::B::C {
    // ...
}
```

#### constexpr if-statements（静态条件语句）
这个特性可以让编译器在编译期就决定哪个分支被运行，去掉运行时的判断开销。比如下面这个get_value函数模板，可以在编译期确定对于指针返回其指向的值，对于非指针直接返回其本身
```cpp
#include <iostream>
#include <memory>

template <typename T>
auto get_value(T t) {
    if constexpr (std::is_pointer_v<T>)
        return *t;
    else
        return t;
}

int main()
{
     auto pi = std::make_unique<int>(9);
     int i = 9;
     
     std::cout << get_value(pi.get()) << "\n";
     std::cout << get_value(i) << "\n";
}
```

### 标准库
#### std::string_view
在很多编程语言中，字符串都是不可修改（immutable）的，这样就可以放心的让同一个字符串内容被多处引用共享。但是C++的std::string却是自带独立内存空间的可修改容器，这使得在某些场景下性能会劣化。比如，想要函数返回一个字符串，很难避免return时它被复制一次。当然，C++11以后我们可以用右值引用等方法来做一定程度的优化，但还是很难解决多处共享同一个字符串等场景的问题。

std::string_view为此而诞生，它不会对字符串内容进行任何修改，且没有内存管理的功能，仅仅只是将C的原生字符串做了一下包装。并且带有查找、越界检查、去除前缀/后缀、运算符重载等方便的功能。
```cpp
#include <iostream>
#include <algorithm>
#include <string_view>

int main()
{
    std::string str = "   trim me";
    std::string_view v = str;
    v.remove_prefix(std::min(v.find_first_not_of(" "), v.size()));
    std::cout << "String: '" << str << "'\n"
              << "View  : '" << v << "'\n";
}
```

#### std::optional
见[optional](optional.md)
std::optional模板。函数返回std::optional时，表示既有可能含值也有可能不含值。调用者可以用bool表达式来判断其是否含值，也可以用value_or函数来提供一个当不含值时的默认值。内部对象的生命周期由std::optional来管理，当std::optional对象被释放时其内部管理的对象也自动被释放
```cpp
#include <string>
#include <functional>
#include <iostream>
#include <optional>
 
// optional 可用作可能失败的工厂的返回类型
std::optional<std::string> create(bool b) {
    if (b)
        return "Godzilla";
    else
        return {};
}
 
// 能用 std::nullopt 创建任何（空的） std::optional
auto create2(bool b) {
    return b ? std::optional<std::string>{"Godzilla"} : std::nullopt;
}
 
// std::reference_wrapper 可用于返回引用
auto create_ref(bool b) {
    static std::string value = "Godzilla";
    return b ? std::optional<std::reference_wrapper<std::string>>{value}
             : std::nullopt;
}
 
int main()
{
    std::cout << "create(false) returned "
              << create(false).value_or("empty") << '\n';
 
    // 返回 optional 的工厂函数可用作 while 和 if 的条件
    if (auto str = create2(true)) {
        std::cout << "create2(true) returned " << *str << '\n';
    }
 
    if (auto str = create_ref(true)) {
        // 用 get() 访问 reference_wrapper 的值
        std::cout << "create_ref(true) returned " << str->get() << '\n';
        str->get() = "Mothra";
        std::cout << "modifying it changed it to " << str->get() << '\n';
    }
}
```

std::optional可以很有效的简化代码，并防止安全问题，在写查找功能的函数时尤其有用。但是使用std::optional时也要注意在构造时如何减少拷贝开销，一般可以使用std::make_optional或者常量表达式来完成构造。另一方面，由于std::optional析构时会自动把内部对象也给析构掉，使用者应注意不要访问野指针

#### std::variant

## 参考文章
[现代C++语言（C++11/14/17）特性总结和使用建议_现代c++详解(98 11 14 17)-CSDN博客](https://blog.csdn.net/qq_36631379/article/details/110728643)
[C++面试总结（五）C++ 11/14新特性_c++ 11 14-CSDN博客](https://blog.csdn.net/qq_36086861/article/details/84938705)
