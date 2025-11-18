# Modern C++23 Complete Cheatsheet

**A comprehensive, GitHub-ready reference covering the C++23 core language, STL, algorithms, containers, memory, threading, chrono, filesystem, ranges, and more.**

---

# Table of Contents

1. [Language Basics](#language-basics)
2. [Types & Value Categories](#types--value-categories)
3. [Expressions & Operators](#expressions--operators)
4. [Memory & Object Model](#memory--object-model)
5. [STL Containers](#stl-containers)
6. [Iterators](#iterators)
7. [Algorithms](#algorithms)
8. [Strings & string_view](#strings--string_view)
9. [Smart Pointers](#smart-pointers)
10. [Utility Types (optional, variant, any, expected, span, mdspan)](#utility-types)
11. [Type Traits](#type-traits)
12. [Ranges (C++20/23)](#ranges)
13. [chrono](#chrono)
14. [random](#random)
15. [filesystem](#filesystem)
16. [Threading & Concurrency](#threading--concurrency)
17. [Formatting & I/O](#formatting--io)
18. [Useful Idioms & Patterns](#useful-idioms--patterns)

---

# 1. Language Basics

## Namespaces

```cpp
namespace myns {
    int x;
}
using myns::x;
using namespace std; // avoid in headers
```

## Functions

```cpp
void f(int);              // declaration
inline int g() { return 1; }
constexpr int h(int x) { return x*x; }
consteval int sq(int x) { return x*x; }
```

## Overloading & Default Args

```cpp
int f(int);
double f(double);
void foo(int x = 3);
```

## Templates

```cpp
template <class T>
T add(T a, T b) { return a + b; }

template <class T>
requires std::integral<T>         // C++20
T incr(T v) { return v + 1; }
```

## Classes

```cpp
struct S {
    int x = 0;
    S() = default;
    S(int x): x(x) {}
    S(const S&) = default;
    S(S&&) = default;
    S& operator=(const S&) = default;
    S& operator=(S&&) = default;
    ~S() = default;
};
```

## Enums

```cpp
enum class Color : uint8_t { Red, Green, Blue };
```

## Lambdas

```cpp
auto f = [a=5](int x) { return a + x; };
auto g = []<class T>(T x) { return x + 1; };
```

---

# 2. Types & Value Categories

## Fundamental types

```
bool, char, wchar_t, char8_t, char16_t, char32_t
signed/unsigned char
short, int, long, long long
float, double, long double
```

## Value Categories

```
prvalue = pure rvalue
xvalue  = expiring value
lvalue  = regular left-value
```

## auto / decltype

```cpp
auto x = 3;          // int
auto& r = x;        // int&

decltype(x) y;       // int
decltype((x)) z = x; // int&
```

---

# 3. Expressions & Operators

### Common operators

```
+, -, *, /, %, +=, -=, *=, /=, %=
&, |, ^, ~, <<, >>
&&, ||, !
==, !=, <, >, <=, >=
?:    // conditional
```

### `constexpr`, `consteval`

```cpp
constexpr int f(int x) { return x*2; }
consteval int g(int x) { return x*2; }
```

### Casts

```cpp
static_cast<double>(x)
dynamic_cast<Derived*>(base)
const_cast<T*>(ptr)
reinterpret_cast<char*>(p)
```

---

# 4. Memory & Object Model

## new / delete

```cpp
int* p = new int(5);
delete p;

int* a = new int[10];
delete[] a;
```

## Placement new

```cpp
char buf[sizeof(int)];
int* p = new(buf) int(42);
p->~int();
```

## Alignment

```cpp
alignas(64) int cacheline;
```

---

# 5. STL Containers

## Sequence Containers

### vector

```cpp
std::vector<int> v;
v.push_back(3);
v.emplace_back(4);
v.size(); v.capacity(); v.reserve(100);
v.data();
```

### string

```cpp
std::string s = "hello";
s += " world";
s.substr(1, 3);
```

### deque

Efficient push/pop at both ends.

### list / forward_list

Node-based, stable iterators.

### array

```cpp
std::array<int, 3> a = {1,2,3};
```

### span (C++20)

```cpp
void f(std::span<int> s) { for(int& x: s) x++; }
```

---

## Associative Containers

### set / multiset

```cpp
std::set<int> s;
s.insert(3);
s.find(3);
```

### map / multimap

```cpp
std::map<std::string, int> m;
m["a"] = 1;
```

---

## Unordered Containers

### unordered_set / unordered_map

```cpp
std::unordered_map<std::string,int> h;
h["x"] = 4;
```

Load factor, bucket_count, rehash.

---

## Container Adapters

### stack, queue, priority_queue

```cpp
std::stack<int> st;
st.push(3);
st.top();
```

---

# 6. Iterators

## Categories

```
Input → Forward → Bidirectional → RandomAccess → Contiguous
```

## Operations

```cpp
++it; it++; --it;
*it; it->member;
it + n; it - n;
```

---

# 7. Algorithms

## Sorting

```cpp
std::sort(v.begin(), v.end());
std::stable_sort(...);
std::partial_sort(...);
std::nth_element(...);
```

## Binary Search

```cpp
std::binary_search(...);
std::lower_bound(...);
std::upper_bound(...);
std::equal_range(...);
```

## Non-modifying

```
all_of, any_of, none_of
for_each
tfind_if, count, mismatch
```

## Modifying

```
copy, move, fill, transform, generate
remove, unique, rotate, shuffle
```

## Set Algorithms

```
set_union, set_intersection, set_difference, set_symmetric_difference
```

## Heap

```
push_heap, pop_heap, make_heap, sort_heap
```

---

# 8. Strings & string_view

## string_view

```cpp
std::string_view sv = "hello";
sv.substr(1);
```

Zero-allocation slicing, lightweight.

## Useful string ops

```
s.find(), rfind(), find_first_of()
substr(), starts_with(), ends_with()
```

---

# 9. Smart Pointers

## unique_ptr

```cpp
std::unique_ptr<Foo> p = std::make_unique<Foo>(3);
```

## shared_ptr / weak_ptr

```cpp
auto sp = std::make_shared<int>(5);
std::weak_ptr<int> wp = sp;
```

## Aliasing constructor

```cpp
auto sp2 = std::shared_ptr<Base>(sp, sp->ptr);   // same control block
```

---

# 10. Utility Types

## optional

```cpp
std::optional<int> x = 3;
if (x) cout << *x;
```

## variant

```cpp
std::variant<int, string> v;
v = 3;
```

## any

```cpp
std::any a = 3;
```

## expected (C++23)

```cpp
std::expected<int, std::string> e = 3;
```

## span & mdspan

```cpp
std::span<int> sp(arr, n);
std::mdspan<int, extents<size_t, 3,3>> mat(ptr);
```

---

# 11. Type Traits

Common traits:

```
is_same, is_integral, is_floating_point
is_enum, is_class
is_trivial, is_pod, is_standard_layout
remove_reference, remove_const
make_signed, make_unsigned
conditional, enable_if
```

Example:

```cpp
static_assert(std::is_same_v<int, int>);
```

---

# 12. Ranges

## Basic Usage

```cpp
#include <ranges>

for (int x : v | std::views::filter(is_even)) {
    ...
}
```

## Common Views

```
filter, transform, reverse, take, drop
keys, values, enumerate (C++23)
```

## Range algorithms

```cpp
std::ranges::sort(vec);
std::ranges::find(vec, 3);
```

---

# 13. chrono

## Durations

```cpp
using namespace std::chrono;
seconds s(3);
milliseconds ms = 500ms;
```

## Clocks

```
system_clock::now()
steady_clock::now()
high_resolution_clock::now()
```

## Formatting

```cpp
std::format("{}", sys_seconds{...});
```

---

# 14. random

```cpp
std::random_device rd;
std::mt19937 gen(rd());
std::uniform_int_distribution<int> dist(1, 10);
int x = dist(gen);
```

---

# 15. filesystem

```cpp
namespace fs = std::filesystem;
fs::exists("a.txt");
fs::directory_iterator(".");
fs::copy("a.txt", "b.txt");
```

---

# 16. Threading & Concurrency

## Basic Threads

```cpp
std::jthread t([]{ ... });    // auto-joining, stoppable
```

## Mutexes

```cpp
std::mutex m;
std::lock_guard lg(m);
```

## Condition Variables

```cpp
std::condition_variable cv;
cv.wait(lk, []{return ready;});
```

## Atomics

```cpp
std::atomic<int> x = 0;
x.fetch_add(1);
```

## Barriers & Latches (C++20)

```cpp
std::barrier sync_point(3);
```

---

# 17. Formatting & I/O

## format (C++20)

```cpp
std::format("Hello {}", name);
```

## I/O Basics

```cpp
std::cout << x;
std::cin >> y;
```

---

# 18. Useful Idioms & Patterns

### RAII

```cpp
struct Guard {
    ~Guard() { cleanup(); }
};
```

### SFINAE

```cpp
template <typename T>
std::enable_if_t<std::is_integral_v<T>> foo(T x);
```

### CRTP

```cpp
template<class D>
struct Base {
    void f() { static_cast<D*>(this)->impl(); }
};
```

### Move-only types

```cpp
struct M {
    M(const M&) = delete;
    M(M&&) = default;
};
```

---

