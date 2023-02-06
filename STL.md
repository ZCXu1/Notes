# STL容器

## 分类

![img](https://oi-wiki.org/lang/csl/images/container1.png)

## 序列式容器

- **向量**(`vector`) 后端可高效增加元素的顺序表。
- **数组**(`array`)**C++11**，定长的顺序表，C 风格数组的简单包装。
- **双端队列**(`deque`) 双端都可高效增加元素的顺序表。
- **列表**(`list`) 可以沿双向遍历的链表。
- **单向列表**(`forward_list`) 只能沿一个方向遍历的链表。

## 关联式容器

- **集合**(`set`) 用以有序地存储 **互异** 元素的容器。其实现是由节点组成的红黑树，每个节点都包含着一个元素，节点之间以某种比较元素大小的谓词进行排列。
- **多重集合**(`multiset`) 用以有序地存储元素的容器。允许存在相等的元素。
- **映射**(`map`) 由 {键，值} 对组成的集合，以某种比较键大小关系的谓词进行排列。
- **多重映射**(`multimap`) 由 {键，值} 对组成的多重集合，亦即允许键有相等情况的映射。

## 无序（关联式）容器

- **无序（多重）集合**(`unordered_set`/`unordered_multiset`)**C++11**，与 `set`/`multiset` 的区别在与元素无序，只关心”元素是否存在“，使用哈希实现。
- **无序（多重）映射**(`unordered_map`/`unordered_multimap`)**C++11**，与 `map`/`multimap` 的区别在与键 (key) 无序，只关心 "键与值的对应关系"，使用哈希实现。

## 容器适配器

容器适配器其实并不是容器。它们不具有容器的某些特点（如：有迭代器、有 `clear()` 函数……）。

> ”适配器是使一种事物的行为类似于另外一种事物行为的一种机制”，适配器对容器进行包装，使其表现出另外一种行为。

- **栈** `(stack`) 后进先出 (LIFO) 的容器。
- **队列**(`queue`) 先进先出 (FIFO) 的容器。
- **优先队列**(`priority_queue`) 元素的次序是由作用于所存储的值对上的某种谓词决定的的一种队列。

## 容器声明

都是 `containerName<typeName,...> name` 的形式，但模板参数（`<>` 内的参数）的个数、形式会根据具体容器而变。

本质原因：STL 就是“标准模板库”，所以容器都是模板类。

## 迭代器

在 STL 中，迭代器（Iterator）用来访问和检查 STL 容器中元素的对象，它的行为模式和指针类似，但是它封装了一些有效性检查，并且提供了统一的访问格式。类似的概念在其他很多高级语言中都存在，如 Python 的 `__iter__` 函数，C# 的 `IEnumerator`。

### 基础使用

迭代器听起来比较晦涩，其实迭代器本身可以看作一个数据指针。迭代器主要支持两个运算符：自增 (`++`) 和解引用（单目 `*` 运算符），其中自增用来移动迭代器，解引用可以获取或修改它指向的元素。

指向某个 [STL 容器](https://oi-wiki.org/lang/csl/container/) `container` 中元素的迭代器的类型一般为 `container::iterator`。

迭代器可以用来遍历容器，例如，下面两个 for 循环的效果是一样的：

```c++
vector<int> data(10);

for (int i = 0; i < data.size(); i++)
  cout << data[i] << endl;  // 使用下标访问元素

for (vector<int>::iterator iter = data.begin(); iter != data.end(); iter++)
  cout << *iter << endl;  // 使用迭代器访问元素
// 在C++11后可以使用 auto iter = data.begin() 来简化上述代码
```

### 分类

在 STL 的定义中，迭代器根据其支持的操作依次分为以下几类：

- InputIterator（输入迭代器）：只要求支持拷贝、自增和解引访问。
- OutputIterator（输出迭代器）：只要求支持拷贝、自增和解引赋值。
- ForwardIterator（向前迭代器）：同时满足 InputIterator 和 OutputIterator 的要求。
- BidirectionalIterator（双向迭代器）：在 ForwardIterator 的基础上支持自减（即反向访问）。
- RandomAccessIterator（随机访问迭代器）：在 BidirectionalIterator 的基础上支持加减运算和比较运算（即随机访问）。

其实这个“分类”并不互斥——一个“类别”是可以包含另一个“类别”的。例如，在要求使用向前迭代器的地方，同样可以使用双向迭代器。

不同的 [STL 容器](https://oi-wiki.org/lang/csl/container/) 支持的迭代器类型不同，在使用时需要留意。

指针满足随机访问迭代器的所有要求，可以当作随机访问迭代器使用。

### 相关函数

很多 [STL 函数](https://oi-wiki.org/lang/csl/algorithm/) 都使用迭代器作为参数。

可以使用 `std::advance(it, n)` 获取迭代器 `it` 的增加 `n` 步的迭代器；若 `n` 为负数，则尝试获取迭代器的前驱，此时若迭代器不满足双向迭代器，行为未定义。

在 C++11 以后可以使用 `std::next(it)` 获取向前迭代器 `it` 的后继，`std::next(it, n)` 获取向前迭代器 `it` 的第 `n` 个后继。

在 C++11 以后可以使用 `std::prev(it)` 获取双向迭代器 `it` 的前驱，`std::prev(it, n)` 获取双向迭代器 `it` 的第 `n` 个前驱。

[STL 容器](https://oi-wiki.org/lang/csl/container/) 一般支持从一端或两端开始的访问，以及对 [const 修饰符](https://oi-wiki.org/lang/const/) 的支持。例如容器的 `begin()` 函数可以获得指向容器第一个元素的迭代器，`rbegin()` 函数可以获得指向容器最后一个元素的反向迭代器，`cbegin()` 函数可以获得指向容器第一个元素的 const 迭代器，`end()` 函数可以获得指向容器尾端（“尾端”并不是最后一个元素，可以看作是最后一个元素的后继；“尾端”的前驱是容器里的最后一个元素，其本身不指向任何一个元素）的迭代器。

## 共有函数

`=`：有赋值运算符以及复制构造函数。

`begin()`：返回指向开头元素的迭代器。

`end()`：返回指向末尾的下一个元素的迭代器。`end()` 不指向某个元素，但它是末尾元素的后继。

`size()`：返回容器内的元素个数。

`max_size()`：返回容器 **理论上** 能存储的最大元素个数。依容器类型和所存储变量的类型而变。

`empty()`：返回容器是否为空。

`swap()`：交换两个容器。

`clear()`：清空容器。

`==`/`!=`/`<`/`>`/`<=`/`>=`：按 **字典序** 比较两个容器的大小。（比较元素大小时 `map` 的每个元素相当于 `set<pair<key, value> >`，无序容器不支持 `<`/`>`/`<=`/`>=`。）

# 序列式容器

## `vector`

`std::vector` 是 STL 提供的 **内存连续的**、**可变长度** 的数组（亦称列表）数据结构。能够提供线性复杂度的插入和删除，以及常数复杂度的随机访问。

### 为什么要使用 `vector`

作为 OIer，对程序效率的追求远比对工程级别的稳定性要高得多，而 `vector` 由于其对内存的动态处理，时间效率在部分情况下低于静态数组，并且在 OJ 服务器不一定开全优化的情况下更加糟糕。所以在正常存储数据的时候，通常不选择 `vector`。下面给出几个 `vector` 优秀的特性，在需要用到这些特性的情况下，`vector` 能给我们带来很大的帮助。

#### `vector` 可以动态分配内存

很多时候我们不能提前开好那么大的空间（eg：预处理 1~n 中所有数的约数）。尽管我们能知道数据总量在空间允许的级别，但是单份数据还可能非常大，这种时候我们就需要 `vector` 来把内存占用量控制在合适的范围内。`vector` 还支持动态扩容，在内存非常紧张的时候这个特性就能派上用场了。

#### `vector` 重写了比较运算符及赋值运算符

`vector` 重载了六个比较运算符，以字典序实现，这使得我们可以方便的判断两个容器是否相等（复杂度与容器大小成线性关系）。例如可以利用 `vector<char>` 实现字符串比较（当然，还是用 `std::string` 会更快更方便）。另外 `vector` 也重载了赋值运算符，使得数组拷贝更加方便。

#### `vector` 便利的初始化

由于 `vector` 重载了 `=` 运算符，所以我们可以方便的初始化。此外从 C++11 起 `vector` 还支持 [列表初始化](https://zh.cppreference.com/w/cpp/language/list_initialization)，例如 `vector<int> data {1, 2, 3};`。

### `vector` 的使用方法

以下介绍常用用法，详细内容 [请参见 C++ 文档](https://zh.cppreference.com/w/cpp/container/vector)。

#### 构造函数

用例参见如下代码（假设你已经 `using` 了 `std` 命名空间相关类型）：

```c++
// 1. 创建空vector; 常数复杂度
vector<int> v0;
// 1+. 这句代码可以使得向vector中插入前3个元素时，保证常数时间复杂度
v0.reserve(3);
// 2. 创建一个初始空间为3的vector，其元素的默认值是0; 线性复杂度
vector<int> v1(3);
// 3. 创建一个初始空间为3的vector，其元素的默认值是2; 线性复杂度
vector<int> v2(3, 2);
// 4. 创建一个初始空间为3的vector，其元素的默认值是1，
// 并且使用v2的空间配置器; 线性复杂度
vector<int> v3(3, 1, v2.get_allocator());
// 5. 创建一个v2的拷贝vector v4， 其内容元素和v2一样; 线性复杂度
vector<int> v4(v2);
// 6. 创建一个v4的拷贝vector v5，其内容是{v4[1], v4[2]}; 线性复杂度
vector<int> v5(v4.begin() + 1, v4.begin() + 3);
// 7. 移动v2到新创建的vector v6，不发生拷贝; 常数复杂度; 需要 C++11
vector<int> v6(std::move(v2));  // 或者 v6 = std::move(v2);
```

可以利用上述的方法构造一个 `vector`，足够我们使用了。

#### 元素访问

`vector` 提供了如下几种方法进行元素访问

1. `at()`

   `v.at(pos)` 返回容器中下标为 `pos` 的引用。如果数组越界抛出 `std::out_of_range` 类型的异常。

2. `operator[]`

   `v[pos]` 返回容器中下标为 `pos` 的引用。不执行越界检查。

3. `front()`

   `v.front()` 返回首元素的引用。

4. `back()`

   `v.back()` 返回末尾元素的引用。

5. `data()`

   `v.data()` 返回指向数组第一个元素的指针。

#### 迭代器

vector 提供了如下几种 [迭代器](https://oi-wiki.org/lang/csl/iterator/)

1. `begin()/cbegin()`

   返回指向首元素的迭代器，其中 `*begin = front`。

2. `end()/cend()`

   返回指向数组尾端占位符的迭代器，注意是没有元素的。

3. `rbegin()/crbegin()`

   返回指向逆向数组的首元素的逆向迭代器，可以理解为正向容器的末元素。

4. `rend()/crend()`

   返回指向逆向数组末元素后一位置的迭代器，对应容器首的前一个位置，没有元素。

以上列出的迭代器中，含有字符 `c` 的为只读迭代器，你不能通过只读迭代器去修改 `vector` 中的元素的值。如果一个 `vector` 本身就是只读的，那么它的一般迭代器和只读迭代器完全等价。只读迭代器自 C++11 开始支持。

#### 长度和容量

`vector` 有以下几个与容器长度和容量相关的函数。注意，`vector` 的长度（size）指有效元素数量，而容量（capacity）指其实际分配的内存长度，相关细节请参见后文的实现细节介绍。

**与长度相关**：

- `empty()` 返回一个 `bool` 值，即 `v.begin() == v.end()`，`true` 为空，`false` 为非空。

- `size()` 返回容器长度（元素数量），即 `std::distance(v.begin(), v.end())`。

- `resize()` 改变 `vector` 的长度，多退少补。补充元素可以由参数指定。

- `max_size()` 返回容器的最大可能长度。

  **与容量相关**：

- `reserve()` 使得 `vector` 预留一定的内存空间，避免不必要的内存拷贝。

- `capacity()` 返回容器的容量，即不发生拷贝的情况下容器的长度上限。

- `shrink_to_fit()` 使得 `vector` 的容量与长度一致，多退但不会少。

### 元素增删及修改

- `clear()` 清除所有元素
- `insert()` 支持在某个迭代器位置插入元素、可以插入多个。**复杂度与 `pos` 距离末尾长度成线性而非常数的**
- `erase()` 删除某个迭代器或者区间的元素，返回最后被删除的迭代器。复杂度与 `insert` 一致。
- `push_back()` 在末尾插入一个元素，均摊复杂度为 **常数**，最坏为线性复杂度。
- `pop_back()` 删除末尾元素，常数复杂度。
- `swap()` 与另一个容器进行交换，此操作是 **常数复杂度** 而非线性的。

### `vector` 的实现细节

`vector` 的底层其实仍然是定长数组，它能够实现动态扩容的原因是增加了避免数量溢出的操作。首先需要指明的是 `vector` 中元素的数量（长度） 与它已分配内存最多能包含元素的数量（容量） 是不一致的，`vector` 会分开存储这两个量。当向 `vector` 中添加元素时，如发现 ，那么容器会分配一个尺寸为 的数组，然后将旧数据从原本的位置拷贝到新的数组中，再将原来的内存释放。尽管这个操作的渐进复杂度是 ，但是可以证明其均摊复杂度为 。而在末尾删除元素和访问元素则都仍然是 的开销。 因此，只要对 `vector` 的尺寸估计得当并善用 `resize()` 和 `reserve()`，就能使得 `vector` 的效率与定长数组不会有太大差距。

### `vector<bool>`

标准库特别提供了对 `bool` 的 `vector` 特化，每个“`bool`”只占 1 bit，且支持动态增长。但是其 `operator[]` 的返回值的类型不是 `bool&` 而是 `vector<bool>::reference`。因此，使用 `vector<bool>` 使需谨慎，可以考虑使用 `deque<bool>` 或 `vector<char>` 替代。而如果你需要节省空间，请直接使用 [`bitset`](https://oi-wiki.org/lang/csl/bitset/)。

## `array`(C++11)

`std::array` 是 STL 提供的 **内存连续的**、**固定长度** 的数组数据结构。其本质是对原生数组的直接封装。

### 为什么要用 `array`

`array` 实际上是 STL 对数组的封装。它相比 `vector` 牺牲了动态扩容的特性，但是换来了与原生数组几乎一致的性能（在开满优化的前提下）。因此如果能使用 C++11 特性的情况下，能够使用原生数组的地方几乎都可以直接把定长数组都换成 `array`，而动态分配的数组可以替换为 `vector`。

### 成员函数

#### 隐式定义的成员函数

| 函数        | 作用                                                 |
| :---------- | :--------------------------------------------------- |
| `operator=` | 以来自另一 `array` 的每个元素重写 `array` 的对应元素 |

#### 元素访问

| 函数         | 作用                                |
| :----------- | :---------------------------------- |
| `at`         | 访问指定的元素，同时进行越界检查    |
| `operator[]` | 访问指定的元素，**不** 进行越界检查 |
| `front`      | 访问第一个元素                      |
| `back`       | 访问最后一个元素                    |
| `data`       | 返回指向内存中数组第一个元素的指针  |

`at` 若遇 `pos >= size()` 的情况会抛出 `std::out_of_range`。

#### 容量

| 函数       | 作用                   |
| :--------- | :--------------------- |
| `empty`    | 检查容器是否为空       |
| `size`     | 返回容纳的元素数       |
| `max_size` | 返回可容纳的最大元素数 |

由于每个 `array` 都是固定大小容器，`size()` 返回的值等于 `max_size()` 返回的值。

### 操作

| 函数   | 作用             |
| :----- | :--------------- |
| `fill` | 以指定值填充容器 |
| `swap` | 交换内容         |

**注意，交换两个 `array` 是 的，而非与常规 STL 容器一样为 。**

### 非成员函数

| 函数            | 作用                          |
| :-------------- | :---------------------------- |
| `operator==` 等 | 按照字典序比较 `array` 中的值 |
| `std::get`      | 访问 `array` 的一个元素       |
| `std::swap`     | 特化的 `std::swap` 算法       |

下面是一个 `array` 的使用示例：

```C++
// 1. 创建空array，长度为3; 常数复杂度 
std::array<int, 3> v0; 
// 2. 用指定常数创建array; 常数复杂度 
std::array<int, 3> v1{1, 2, 3}; v0.fill(1);  
// 填充数组 // 访问数组 
for (int i = 0; i != arr.size(); ++i) cout << arr[i] << " ";
```



## `deque`

`std::deque` 是 STL 提供的 [双端队列](https://oi-wiki.org/ds/queue/) 数据结构。能够提供线性复杂度的插入和删除，以及常数复杂度的随机访问。

### `deque` 的使用方法

以下介绍常用用法，详细内容 [请参见 C++ 文档](https://zh.cppreference.com/w/cpp/container/deque)。`deque` 的迭代器函数与 `vector` 相同，因此不作详细介绍。

#### 构造函数

参见如下代码（假设你已经 `using` 了 `std` 命名空间相关类型）：

```C++
// 1. 定义一个int类型的空双端队列 v0
deque<int> v0;
// 2. 定义一个int类型的双端队列 v1，并设置初始大小为10; 线性复杂度
deque<int> v1(10);
// 3. 定义一个int类型的双端队列 v2，并初始化为10个1; 线性复杂度
deque<int> v2(10, 1);
// 4. 复制已有的双端队列 v1; 线性复杂度
deque<int> v3(v1);
// 5. 创建一个v2的拷贝deque v4，其内容是v4[0]至v4[2]; 线性复杂度
deque<int> v4(v2.begin(), v2.begin() + 3);
// 6. 移动v2到新创建的deque v5，不发生拷贝; 常数复杂度; 需要 C++11
deque<int> v5(std::move(v2));
```

#### 元素访问

与 `vector` 一致，但无法访问底层内存。其高效的元素访问速度可参考实现细节部分。

- `at()` 返回容器中指定位置元素的引用，执行越界检查，**常数复杂度**。
- `operator[]` 返回容器中指定位置元素的引用。不执行越界检查，**常数复杂度**。
- `front()` 返回首元素的引用。
- `back()` 返回末尾元素的引用。

#### 迭代器

与 `vector` 一致。

#### 长度

与 `vector` 一致，但是没有 `reserve()` 和 `capacity()` 函数。（仍然有 `shrink_to_fit()` 函数）

#### 元素增删及修改

与 `vector` 一致，并额外有向队列头部增加元素的函数。

- `clear()` 清除所有元素
- `insert()` 支持在某个迭代器位置插入元素、可以插入多个。**复杂度与 `pos` 与两端距离较小者成线性**。
- `erase()` 删除某个迭代器或者区间的元素，返回最后被删除的迭代器。复杂度与 `insert` 一致。
- `push_front()` 在头部插入一个元素，**常数复杂度**。
- `pop_front()` 删除头部元素，**常数复杂度**。
- `push_back()` 在末尾插入一个元素，**常数复杂度**。
- `pop_back()` 删除末尾元素，**常数复杂度**。
- `swap()` 与另一个容器进行交换，此操作是 **常数复杂度** 而非线性的。

### `deque` 的实现细节

`deque` 通常的底层实现是多个不连续的缓冲区，而缓冲区中的内存是连续的。而每个缓冲区还会记录首指针和尾指针，用来标记有效数据的区间。当一个缓冲区填满之后便会在之前或者之后分配新的缓冲区来存储更多的数据。更详细的说明可以参考 [STL 源码剖析——deque 的实现原理和使用方法详解](https://blog.csdn.net/baidu_28312631/article/details/48000123)。

## `list`

`std::list` 是 STL 提供的 [双向链表](https://oi-wiki.org/ds/linked-list/) 数据结构。能够提供线性复杂度的随机访问，以及常数复杂度的插入和删除。

### `list` 的使用方法

`list` 的使用方法与 `deque` 基本相同，但是增删操作和访问的复杂度不同。详细内容 [请参见 C++ 文档](https://zh.cppreference.com/w/cpp/container/list)。`list` 的迭代器、长度、元素增删及修改相关的函数与 `deque` 相同，因此不作详细介绍。

#### 元素访问

由于 `list` 的实现是链表，因此它不提供随机访问的接口。若需要访问中间元素，则需要使用迭代器。

- `front()` 返回首元素的引用。
- `back()` 返回末尾元素的引用。

#### 操作

`list` 类型还提供了一些针对其特性实现的 STL 算法函数。由于这些算法需要 [随机访问迭代器](https://oi-wiki.org/lang/csl/iterator/)，因此 `list` 提供了特别的实现以便于使用。这些算法有 `splice()`、`remove()`、`sort()`、`unique()`、`merge()` 等。

## `forward_list`（C++11）

`std::forward_list` 是 STL 提供的 [单向链表](https://oi-wiki.org/ds/linked-list/) 数据结构，相比于 `std::list` 减小了空间开销。

### `forward_list` 的使用方法

`forward_list` 的使用方法与 `list` 几乎一致，但是迭代器只有单向的，因此其具体用法不作详细介绍。详细内容 [请参见 C++ 文档](https://zh.cppreference.com/w/cpp/container/forward_list)

# 关联式容器

## `set`

`set` 是关联容器，含有键值类型对象的已排序集，搜索、移除和插入拥有对数复杂度。`set` 内部通常采用红黑树实现。平衡二叉树的特性使得 `set` 非常适合处理需要同时兼顾查找、插入与删除的情况。

和数学中的集合相似，`set` 中不会出现值相同的元素。如果需要有相同元素的集合，需要使用 `multiset`。`multiset` 的使用方法与 `set` 的使用方法基本相同。

### 插入与删除操作

- `insert(x)` 当容器中没有等价元素的时候，将元素 x 插入到 `set` 中。
- `erase(x)` 删除值为 x 的 **所有** 元素，返回删除元素的个数。
- `erase(pos)` 删除迭代器为 pos 的元素，要求迭代器必须合法。
- `erase(first,last)` 删除迭代器在 范围内的所有元素。
- `clear()` 清空 `set`。

### insert 函数的返回值
insert 函数的返回值类型为 pair<iterator, bool>，其中 iterator 是一个指向所插入元素（或者是指向等于所插入值的原本就在容器中的元素）的迭代器，而 bool 则代表元素是否插入成功，由于 set 中的元素具有唯一性质，所以如果在 set 中已有等值元素，则插入会失败，返回 false，否则插入成功，返回 true；map 中的 insert 也是如此。

### 迭代器

`set` 提供了以下几种迭代器：

1. `begin()/cbegin()`
   返回指向首元素的迭代器，其中 `*begin = front`。
2. `end()/cend()`
   返回指向数组尾端占位符的迭代器，注意是没有元素的。
3. `rbegin()/crbegin()`
   返回指向逆向数组的首元素的逆向迭代器，可以理解为正向容器的末元素。
4. `rend()/crend()`
   返回指向逆向数组末元素后一位置的迭代器，对应容器首的前一个位置，没有元素。

以上列出的迭代器中，含有字符 `c` 的为只读迭代器，你不能通过只读迭代器去修改 `set` 中的元素的值。如果一个 `set` 本身就是只读的，那么它的一般迭代器和只读迭代器完全等价。只读迭代器自 C++11 开始支持。

### 查找操作

- `count(x)` 返回 `set` 内键为 x 的元素数量。
- `find(x)` 在 `set` 内存在键为 x 的元素时会返回该元素的迭代器，否则返回 `end()`。
- `lower_bound(x)` 返回指向首个不小于给定键的元素的迭代器。如果不存在这样的元素，返回 `end()`。
- `upper_bound(x)` 返回指向首个大于给定键的元素的迭代器。如果不存在这样的元素，返回 `end()`。
- `empty()` 返回容器是否为空。
- `size()` 返回容器内元素个数。

### lower_bound 和 upper_bound 的时间复杂度
set 自带的 lower_bound 和 upper_bound 的时间复杂度为`O(log n)`。

但使用 algorithm 库中的 lower_bound 和 upper_bound 函数对 set 中的元素进行查询，时间复杂度为 `O(n)`。

### nth_element 的时间复杂度
set 没有提供自带的 nth_element。使用 algorithm 库中的 nth_element 查找第 k大的元素时间复杂度为 `O(n)`。

如果需要实现平衡二叉树所具备的`O(log n)`查找第k大元素的功能，需要自己手写平衡二叉树或权值线段树，或者选择使用 pb_ds 库中的平衡二叉树。

### 使用样例

#### `set` 在贪心中的使用

在贪心算法中经常会需要出现类似 **找出并删除最小的大于等于某个值的元素**。这种操作能轻松地通过 `set` 来完成。

```c++

// 现存可用的元素
set<int> available;
// 需要大于等于的值
int x;

// 查找最小的大于等于x的元素
set<int>::iterator it = available.lower_bound(x);
if (it == available.end()) {
  // 不存在这样的元素，则进行相应操作……
} else {
  // 找到了这样的元素，将其从现存可用元素中移除
  available.erase(it);
  // 进行相应操作……
}
```

## `map`

`map` 是有序键值对容器，它的元素的键是唯一的。搜索、移除和插入操作拥有对数复杂度。`map` 通常实现为红黑树。

你可能需要存储一些键值对，例如存储学生姓名对应的分数：`Tom 0`，`Bob 100`，`Alan 100`。但是由于数组下标只能为非负整数，所以无法用姓名作为下标来存储，这个时候最简单的办法就是使用 STL 中的 `map` 了！

`map` 重载了 `operator[]`，可以用任意定义了 `operator <` 的类型作为下标（在 `map` 中叫做 `key`，也就是索引）：

```c++
map<Key, T> yourMap;
```

其中，`Key` 是键的类型，`T` 是值的类型，下面是使用 `map` 的实例：

```c++
map<string, int> mp;
```

`map` 中不会存在键相同的元素，`multimap` 中允许多个元素拥有同一键。`multimap` 的使用方法与 `map` 的使用方法基本相同。

### Warning

正是因为 `multimap` 允许多个元素拥有同一键的特点，`multimap` 并没有提供给出键访问其对应值的方法。

### 插入与删除操作

- 可以直接通过下标访问来进行查询或插入操作。例如 `mp["Alan"]=100`。
- 通过向 `map` 中插入一个类型为 `pair<Key, T>` 的值可以达到插入元素的目的，例如 `mp.insert(pair<string,int>("Alan",100));`；
- `erase(key)` 函数会删除键为 `key` 的 **所有** 元素。返回值为删除元素的数量。
- `erase(pos)`: 删除迭代器为 pos 的元素，要求迭代器必须合法。
- `erase(first,last)`: 删除迭代器在 范围内的所有元素。
- `clear()` 函数会清空整个容器。

### 下标访问中的注意事项
在利用下标访问 map 中的某个元素时，如果 map 中不存在相应键的元素，会自动在 map 中插入一个新元素，并将其值设置为默认值（对于整数，值为零；对于有默认构造函数的类型，会调用默认构造函数进行初始化）。

当下标访问操作过于频繁时，容器中会出现大量无意义元素，影响 map 的效率。因此一般情况下推荐使用 find() 函数来寻找特定键的元素。

### 查询操作

- `count(x)`: 返回容器内键为 x 的元素数量。复杂度为 （关于容器大小对数复杂度，加上匹配个数）。
- `find(x)`: 若容器内存在键为 x 的元素，会返回该元素的迭代器；否则返回 `end()`。
- `lower_bound(x)`: 返回指向首个不小于给定键的元素的迭代器。
- `upper_bound(x)`: 返回指向首个大于给定键的元素的迭代器。若容器内所有元素均小于或等于给定键，返回 `end()`。
- `empty()`: 返回容器是否为空。
- `size()`: 返回容器内元素个数。

### 使用样例

#### `map` 用于存储复杂状态

在搜索中，我们有时需要存储一些较为复杂的状态（如坐标，无法离散化的数值，字符串等）以及与之有关的答案（如到达此状态的最小步数）。`map` 可以用来实现此功能。其中的键是状态，而值是与之相关的答案。下面的示例展示了如何使用 `map` 存储以 `string` 表示的状态。

```c++
// 存储状态与对应的答案
map<string, int> record;

// 新搜索到的状态与对应答案
string status;
int ans;
// 查找对应的状态是否出现过
map<string, int>::iterator it = record.find(status);
if (it == record.end()) {
  // 尚未搜索过该状态，将其加入状态记录中
  record[status] = ans;
  // 进行相应操作……
} else {
  // 已经搜索过该状态，进行相应操作……
}
```

## 遍历容器

可以利用迭代器来遍历关联式容器的所有元素。

```c++
set<int> s;
typedef set<int>::iterator si;
for (si it = s.begin(); it != s.end(); it++) cout << *it << endl;
```

需要注意的是，对 `map` 的迭代器解引用后，得到的是类型为 `pair<Key, T>` 的键值对。

在 C++11 中，使用范围 for 循环会让代码简洁很多：

```c++
set<int> s;
for (auto x : s) cout << x << endl;
```

对于任意关联式容器，使用迭代器遍历容器的时间复杂度均为 `O(n)`。

## 自定义比较方式

`set` 在默认情况下的比较函数为 `<`（如果是非内置类型需要 [重载 `<` 运算符](https://oi-wiki.org/lang/op-overload/#compare)）。然而在某些特殊情况下，我们希望能自定义 `set` 内部的比较方式。

这时候可以通过传入自定义比较器来解决问题。

具体来说，我们需要定义一个类，并在这个类中 [重载 `()` 运算符](https://oi-wiki.org/lang/op-overload/#function)。

例如，我们想要维护一个存储整数，且较大值靠前的 `set`，可以这样实现：

```C++
struct cmp {
  bool operator()(int a, int b) { return a > b; }
};
set<int, cmp> s;
```

对于其他关联式容器，可以用类似的方式实现自定义比较，这里不再赘述。

# 无序关联式容器

## 概述

自 C++11 标准起，四种基于哈希实现的无序关联式容器正式纳入了 C++ 的标准模板库中，分别是：`unordered_set`，`unordered_multiset`，`unordered_map`，`unordered_multimap`。

它们与相应的关联式容器在功能，函数等方面有诸多共同点，而最大的不同点则体现在普通的关联式容器一般采用红黑树实现，内部元素按特定顺序进行排序；而这几种无序关联式容器则采用哈希方式存储元素，内部元素不以任何特定顺序进行排序，所以访问无序关联式容器中的元素时，访问顺序也没有任何保证。

采用哈希存储的特点使得无序关联式容器 **在平均情况下** 大多数操作（包括查找，插入，删除）都能在常数时间复杂度内完成，相较于关联式容器与容器大小成对数的时间复杂度更加优秀。

## Warning

在最坏情况下，对无序关联式容器进行插入、删除、查找等操作的时间复杂度会 **与容器大小成线性关系**！这一情况往往在容器内出现大量哈希冲突时产生。

同时，由于无序关联式容器的操作时通常存在较大的常数，其效率有时并不比普通的关联式容器好太多。

因此应谨慎使用无序关联式容器，尽量避免滥用（例如懒得离散化，直接将 `unordered_map<int, int>` 当作空间无限的普通数组使用）。

由于无序关联式容器与相应的关联式容器在用途和操作中有很多共同点，这里不再介绍无序关联式容器的各种操作，这些内容读者可以参考 [关联式容器](https://oi-wiki.org/lang/csl/associative-container/)。

## 制造哈希冲突

上文中提到了，在最坏情况下，对无序关联式容器进行一些操作的时间复杂度会与容器大小成线性关系。

在哈希函数确定的情况下，可以构造出数据使得容器内产生大量哈希冲突，导致复杂度达到上界。

在标准库实现里，每个元素的散列值是将值对一个质数取模得到的，更具体地说，是 [这个列表](https://github.com/gcc-mirror/gcc/blob/releases/gcc-8.1.0/libstdc%2B%2B-v3/src/shared/hashtable-aux.cc) 中的质数（g++ 6 及以前版本的编译器，这个质数一般是 ，g++ 7 及之后版本的编译器，这个质数一般是 ）。

因此可以通过向容器中插入这些模数的倍数来达到制造大量哈希冲突的目的。

## 自定义哈希函数

使用自定义哈希函数可以有效避免构造数据产生的大量哈希冲突。

要想使用自定义哈希函数，需要定义一个结构体，并在结构体中重载 `()` 运算符，像这样：

```C++
struct my_hash {
  size_t operator()(int x) const { return x; }
};
```

当然，为了确保哈希函数不会被迅速破解（例如 Codeforces 中对使用无序关联式容器的提交进行 hack），可以试着在哈希函数中加入一些随机化函数（如时间）来增加破解的难度。

例如，在 [这篇博客](https://codeforces.com/blog/entry/62393) 中给出了如下哈希函数：

```C++
struct my_hash {
  static uint64_t splitmix64(uint64_t x) {
    x += 0x9e3779b97f4a7c15;
    x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
    x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
    return x ^ (x >> 31);
  }

  size_t operator()(uint64_t x) const {
    static const uint64_t FIXED_RANDOM =
        chrono::steady_clock::now().time_since_epoch().count();
    return splitmix64(x + FIXED_RANDOM);
  }

  // 针对 std::pair<int, int> 作为主键类型的哈希函数
  size_t operator()(pair<uint64_t, uint64_t> x) const {
    static const uint64_t FIXED_RANDOM =
        chrono::steady_clock::now().time_since_epoch().count();
    return splitmix64(x.first + FIXED_RANDOM) ^
           (splitmix64(x.second + FIXED_RANDOM) >> 1);
  }
};
```

写完自定义的哈希函数后，就可以通过 `unordered_map<int, int, my_hash> my_map;` 或者 `unordered_map<pair<int, int>, int, my_hash> my_pair_map;` 的定义方式将自定义的哈希函数传入容器了。

# 容器适配器

## 栈

STL 栈 (`std::stack`) 是一种后进先出 (Last In, First Out) 的容器适配器，仅支持查询或删除最后一个加入的元素（栈顶元素），不支持随机访问，且为了保证数据的严格有序性，不支持迭代器。

### 头文件

`#include <stack> `

### 定义

```C++
std::stack<TypeName> s;  // 使用默认底层容器 deque，数据类型为 TypeName
std::stack<TypeName, Container> s;  // 使用 Container 作为底层容器
std::stack<TypeName> s2(s1);        // 将 s1 复制一份用于构造 s2
```

### 成员函数[¶](https://oi-wiki.org/lang/csl/container-adapter/#_4)

#### 以下所有函数均为常数复杂度[¶](https://oi-wiki.org/lang/csl/container-adapter/#_5)

- `top()` 访问栈顶元素（如果栈为空，此处会出错）
- `push(x)` 向栈中插入元素 x
- `pop()` 删除栈顶元素
- `size()` 查询容器中的元素数量
- `empty()` 询问容器是否为空

### 简单示例

```C++
std::stack<int> s1;
s1.push(2);
s1.push(1);
std::stack<int> s2(s1);
s1.pop();
std::cout << s1.size() << " " << s2.size() << endl;  // 1 2
std::cout << s1.top() << " " << s2.top() << endl;    // 2 1
s1.pop();
std::cout << s1.empty() << " " << s2.empty() << endl;  // 1 0
```

## 队列

STL 队列 (`std::queue`) 是一种先进先出 (First In, First Out) 的容器适配器，仅支持查询或删除第一个加入的元素（队首元素），不支持随机访问，且为了保证数据的严格有序性，不支持迭代器。

### 头文件

` #include <queue> `

### 定义

```C++
std::queue<TypeName> q;  // 使用默认底层容器 deque，数据类型为 TypeName
std::queue<TypeName, Container> q;  // 使用 Container 作为底层容器

std::queue<TypeName> q2(q1);  // 将 s1 复制一份用于构造 q2
```

### 成员函数

#### 以下所有函数均为常数复杂度

- `front()` 访问队首元素（如果队列为空，此处会出错）
- `push(x)` 向队列中插入元素 x
- `pop()` 删除队首元素
- `size()` 查询容器中的元素数量
- `empty()` 询问容器是否为空

### 简单示例

```C++
std::queue<int> q1;
q1.push(2);
q1.push(1);
std::queue<int> q2(q1);
q1.pop();
std::cout << q1.size() << " " << q2.size() << endl;    // 1 2
std::cout << q1.front() << " " << q2.front() << endl;  // 1 2
q1.pop();
std::cout << q1.empty() << " " << q2.empty() << endl;  // 1 0
```

## 优先队列

### 头文件

` #include <queue> `

### 定义

```C++
std::priority_queue<TypeName> q;             // 数据类型为 TypeName
std::priority_queue<TypeName, Container> q;  // 使用 Container 作为底层容器
std::priority_queue<TypeName, Container, Compare> q;
// 使用 Container 作为底层容器，使用 Compare 作为比较类型

// 默认使用底层容器 vector
// 比较类型 less<TypeName>（此时为它的 top() 返回为最大值）
// 若希望 top() 返回最小值，可令比较类型为 greater<TypeName>
// 注意：不可跳过 Container 直接传入 Compare

// 从 C++11 开始，如果使用 lambda 函数自定义 Compare
// 则需要将其作为构造函数的参数代入，如：
auto cmp = [](const std::pair<int, int> &l, const std::pair<int, int> &r) {
  return l.second < r.second;
};
std::priority_queue<std::pair<int, int>, std::vector<std::pair<int, int> >,
                    decltype(cmp)>
    pq(cmp);
```

### 成员函数

#### 以下所有函数均为常数复杂度

- `top()` 访问堆顶元素（此时优先队列不能为空）
- `empty()` 询问容器是否为空
- `size()` 查询容器中的元素数量

#### 以下所有函数均为对数复杂度

- `push(x)` 插入元素，并对底层容器排序
- `pop()` 删除堆顶元素（此时优先队列不能为空）

### 简单示例

```C++
std::priority_queue<int> q1;
std::priority_queue<int, std::vector<int> > q2;
// C++11 后空格可省略
std::priority_queue<int, std::deque<int>, std::greater<int> > q3;
// q3 为小根堆
for (int i = 1; i <= 5; i++) q1.push(i);
// q1 中元素 :  [1, 2, 3, 4, 5]
std::cout << q1.top() << std::endl;
// 输出结果 : 5
q1.pop();
// 堆中元素 : [1, 2, 3, 4]
std::cout << q1.size() << std::endl;
// 输出结果 ：4
for (int i = 1; i <= 5; i++) q3.push(i);
// q3 中元素 :  [1, 2, 3, 4, 5]
std::cout << q3.top() << std::endl;
// 输出结果 : 1
```

# STL算法

STL 提供了大约 100 个实现算法的模版函数，基本都包含在 `<algorithm>` 之中，还有一部分包含在 `<numeric>` 和 `<functional>`。完备的函数列表请 [参见参考手册](https://zh.cppreference.com/w/cpp/algorithm)，排序相关的可以参考 [排序内容的对应页面](https://oi-wiki.org/basic/stl-sort/)。

- `find`：顺序查找。`find(v.begin(), v.end(), value)`，其中 `value` 为需要查找的值。
- `find_end`：逆序查找。`find_end(v.begin(), v.end(), value)`。
- `reverse`：翻转数组、字符串。`reverse(v.begin(), v.end())` 或 `reverse(a + begin, a + end)`。
- `unique`：去除容器中相邻的重复元素。`unique(ForwardIterator first, ForwardIterator last)`，返回值为指向 **去重后** 容器结尾的迭代器，原容器大小不变。与 `sort` 结合使用可以实现完整容器去重。
- `random_shuffle`：随机地打乱数组。`random_shuffle(v.begin(), v.end())` 或 `random_shuffle(v + begin, v + end)`。

- `sort`：排序。`sort(v.begin(), v.end(), cmp)` 或 `sort(a + begin, a + end, cmp)`，其中 `end` 是排序的数组最后一个元素的后一位，`cmp` 为自定义的比较函数。
- `stable_sort`：稳定排序，用法同 `sort()`。
- `nth_element`：按指定范围进行分类，即找出序列中第 大的元素，使其左边均为小于它的数，右边均为大于它的数。`nth_element(v.begin(), v.begin() + mid, v.end(), cmp)` 或 `nth_element(a + begin, a + begin + mid, a + end, cmp)`。
- `binary_search`：二分查找。`binary_search(v.begin(), v.end(), value)`，其中 `value` 为需要查找的值。
- `merge`：将两个（已排序的）序列 **有序合并** 到第三个序列的 **插入迭代器** 上。`merge(v1.begin(), v1.end(), v2.begin(), v2.end() ,back_inserter(v3))`。
- `inplace_merge`：将两个（已按小于运算符排序的）：`[first,middle), [middle,last)` 范围 **原地合并为一个有序序列**。`inplace_merge(v.begin(), v.begin() + middle, v.end())`。
- `lower_bound`：在一个有序序列中进行二分查找，返回指向第一个 **大于等于** 的元素的位置的迭代器。如果不存在这样的元素，则返回尾迭代器。`lower_bound(v.begin(),v.end(),x)`。
- `upper_bound`：在一个有序序列中进行二分查找，返回指向第一个 **大于** 的元素的位置的迭代器。如果不存在这样的元素，则返回尾迭代器。`upper_bound(v.begin(),v.end(),x)`。

- `next_permutation`：将当前排列更改为 **全排列中的下一个排列**。如果当前排列已经是 **全排列中的最后一个排列**（元素完全从大到小排列），函数返回 `false` 并将排列更改为 **全排列中的第一个排列**（元素完全从小到大排列）；否则，函数返回 `true`。`next_permutation(v.begin(), v.end())` 或 `next_permutation(v + begin, v + end)`。
- `partial_sum`：求前缀和。设源容器为 ，目标容器为 ，则令 。`partial_sum(src.begin(), src.end(), back_inserter(dst))`。

### 使用样例

- 使用 `next_permutation` 生成 1 到9的全排列。例题：[Luogu P1706 全排列问题](https://www.luogu.com.cn/problem/P1706)

```C++
int N = 9, a[] = {1, 2, 3, 4, 5, 6, 7, 8, 9};
do {
  for (int i = 0; i < N; i++) cout << a[i] << " ";
  cout << endl;
} while (next_permutation(a, a + N));
```

- 使用 `lower_bound` 与 `upper_bound` 查找有序数组a中小于x，等于x，大于x元素的分界线。

```C++
int N = 10, a[] = {1, 1, 2, 4, 5, 5, 7, 7, 9, 9}, x = 5;
int i = lower_bound(a, a + N, x) - a, j = upper_bound(a, a + N, x) - a;
// a[0] ~ a[i - 1] 为小于x的元素， a[i] ~ a[j - 1] 为等于x的元素， a[j] ~ a[N -
// 1] 为大于x的元素
cout << i << " " << j << endl;
```

- 使用 `partial_sum` 求解src中元素的前缀和，并存储于dst中。

```C++
vector<int> src = {1, 2, 3, 4, 5}, dst;
// 求解src中元素的前缀和，dst[i] = src[0] + ... + src[i]
// back_inserter 函数作用在 dst 容器上，提供一个迭代器
partial_sum(src.begin(), src.end(), back_inserter(dst));
for (unsigned int i = 0; i < dst.size(); i++) cout << dst[i] << " ";
```

- 使用 `lower_bound` 查找有序数组a中最接近x的元素。例题：[UVA10487 Closest Sums](https://www.luogu.com.cn/problem/UVA10487)

```C++
int N = 10, a[] = {1, 1, 2, 4, 5, 5, 8, 8, 9, 9}, x = 6;
// lower_bound将返回a中第一个大于等于x的元素的地址，计算出的i为其下标
int i = lower_bound(a, a + N, x) - a;
// 在以下两种情况下，a[i] (a中第一个大于等于x的元素) 即为答案：
// 1. a中最小的元素都大于等于x；
// 2. a中存在大于等于x的元素，且第一个大于等于x的元素 (a[i])
// 相比于第一个小于x的元素 (a[i - 1]) 更接近x；
// 否则，a[i - 1] (a中第一个小于x的元素) 即为答案
if (i == 0 || (i < N && a[i] - x < x - a[i - 1]))
  cout << a[i];
else
  cout << a[i - 1];
```

- 使用 `sort` 与 `unique` 查找数组 中 **第 小的值**（注意：重复出现的值仅算一次，因此本题不是求解第 小的元素）。例题：[Luogu P1138 第 k 小整数](https://www.luogu.com.cn/problem/P1138)

```C++
int N = 10, a[] = {1, 3, 3, 7, 2, 5, 1, 2, 4, 6}, k = 3;
sort(a, a + N);
// unique将返回去重之后数组最后一个元素之后的地址，计算出的cnt为去重后数组的长度
int cnt = unique(a, a + N) - a;
cout << a[k - 1];
```

# bitset

## 介绍[¶](https://oi-wiki.org/lang/csl/bitset/#_1)

`std::bitset` 是标准库中的一个存储 `0/1` 的大小不可变容器。严格来讲，它并不属于 STL。

由于内存地址是按字节即 `byte` 寻址，而非比特 `bit`，一个 `bool` 类型的变量，虽然只能表示 `0/1`, 但是也占了 1 byte 的内存。

`bitset` 就是通过固定的优化，使得一个字节的八个比特能分别储存 8 位的 `0/1`。

对于一个 4 字节的 `int` 变量，在只存 `0/1` 的意义下，`bitset` 占用空间只是其 ，计算一些信息时，所需时间也是其 。

在某些情况下通过 `bitset` 可以优化程序的运行效率。至于其优化的是复杂度还是常数，要看计算复杂度的角度。一般 `bitset` 的复杂度有以下几种记法：（设原复杂度为 ）

1. ，这种记法认为 `bitset` 完全没有优化复杂度。
2. ，这种记法不太严谨（复杂度中不应出现常数），但体现了 `bitset` 能将所需时间优化至 。
3. ，其中 （计算机的位数），这种记法较为普遍接受。
4. ，其中 为计算机一个整型变量的大小。

当然，`vector` 的一个特化 `vector<bool>` 的储存方式同 `bitset` 一样，区别在于其支持动态开空间，`bitset` 则和我们一般的静态数组一样，是在编译时就开好了的。

然而，`bitset` 有一些好用的库函数，不仅方便，而且有时可以避免使用 for 循环而没有实质的速度优化。因此，一般不使用 `vector<bool>`。

## 使用

### 头文件

` #include <bitset> `

### 指定大小

 `bitset<1000> bs;  // a bitset with 1000 bits `

### 构造函数

- `bitset()`: 每一位都是 `false`。
- `bitset(unsigned long val)`: 设为 `val` 的二进制形式。
- `bitset(const string& str)`: 设为 串 `str`。

### 运算符

- `operator []`: 访问其特定的一位。
- `operator ==/!=`: 比较两个 `bitset` 内容是否完全一样。
- `operator &/&=/|/| =/^/^=/~`: 进行按位与/或/异或/取反操作。**`bitset` 只能与 `bitset` 进行位运算**，若要和整型进行位运算，要先将整型转换为 `bitset`。
- `operator <>/<<=/>>=`: 进行二进制左移/右移。
- `operator <>`: 流运算符，这意味着你可以通过 `cin/cout` 进行输入输出。

### 成员函数

- `count()`: 返回 `true` 的数量。
- `size()`: 返回 `bitset` 的大小。
- `test(pos)`: 它和 `vector` 中的 `at()` 的作用是一样的，和 `[]` 运算符的区别就是越界检查。
- `any()`: 若存在某一位是 `true` 则返回 `true`，否则返回 `false`。
- `none()`: 若所有位都是 `false` 则返回 `true`，否则返回 `false`。
- `all()`:**C++11**，若所有位都是 `true` 则返回 `true`，否则返回 `false`。
- 1. `set()`: 将整个 `bitset` 设置成 `true`。
  2. `set(pos, val = true)`: 将某一位设置成 `true`/`false`。
- 1. `reset()`: 将整个 `bitset` 设置成 `false`。
  2. `reset(pos)`: 将某一位设置成 `false`。相当于 `set(pos, false)`。
- 1. `flip()`: 翻转每一位。（，相当于异或一个全是 的 `bitset`）
  2. `flip(pos)`: 翻转某一位。
- `to_string()`: 返回转换成的字符串表达。
- `to_ulong()`: 返回转换成的 `unsigned long` 表达 (`long` 在 NT 及 32 位 POSIX 系统下与 `int` 一样，在 64 位 POSIX 下与 `long long` 一样）。
- `to_ullong()`:**C++11**，返回转换成的 `unsigned long long` 表达。

一些文档中没有的成员函数：

- `_Find_first()`: 返回 `bitset` 第一个 `true` 的下标，若没有 `true` 则返回 `bitset` 的大小。
- `_Find_next(pos)`: 返回 `pos` 后面（下标严格大于 `pos` 的位置）第一个 `true` 的下标，若 `pos` 后面没有 `true` 则返回 `bitset` 的大小。

# string

## `string` 是什么

`std::string` 是在标准库 `<string>`（注意不是 C 语言中的 `<string.h>` 库）中提供的一个类，本质上是 `std::basic_string<char>` 的别称。

## 为什么要使用 `string`

在 C 语言中，提供了字符串的操作，但只能通过字符数组的方式来实现字符串。而 `string` 则是一个简单的类，使用简单，在 OI 竞赛中被广泛使用。并且相较于其他 STL 容器，`string` 的常数可以算是非常优秀的，基本与字符数组不相上下。

### `string` 可以动态分配空间

和许多 STL 容器相同，`string` 能动态分配空间，这使得我们可以直接使用 `std::cin` 来输入，但其速度则同样较慢。这一点也同样让我们不必为内存而烦恼。

### `string` 重载了加法运算符和比较运算符

`string` 的加法运算符可以直接拼接两个字符串或一个字符串和一个字符。和 `std::vector` 类似，`string` 重载了比较运算符，同样是按字典序比较的，所以我们可以直接调用 `std::sort` 对若干字符串进行排序。

## 使用方法

下面介绍 `string` 的基本操作，具体可看 [C++ 文档](https://zh.cppreference.com/w/cpp/string/basic_string)。

### 声明

`std::string s; `

### 转 char 数组

在 C 语言里，也有很多字符串的函数，但是它们的参数都是 char 指针类型的，为了方便使用，`string` 有两个成员函数能够将自己转换为 char 指针——`data()`/`c_str()`（它们几乎是一样的，但最好使用 `c_str()`，因为 `c_str()` 保证末尾有空字符，而 `data()` 则不保证），如：

```C++
printf("%s", s);          // 编译错误
printf("%s", s.data());   // 编译通过，但是是 undefined behavior
printf("%s", s.c_str());  // 一定能够正确输出
```

### 获取长度[¶](https://oi-wiki.org/lang/csl/string/#_3)

很多函数都可以返回 string 的长度：

```C++

printf("s 的长度为 %d", s.size());
printf("s 的长度为 %d", s.length());
printf("s 的长度为 %d", strlen(s.c_str()));
```

### 寻找某字符（串）第一次出现的位置

```C++
printf("字符 a 在 s 的 %d 位置第一次出现", s.find('a'));
printf("字符串 t 在 s 的 %d 位置第一次出现", s.find(t));
printf("在 s 中自 pos 位置起字符串 t 第一次出现在 %d 位置", s.find(t, pos));
```

### 截取子串

`substr(pos, len)`，这个函数的参数是从 `pos` 位置开始截取最多 `len` 个字符（如果从 `pos` 开始的后缀长度不足 `len` 则截取这个后缀）。

```C++
printf("从这个字符串的第二位开始的最多三个字符构成的子串是 %s",
       s.substr(1, 3).c_str());
```

# pair

`std::pair` 是标准库中定义的一个类模板。用于将两个变量关联在一起，组成一个“对”，而且两个变量的数据类型可以是不同的。

## 使用

### 初始化

可以在定义时直接完成 `pair` 的初始化。

```C++
pair<int, double> p0(1, 2.0);
```

也可以使用先定义，后赋值的方法完成 `pair` 的初始化。

```C++
pair<int, double> p1;
p1.first = 1;
p1.second = 2.0;
```

还可以使用 `std::make_pair` 函数。该函数接受两个变量，并返回由这两个变量组成的 `pair`。

```C++
pair<int, double> p2 = make_pair(1, 2.0);
```

一种常用的方法是使用宏定义 `#define mp make_pair`，将有些冗长的 `make_pair` 化简为 `mp`。

在 C++11 以及之后的版本中，`make_pair` 可以配合 `auto` 使用，以避免显式声明数据类型。

```C++
auto p3 = make_pair(1, 2.0);
```

### 访问

通过成员函数 `first` 与 `second`，可以访问 `pair` 中包含的两个变量。

```C++
int i = p0.first;
double d = p0.second;
```

也可以对其进行修改。

```C++
p1.first++;
```

### 比较

`pair` 已经预先定义了所有的比较运算符，包括 `<`、`>`、`<=`、`>=`、`==`、`!=`。当然，这需要组成 `pair` 的两个变量所属的数据类型定义了 `==` 和/或 `<` 运算符。

其中，`<`、`>`、`<=`、`>=` 四个运算符会先比较两个 `pair` 中的第一个变量，在第一个变量相等的情况下再比较第二个变量

```C++
if (p2 >= p3) {
  cout << "do something here" << endl;
}
```

由于 `pair` 定义了 STL 中常用的 `<` 与 `==`，使得其能够很好的与其他 STL 函数或数据结构配合。比如，`pair` 可以作为 `priority_queue` 的数据类型。

```C++
priority_queue<pair<int, double> > q;
```

### 赋值与交换

可以将 `pair` 的值赋给另一个类型一致的 `pair`。

```C++
p0 = p1;
```

也可以使用 `swap` 函数交换 `pair` 的值。

```C++
swap(p0, p1);
p2.swap(p3);
```

## 应用举例

### 离散化

`pair` 可以轻松实现离散化。

我们可以创建一个 `pair` 数组，将原始数据的值作为每个 `pair` 第一个变量，将原始数据的位置作为第二个变量。在排序后，将原始数据值的排名（该值排序后所在的位置）赋给该值原本所在的位置即可。

```C++
// a为原始数据
pair<int, int> a[MAXN];
// ai为离散化后的数据
int ai[MAXN];
for (int i = 0; i < n; i++) {
  // first为原始数据的值，second为原始数据的位置
  scanf("%d", &a[i].first);
  a[i].second = i;
}
// 排序
sort(a, a + n);
for (int i = 0; i < n; i++) {
  // 将该值的排名赋给该值原本所在的位置
  ai[a[i].second] = i;
}
```

### Dijkstra

如前所述，`pair` 可以作为 `priority_queue` 的数据类型。

那么，在 Dijkstra 算法的堆优化中，可以使用 `pair` 与 `priority_queue` 维护节点，将节点当前到起点的距离作为第一个变量，将节点编号作为第二个变量。

```C++
priority_queue<pair<int, int>, std::vector<pair<int, int> >,
               std::greater<pair<int, int> > >
    q;
... while (!q.empty()) {
  // dis为入堆时节点到起点的距离，i为节点编号
  int dis = q.top().first, i = q.top().second;
  q.pop();
  ...
}
```

### pair 与 map

`map` 的是 C++ 中存储键值对的数据结构。很多情况下，`map` 中存储的键值对通过 `pair` 向外暴露。

```C++
map<int, double> m;
m.insert(make_pair(1, 2.0));
```

