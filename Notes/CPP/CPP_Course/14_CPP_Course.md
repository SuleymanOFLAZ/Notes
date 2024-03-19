#Cpp_Course 
[[00_Course_Files]]

Some rules have been changed about overloading the comparison operators and a new comparison operator is added to the language with the C++20. This operator is the **three-way comparison (or spaceship) operator**: "<=>". #Cpp20

**Should we implement the operator functions as global operator functions or member operator functions?**
The general approach is:
- The symmetric binary operators are implemented as global. The symmetric means here that, if "a > b" is the same operation as " b > a" this is symmetric. Because if we make it a member function, probably we need a global operator function too (Note that, this is because the left operand is used as this pointer). 
- Operators that change the class object are implemented as member operator functions. (For example ++, --, =, += operators)
- Unary operators are implemented as member operator functions.

There are some idiomatic structures for writing these operator functions. We should learn them. 

# Good Practices for Classes
**SOLID Principle**: #SolidPrinciple
Single Responsibility Principle
Open/Closed Principle
Liskov Substitution Principle
Interface Segregation Principle
Dependency Inversion Principle

![[Pasted image 20240312212456.png|800]]

1. Single responsibility principle: A class has to have a single responsibility.
2. The interface of the class has to be cohesive.
3. Large classes should be avoided.
4. Classes should be designed from outside to inside. That means we should first design the interface of the class, then we should design the implementation.
5. Large function codes should be avoided.

# Wrapper Class
#Cpp_WrapperFunction
The **wrapper class** wraps a class and provides it with an interface. For example, it takes a pointer as a member and provides it an interface and probably some functionalities too. For example, the smart pointer class. The class uses the pointer and we make calls to the class's member functions. This is one of the areas in which C++ is very successful. Because wrapper classes have no cost if they are written well and used with the modern tools of the language. There is no difference between using a pointer and a class object with smart pointers. We use the pointer more safely, the making mistakes risks reduced, and writing and reading code become easier.

# Operator Overloading Example
We will create a **wrapper class**.

We will write a class that wraps the int. Why do we do this? For example, the integer overflow is a problem. We can give a guarantee that the class can throw an exception in case of integer overflow. For example, we can create an int object that saves previous values. There can be situations in which we have to use a class object (we can't use the int directly), in these cases we can use the class. For example the Big Integer Class.
```cpp title:mint.h
#include <iosfwd>
#include <cstdlib>

class Mint {
public:
	explicit Mint(int val = 0): mval{val} {}
	friend std::ostream& operator<<(std::ostream&, const Mint&); // Inserter function
	friend std::istream& operator>>(std::istream&, Mint&); // Extractor function

	friend bool operator<(const Mint& lhs, const Mint& rhs) // Implicity inline, global, friend function
	{
		return lhs.mval < rhs.mval;
	}
	friend bool operator==(const Mint&, const Mint&); // Implicity inline, global, friend function
	{
		return lhs.mval == rhs.mval;
	}

	Mint operator+()const
	{
		return *this;
	}
	Mint operator-()const
	{
		return Mint{ -mval };
	}
	
	Mint& operator+=(const Mint& other)
	{
		mval += other.mval;
		return *this;
	}
	Mint& operator-=(const Mint& other)
	{
		mval -= other.mval;
		return *this;
	}
	Mint& operator*=(const Mint& other)
	{
		mval *= other.mval;
		return *this;
	}
	Mint& operator/=(const Mint& other)
	{
		mval /= other.mval;
		return *this;
	}
	Mint& operator%=(const Mint& other)
	{
		mval %= other.mval;
		return *this;
	}

	Mint& operator++();
	Mint operator++(int);
	Mint& operator--();
	Mint operator--(int);

	static Mint random()
	{
		return Mint{std::rand()};
	}
		
private:
	int mval;
};

inline Mint operator+(const Mint& lhs, const Mint& rhs) // We can make these functions friend
{
	return Mint{lhs} += rhs;
	/* Same as:
		Mint result{lhs};
		result += rhs;
		return result;
	*/
}
inline Mint operator-(const Mint& lhs, const Mint& rhs)
{
	return Mint{lhs} -= rhs;
}
inline Mint operator*(const Mint& lhs, const Mint& rhs)
{
	return Mint{lhs} *= rhs;
}
inline Mint operator/(const Mint& lhs, const Mint& rhs)
{
	return Mint{lhs} /= rhs;
}
inline Mint operator%(const Mint& lhs, const Mint& rhs)
{
	return Mint{lhs} %= rhs;
}

inline bool operator!=(const Mint& lhs, const Mint& rhs)
{
	return !(lhs == rhs);
}
inline bool operator>(const Mint& lhs, const Mint& rhs)
{
	return rhs < lhs;
}
inline bool operator>=(const Mint& lhs, const Mint& rhs)
{
	return !(lhs < rhs);
}
inline bool operator<=(const Mint& lhs, const Mint& rhs)
{
	return !(rhs < lhs);
}

inline Mint& operator++()
{
	++mval;
	return *this;
}
inline Mint operator++(int)
{
	Mint result{*this};
	++ *this; // Or we can write: "operator++()". Because the pre-increment function and the post-increment functions are non-static member functions
	return result;
}
inline Mint& operator--()
{
	--mval;
	return *this;
}
inline Mint operator--(int)
{
	Mint result{*this};
	-- *this;
	return result;
}
```
```cpp title:mint.cpp
#include "mint.h"
#include <ostream>
#include <istream>
#include <fsrteam>

std::ostream& operator<<(std::ostream& os, const Mint& x)
{
	return os << '(' << x.mval << ')'; // Chaining here // "x.mval" is valid because the functions is a friend.
}

std::istream& operator>>(std::istream& is, Mint& x)
{
	return is >> x.mval;
}


```
```cpp title:main.cpp
#include "mint.h"
#include <iostream>

int main()
{
	Mint m1, m2;

	std::cout << "Enter two numbres: ";
	std::cin >> m1 >> m2;
	std::cout << m1 << " " << m2;

	std::ofstream ofs{"text.txt"};

	for(int i = 0; i < 10; ++i)
	{
		ofs << Mint{i} << " "; // The operator<< functions serves other objects too (an ostream class object and an ofstream class object) because the inheritance is used here.
	}

	std::cout << Mint{12} * Mint{3} + Mint{90};

	auto x = Mint::random();
}




```

> [!note] Note: We could make this library a header-only library. The inline functions and inline variables (C++17).

> [!question] Question: Should we use the constructor with the default argument or have a separate default constructor? (In case we have one constructor that takes one parameter) #Cpp_DefaultConstructor #Cpp_DefaultArgument 
> A: There is no clear answer to that question. However, using the constructor with the default argument is a generic choice.

> [!note] Note: Making the conversion constructor explicit is a good practice if we don't have a specific design choice for the conversion constructor. #Cpp_ConversionConstructor 

> [!note] Note: Writing an inserter function instead of the classical "void print()const" member functions is a good practice. This requires the operator overloading. #Cpp_OperatorOverloading 

> [!note] Note: We should write the operator functions as global for symmetric binary operators.

> [!important] Important: If we overload the arithmetic operators, we should overload the compound assignment operators.

> [!important] Important: The common practice is the arithmetic operator functions call the compound assignment operators. #Cpp_Interview_Topic 

> [!important] Important: The general approach is if the operator function is changed the object, makes it a member function.

> [!important] Important: The common practice is implementing the sign operator functions as member functions.

> [!important] Important: The common practice is the not equal operator ("!=") function uses the equal operator ("\==") function. The other comparison operator functions use the "<" operator. Therefore if two of these operators ("\==" and "<") are declared as friends, there is no need to make the other ones friends (because they call these two functions). Or all 6 comparison operators can be declared as global.
## Writing an inserter function
The "cout" is a class object and controls an output stream. The type of the cout is ostream (a class). The other classes (types) are created from the ostream class using the **inheritance** mechanism. For example, a class writing a file or a memory is inherited from the ostream class.

Inserter functions (functions that overload the << operator) can be used to write to any output stream.
```cpp
#include <fstream>

int main()
{
	using namespace std;

	ofstream ofs{"text,txt"};
	
	for(int i = 0; i < 100; ++i)
	{
		ofs << i << "\n"; // Writes to the file
	}
}
```
The same function (inserter function - operator<<) gives the output to the file if we use it with the ofs object.
 
The functions that provide this are called **inserter functions**. How do we write these functions? The "<<" is a binary operator. We write "ofs << i" to use it. The function has to be the member function of the left operand or a global function. However, we didn't write the class. Therefore, it has to be a global operator function.

This is a global function however if we make a friend declaration for the function, we can define it in the class definition (our class) and it can access the private members of our class. If we don't want to make the inserter function friend, we should define it in the namespace. #Cpp_FriendDeclaration 
```cpp hl:operator title:"The insertor function"
#include <iosfwd>

class Mint {
public:
	explicit Mint(int = 0);
	friend std::ostream& operator<<(std::ostream&, const Mint&); // The inserter function
};

// std::ostream& operator<<(std::ostream&, const Mint&); // Or it can be defined as global
```
 The return type of the inserter function is "std::ostream&" due to chaining. #Cpp_Chaining
### The iosfwd
#Cpp_HeaderFile_iosfwd
 Should the definition of the ostream class also be visible? Actually not. Because we didn't write the definition of the function. The iostream header is heavy. For that reason there is another header file that contains the forward declaration of the iostream. The header is "**iosfwd**". Because we only write the decleration of the function in the header file, we should include the iosfwd header. If we were define the function in the header file we would have to include the iostream header file.
### How do we write the extractor function?
The input stream class is "istream". Because the extractor function changes the object, we don't make the parameter const.
```cpp hl:operator>> title:"The extractor function"
#include <iosfwd>

class Mint {
public:
	explicit Mint(int = 0);
	friend std::ostream& operator<<(std::ostream&, const Mint&);
	friend std::istream& operator>>(std::istream&, Mint&); // Extractor function
};
```

> [!important] Important: If we want the inserter function and the extractor function to access the private section of the our class, we have to define it inside the class definition as a friend function. If we don't need to make the operator functions friends, we should define them as global functions.
## Comparison operators (Before C++20)
First, we look at the before C++20 comparison operators. C++20 has brought major changes to the comparison operators. We will look at the C++20 changes in the comparison operators later. #Cpp20 

There are 6 comparison operators (before the C++20). 
- <
- <=
- >
- >=
- ==
- !=

In the use of the standard library, the main comparison operators "<" and "\==" are accepted. Therefore first we should at these operators to our class if we want our class object to be the operand of the comparison operators.

We can separate the classes into three categories when we look at it from this perspective (In terms of comparison operators).
- The classes that don't give an interface of comparison operators. They can't be the operand of the comparison operators. Because there is no meaning to it in the problem domain.
- There are **equality operators** ("\==" and "!=") but, there are no **ordering operators** ("<", "<=", ">", ">="). So we can be questioning if the class objects are equal or not equal, however, we can't be questioning if is an object greater than the other, etc.
- There are equality and ordering. We can compare class objects in all manner.

> [!important] Important: Don't make using-declarations inside the header file. Because files that include the header file also include the using-declaration. #Cpp_Interview_Topic 


# Operator[ ]
There are two **dereferencing operators** these are "[ ]" and "\*". The "[]" is a pointer pointer operator. The "x\[y]" actually means "\*(x+y)". 

The "\n" operator is called the "**subscript operator**" or the "**array index operator**".

The subscript operator can't be overloaded as global. It has to be a member operator function.

> [!question] Question: Is there a problem with the below code? #Cpp_Interview_Question 
> A: No, because the "i\[a]" is equal to "a\[i]". Because the "i\[a]" equal to "\*(i + a)" and the "a\[i]" equal to "\*(a + i)"

```c
int main()
{
	int a[]{1, 2, 3, 4, 5};

	for (int i = 0; i < 5; ++i){
		printf("%d\n". i[a]); // printf("%d\n". *(i+a));
	}
}
```

We use the subscript operator to access an element of an array. You can either make the name of an array the operand or directly use a pointer variable as an operand.

```cpp
int main()
{
	int a[]{1, 2, 3, 4, 5};
	int *p{a};

	p[2]
}
```

- ? Overloading the subscript operator with which type of class makes the job of the client code easy? (intuitive)
- **array-like classes**: Classes that represent an array. For example, the standard library has such a class called "array". Or a dynamic array class which is the "vector" class in the standard library. The "string" class is also a dynamic array.  And also the "deque" and "map" classes. These are the classes that overload the square brackets operator.  #Cpp_StandardLibrary_Array #Cpp_StandardLibrary_Vector
	- std::array
	- std::vector
	- std::string
	- std::deque
	- std::map
- **pointer-like classes**: Some classes are used like pointers even if they are not pointers. These types of classes can be divided into two categories.
	- 1: The classes that are used to hold locations in the containers. These are called "**iterators**".
	- 2: The classes that are used to control the life of the objects that have dynamic lives. These are called "**smart pointers**".

> [!note] Note: The std::array class is a wrapper class that wraps the array. #Cpp_StandardLibrary_Array 

```cpp title:"The array class example"
#include <iostream>
#include <array>

int main()
{
	std::array ar{1, 4, 5, 7};
	std::cout << ar[2] << "\n";
	std::cout << ar.operator[](2) << "\n";
	++ar[3];
	++ar.operator[](3);
}
```

```cpp title:"The vector and string example"
#include <iostream>
#include <vector>
#include <string>

int main()
{
	using namespace std;

	vector<string> vec01{"Programming", "Language"};
	for(size_t i{}, i < vec01.size(); ++i)
	{
		vec01[i] += " "; // The operator[] function of the vector is called. Then the operator+= function of the string is called.
	}
}
```

The "a\[b]" expression is an L-value expression. Therefore the "operator\[]" function has to return an L-value reference to make the expression an L-value expression.

```cpp
class Myclass {
public:
	int& operator[](size_t idx)
	{
		return mptr[idx];
	}
private:
	int* mptr;
};
```
The operator\[] function takes the "size_t" type as typically. It should be an integer value. And returns a reference to the object that is accessed with the index. For example, if the class is representing an int array, the operator function should return int&.

## The Relation Between The operator\[] Function and The Const Overloading
#Cpp_ConstOverloading #Cpp_OperatorFunction_SquareBracket
Let's make a quick reminder about the const overloading.
```cpp
class Myclass {
public:
	void func();
	void func()const; // Const overloading
};

int main()
{
	Myclass m;
	Myclass cm;

	m.func(); // The non-const function will be called.
	cm.func(); // The const function will be called.
}
```

```cpp error:cs[1]
#include <string>

using namespace std;

int main()
{
	string s{"Hello"};
	const string cs{"World"};

	s[1] = 'e';
	cs[1] = 'e'; // Syntax ERROR.
}
```

> [!question] Question: How does it make a syntax error when we try to change the const object with the \[] operator?
> A: There are actually two operator\[] functions of the class. There is a const overloading. The return type of one function is "char&" and the other one is "const char&". Therefore, when we make a call with a const object, it is a syntax error because the function returns a const reference.

Line 14 in the below code is a syntax error because only a const member function can be called for a const class object. This is normal in terms of syntax. However, line 15 is a syntax error too, and this doesn't make sense in terms of syntax.
```cpp error:a[4]
#include <initializer_list>

class Array {
public:
	Array(int);
	Array(std::initializer_list<int>);
	int& operator[](std::size_t);
};

int main()
{
	const Array ca{1, 2, 3, 4, 5};

	ca[4] = 20; // Syntax ERROR. OK, this makes sense in terms of syntax.
	int x = ca[4]; // Syntax ERROR. NOT OK, this doesn't make sense in terms of syntax.
}
```

We need such a structure that we should be able to access the members of the const class object or non-const class object with the subscript operator. However, we should be able to set the members of the non-const class object and we shouldn't be able to set the members of the const class object. 

The solution is overloading (const overloading) the subscript operator function. This solves the problem because a const overloaded function exists now. The compiler can call the const member function for the const objects. We make the return of the const member function const so assigning to a const object will be a syntax error as expected in terms of the syntax.
```cpp hl:"const int","40"
class Array {
public:
	Array(int);
	Array(std::initializer_list<int>);
	int& operator[](std::size_t);
	const int& operator[](std::site_t)const; // The const overload function.
};

int main()
{
	const Array ca{1, 2, 3, 4, 5};
	Array a{1, 2, 3, 4, 5};

	auto val = ca[3]; // OK as expected in terms of syntax,
	ca[3] = 40; // Syntax ERROR. Because the const overloaded function returns a const value. OK in terms of syntax.

	auto i = a[2]; // OK as expected in terms of syntax,
	++a[2]; // OK as expected in terms of syntax,
}
```

This technique is frequently used in standard libraries too. A standard library example. The "front" and "back" functions of the vector class use the same technique as the subscript operator function.
```cpp
#include <vector>

int main()
{
	using namespace std;

	const vector<int> ivec{1, 2, 3, 4, 5};

	ivec[2] = 5; // Syntax error and ok in terms of syntax. The mentioned technique is used here.
	ivec.back() = 22; // Syntax error and ok in terms of syntax. The mentioned technique is used here.
}
```

## Implementing the operator\[]
```cpp
#include <iostream>

class IntArray {
public:
	IntArray(std::size_t n) : msize{n}, mp{new int[n] {} }
	{
	}
	~IntArray()
	{
		delete[]mp;
	}
	// special member functions etc ...

	int& operator[](std::size_t idx)
	{
		return mp[idx];
	}
	const int& operator[](std::size_t idx)const
	{
		return mp[idx];
	}
	/// ...
	std::size_t size()const
	{
		return msize;
	}
private:
	int* mp;
	std::size_t msize;
}

int main()
{
	IntArray a(20);

	a[5] = 6;
	a[3] = 2;

	++a[1];
	--a[19];
	
	for(int i = 0; a < a.size(); ++i)
	{
		std::cout << a[i] << " ";
	}
}
```
# Implementation Specified
Holding the same string literals in one array or in different arrays depends on the compiler. This is the **implementation specified**. The implementation specified is different from the implementation defined. The compiler doesn't have to document the implementation of specified situations and produce the same code.

```cpp
#include <iostream>

int main()
{
	const char *p1 = "\n";
	const char *p2 = "\n";

	if (p1 == p2) // This is the implentation specified. (C and C++)
		std::cout << "Same\n";
	else
		std::cout << "Not same\n";
}
```

# Manipulator Functions
## About the endl
The "endl" is a function. It first writes an '\n' character to the output stream and then it flashes the output buffer.

> [!question] Question: How does the below code work if the "endl" is a function? #Cpp_Interview_Question 
> A: The function name is converted to the function pointer if the function's name is used in an expression. This is called "**function to pointer conversion**". The ostream class has a non- static member operator<< function that takes a function parameter that the type is endl function. Therefore when we write "cout << endl", the call "operator<<(endl)" happens. Then the operator function that takes the function address of the endl function calls the endl function for the "\*this". The "\*this" is the cout object here.

```cpp title:Question
#include <iostream>
using namespace std;

int main()
{
	cout << 123 << endl << 234;
}
```

The ostream is a class template. There is an inheritance mechanism here. The osteam class is inherited from the "**ios_base**" class.
```cpp
#include <iostream>
using namespace std;

// The representation of a part of the stream class
class ostream {
public:
	operator& operator<<(int);
	operator& operator<<(double);
	operator& operator<<(void*);
	// ...
	operator& operator<<(ostream& (*fp)(ostream&))
	{
		return fp(*this);
	}
	
};

// The representation of the endl function
ostream& endl(ostream& os)
{
	os.put('\n');
	os.flush();
	return *this;
}
```

> [!note] Note: Manipulator Function
> The functions that take a ostream object as a reference and, return the object that they took as a reference and manipulate the object that they took are called "**ostream manipulators**". And the functions that make these kinds of manipulations are called manipulators.

## Writing a Manipulator Function
> [!question] Question: Can we write a manipulator function such as the endl?
> A: Yes we can write our manipulator functions.

Our manipulator function has to take "std::ostream&" type and return "std::ostream&" type. Our manipulator, function has to take the "std::ostream&" type as a parameter because the ostream class has a non-static member "operator<<(std::ostream& (\*pf)(std::ostream&) )" function. And the return of our manipulator function has to be "std::ostream&" because of the chaining.

```cpp
#include <iostream>

using namespace std;

std::ostream& sline(std::ostream& os)
{
	return os << "\n*****************************************\n";
}

int main()
{
	int x = 4;
	double d = 5.6;
	cout << x << sline << d << sline;
}
```

---
# Terms
- wrapper class
- smart pointer class
- inserter functions
- compound assignment operators
- equality operators
- ordering operators
- dereferencing operators
- array-like classes
- pointer-like classes
- ios_base
- function to pointer conversion
- Manipulator
- ostream manipulator
- smart pointers 
- iterators

---
Return: [[00_Course_Files]]