---
title: Codecademy C++ 练习题
date: 2021-09-23 06:44:50
tags:

---

# CODE CHALLENGE: C++ FUNCTIONS


## Introduction

**1.**

Write a function `introduction()` with no return value that has:

- `std::string` parameter named `first_name`.
- `std::string` parameter named `last_name`.

The function should print the `last_name`, followed by a comma, a space, `first_name` another space, and finally `last_name` again.

For example, `introduction("James", "Bond");` should print the following:

```
Bond, James Bond
```



**Answer** :

```c++
#include <iostream>

// Define introduction() here:
void introduction(std::string first_name, std::string last_name) {

	std::cout << last_name << ", " << first_name << " " << last_name << "\n";

}


int main() {

	introduction("Beyonce", "Knowles");

}
```



## Average

**1.**

Write a function `average()` that takes:

- A `double` parameter named `num1`.
- A `double` parameter named `num2`.

The function should return a `double` that is the average of the arguments passed in.



**Answer** :

```c++
#include <iostream>

// Define average() here:
double average(double num1, double num2) {

	double total = 0;

	total = (num1 + num2) / 2;

	return total;

}

int main() {

	std::cout << average(42.0, 24.0) << "\n";
	std::cout << average(1.0, 2.0) << "\n";

}
```



## Tenth Power

**1.**

Write a function named `tenth_power()` that has:

- An `int` parameter named `num`.

The function should return `num` raised to the 10th power.



**Answer** :

```c++
#include <iostream>
#include <cmath>

// Define tenth_power() here:
int tenth_power(int num) {

	num = num * num * num * num * num * num * num * num * num * num;

	return num;
    //按理说最优解应该为return pow(num, 10); 但此时还没有学到调用其他文件的函数。

}

int main() {

	std::cout << tenth_power(0) << "\n";
	std::cout << tenth_power(1) << "\n";
	std::cout << tenth_power(2) << "\n";

}
```



## First Three Multiples

**1.**

Write a function named `first_three_multiples()` that has:

- An `int` parameter named `num`.

The function should return an `std::vector` of the first three multiples of `num` in ascending order.

For example, `first_three_multiples(7)` should return a vector with `7`, `14`, and `21`.



**Answer** :

```c++
#include <iostream>
#include <vector>

// Define first_three_multiples() here:
std::vector<int> first_three_multiples(int num) {

    return std::vector<int> {num, num * 2, num * 3};

}

int main() {
  
  for (int element : first_three_multiples(8)) {
    std::cout << element << "\n";
  }
  
}
```



## Water Plant

**1.**

Define a function `needs_water()` that accepts:

- An `int` number of `days` since the previous watering.
- A `bool` value `is_succulent`. (A value of `true` would indicate that the plant is a succulent.)

Inside the function, you’ll need some conditional logic:

- If `is_succulent` is `false` and `days` is greater than `3`, return `"Time to water the plant."`.
- If `is_succulent` is `true` and `days` is `12` or less, return `"Don't water the plant!"`.
- If `is_succulent` is `true` and `days` is greater than or equal to `13`, return `"Go ahead and give the plant a little water."`.
- Otherwise, return `"Don't water the plant!"`.

**Note:** Don’t print the strings; return them from the function.



**Answer** :

```c++
#include <iostream>

// Define needs_water() here:
std::string needs_water(int days, bool is_succulent) {
    
  if (days > 3 && is_succulent == false) {
    return "Time to water the plant.";
  } else if (days <= 12 && is_succulent == true) {
    return "Don't water the plant!";
  } else if (days >= 13 && is_succulent == true) {
    return "Go ahead and give the plant a little water.";
  }
    
}

int main() {
  
  std::cout << needs_water(10, false) << "\n";
  
}
```



## Palindrome

**1.**

Define a function `is_palindrome()` that takes:

- An `std::string` parameter `text`.

The function should return:

- `true` if `text` is a palindrome.
- `false` if `text` is not a palindrome.

(A palindrome is any text that has the same characters backwards as it does forwards. For example, “hannah” and “racecar” are palindromes, while “menu” and “ardvark” are not.)

We will not test for edge cases such as capitalization or spaces.



**Answer** :

```c++
#include <iostream>
#include <vector>

//关于获取字符串长度的方法除了用vector还可以用string的length
// Define is_palindrome() here:
bool is_palindrome(std::string text) {
    
  std::string flips;

  for (int i = text.size() - 1; i >= 0; i--) {
    flips = flips + text[i];
  }
  
  return flips == text;
    
}

int main() {
  
  std::cout << is_palindrome("madam") << "\n";
  std::cout << is_palindrome("ada") << "\n";
  std::cout << is_palindrome("lovelace") << "\n";
  
}
```



至此关于函数篇的六个小练习题完结。
