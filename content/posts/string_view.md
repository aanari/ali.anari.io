---
title: "C++ string_view"
date: 2023-03-14
draft: false
description: "Better C++ string handling"
tags: [cpp, coding]
---

## Overview

When creating a function to take a (constant) string as an argument, you have three main options:

```cpp
// C Convention
void Foo(const char* s);

// Old Standard C++ convention
void Foo(const std::string& s);

// string_view C++17 convention
void Foo(std::string_view s);
```

The problem with the first option, is that you need to use the (efficient but inconvenient) `c_str()` function:

```cpp
void AlreadyHasString(const std::string& s) {
  Foo(s.c_str());
}
```

And with the second option, the syntax is the same however the compiler makes an inefficient temporary string in the process:

```cpp
void AlreadyHasCharStar(const char* s) {
  Foo(s);
}
```

## Solution

To address the issue with `std::string` being expensive to initialize (or copy), C++17 introduced `std::string_view` (which lives in the [<string_view>](https://en.cppreference.com/w/cpp/header/string_view) header). `std::string_view` provides read-only access to an existing string (a C-style string literal, a `std::string`, or a char array) without making a copy. In general, prefer `std::string_view` over `std::string` when you need a read-only string, especially for function parameters. However, returning a `std::string_view` from a function is usually a bad idea. 

Unlike `std::string`, `std::string_view` has full support for constexpr:

```cpp
constexpr std::string_view s{ "Hello, world!" };
```

A `std::string_view` can be created using a `std::string` initializer, and a `std::string` will implicitly convert to a `std::string_view`:

```cpp
#include <iostream>
#include <string>
#include <string_view>

void printSV(std::string_view str)
{
    std::cout << str << '\n';
}

int main()
{
    std::string s{ "Hello, world" };
    std::string_view sv{ s }; // Initialize a std::string_view from a std::string
    std::cout << sv << '\n';

    printSV(s); // implicitly convert a std::string to std::string_view

    return 0;
}
```

Because `std::string` makes a copy of its initializer (which is expensive), C++ won’t allow implicit conversion of a `std::string_view` to a `std::string`. However, we can explicitly create a `std::string` with a `std::string_view` initializer, or we can convert an existing `std::string_view` to a `std::string` using `static_cast`:

```cpp
#include <iostream>
#include <string>
#include <string_view>

void printString(std::string str)
{
    std::cout << str << '\n';
}

int main()
{
  std::string_view sv{ "balloon" };

  std::string str{ sv }; // okay, we can create std::string using std::string_view initializer

  // printString(sv);   // compile error: won't implicitly convert std::string_view to a std::string

  printString(static_cast<std::string>(sv)); // okay, we can explicitly cast a std::string_view to a std::string

  return 0;
}
```

Finally, we can create string literals with type `std::string_view` by using a `sv` suffix after the double-quoted string literal.


```cpp
#include <iostream>
#include <string>      // for std::string
#include <string_view> // for std::string_view

int main()
{
    using namespace std::literals; // easiest way to access the s and sv suffixes

    std::cout << "foo\n";   // no suffix is a C-style string literal
    std::cout << "goo\n"s;  // s suffix is a std::string literal
    std::cout << "moo\n"sv; // sv suffix is a std::string_view literal

    return 0;
};
```
