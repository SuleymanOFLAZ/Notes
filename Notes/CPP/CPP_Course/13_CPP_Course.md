#Cpp_Course 
[[00_Course_Files]]

# Friend Declarations
#Cpp_FriendDeclaration
**Friend declarations** are used to give some codes access privileges to the private section of the class. 

```cpp title:fighter.h
class Fighter {
public:
	void kill();
	void attack();
};

class Weapon {

};

bool operator== (const Fighter&, const Fighter&);
```

The public interface of the class doesn't only consist of the members that are declared in the public section of the class, some functions too that are declared in the header file are included in the public interface of the class. Thus, the interface of the class is the items that are declared in the public section of the class definition and some global functions. 

Even sometimes, other classes that are declared in the header file or a separate header file can be the interface of the class. For example, the "Weapon" class is used only related to the "Fighter" class. Sometimes, we want to make available these kinds of classes (the "Weapon" class) to the client codes but, sometimes we only want to use it in the implementation. Sometimes we make these kinds of classes nested types, sometimes we make them stand-alone.

The classes or global functions that we mentioned can't access the private section of the class but they have to. This is allowed with a special declaration.

This can create a bad perception at first glance because it breaks the principle of object-oriented programming. However, it actually does not break because we give these privileges to only the implementation codes, not the client codes.

## How do we make a friend declaration?
There are three types of friend declarations. These are:
- to giving friend privilege to a global function (a function that is declared inside the namespace). #Cpp_NameSpace 
- to giving friend privilege to a member function of a class. #Cpp_memberFunctions 
- to giving friend privilege to a class.

## 1. Declaring a global function as friend
```cpp error:18,19 valid:11,12
class Myclass {
private:
	friend void foo(int); // Friend declaration of a global function
	void func();
	int mx;
};

void foo(int)
{
	Myclass m;
	m.mx = 10;// OK. The function "foo" is declared as a friend inside the class definition.
	m.func(); // OK. The function "foo" is declared as a friend inside the class definition.
}

void g()
{
	Myclass m;
	m.mx = 10;// Syntax ERROR. The function "g" is not a friend of the class "Myclass".
	m.func(); // Syntax ERROR. The function "g" is not a friend of the class "Myclass".
}
```

The privileges are given to the function that has the name "foo" and returns void and takes an int parameter. First, the function foo is searched in the namespace that the class is in.

```cpp error:foo(12)
class Myclass {
private:
	friend void foo(int); // Friend declaration of a global function
	void func();
	int mx;
};

int main()
{
	foo(12); // Name-lookup error. The declarations of the function don't exist.
}
```

> [!warning]  Warning: Just the friend declaration doesn't make the name visible at the scope. Look at the upper example. But there is an exception about it that is called Argument Dependent Lookup - ADL. We will look at it in detail later. For now, look at the below example for ADL. #Cpp_ADL

```cpp valid:foo(m)
class Myclass {
private:
	friend void foo(int); // Friend declaration of a global function
	void func();
	int mx;
};

int main()
{
	Myclass m;
	foo(m); // OK due to ADL.
}
```

> [!note] Note: It is not important which section of the class definition (private, public, or protected) the friend declaration is made.

> [!warning]  Warning: The friend declaration doesn't make the item a member of the class. 

## 2. Declaring a member function as friend
```cpp
class Myclass {
private:
	friend void Data::foo(int); // Friend declaration of a global function. If the name Data is not visible at that point, this is a syntax error.
	void func();
	int mx;
};
```

We make the "foo" function (that has no return and parameter) of the "Data" class friend. This declaration will be valid if the "Data" name is visible, so the upper code is a syntax error if the name "Data" is not visible at that point.

```cpp error:23 valid:17
class Data{
public:
	void foo();
	void bar();
};

class Myclass {
private:
	friend void Data::foo(int); // Friend declaration of a global function. OK, the name "Data" is visible at that point.
	void func();
	int mx;
};

void Data::foo()
{
	Myclass m;
	m.func(); // OK
}

void Data::bar()
{
	Myclass m;
	m.func(); // Syntax Error. The bar function is not a friend.
}
```

```cpp
class Data{
public:
	Data();
};

class Myclass {
private:
	friend Data::Data(); // OK. We can declare a special member function of another class as friend.
};
```

> [!note] Note:  We can declare special member functions and other constructors that are not a special member function of the other classes as friends.

## 3. Declaring a class as friend
```cpp
class Myclass {
private:
	int mx;
	void foo();
	friend class Data; // OK. It is valid even if the definition of the "Data" is not visible at that point.
};

class Data {
	 void fdata()
	 {
		 Myclass m;
		 m.mx = 10; // OK
		 m.foo(); // OK
	 }
};
```

If we give a friendship to a member function of the class, the class has to be a complete type. However, the rules are different here. The class doesn't have to be a complete type to give it a friendship.

> [!note] Note: While some other languages such as Java and C# don't have global functions, why does C++ have global functions (also called free functions or stand-alone functions)? If the global functions didn't exist in C++, it would be hard to make compatible this with the other tools of the language.

# Operator Overloading
#Cpp_OperatorOverloading #Cpp_OperatorFunctions
When an object is an operand of an operator, the compiler turns the expression into a function call. This is called "**operator overloading**". The functions called via this method are called "**operator functions**" and these functions are subjected to special rules.

There are 2 ways to call functions in C++. Some functions are called via their name. However, some functions are also called by making it an operand of an operator. To make this possible the class has to define a function that makes this operation.

> [!question] Question: Why there is operator overloading? What is its purpose? Does it have a run-time cost? #Cpp_Interview_Question 
> The operator overloading exists because we want to use the operators on the class objects. It makes it easier to write the client codes. Provides a higher level of perception and abstraction. There is no run-time cost of it.

## Rules for Operator Overloading
1. Operator overloading is a tool that is created for class types and enumeration types.

2. If the operand of a unary operator is an arithmetic type or if the two operands of a binary operator are an arithmetic type, we can't use the operator overloading.
```cpp
int main()
{
	int x{2}, y{12};
	x + y; // We can't overload the + operator for this example. Because the operands are arithmetic types.
}
```

3. Functions that are used for operator overloading (operator functions) have to be a global function or non-static member function. It can't be a static member function. If we use a global function, it is called a "**global operator function**", if we use a non-static member function, it is called a "**member operator function**".

4. Some operators can't be overloaded as global functions. They have to be overloaded as non-static member functions.
- Index operator ( [ ] )
- Function call operator ( () )
- Arrow operator ( -> )
- typecast operators
- special member functions (For example, operator=)

5. Every operator can't be overloaded. The operators that can't be overloaded are:
- scope resolution operator (::)
- member selection operator (.)
- sizeof  operator
- operator ".\*"  (This operator doesn't exist in C. We will look at it later)
- operator "? :"
- typeid

6. The name of the operator functions can't be chosen arbitrarily. The "**operator**" is a keyword and we have to use it in the name of the operator function before the operator token that we want to overload. #Cpp_keyword_operator

7. Operator functions can be called by their names. We don't have to call them with the operator notation always. There are some places that we have to call the operator functions by their name. We will look at these cases later.

```cpp
#include <bitset>
#include <iostream>

int main()
{
	using namesapce std;

	bitset<16> bs1(12u);
	bitset<16> bs1(56u);

	operator&(bs1, bs2); // Calling the operator function by its name

	int ival{2};
	cout << ival; // Calling the operator function by operator notation
	cout.operator<<(ival); // Calling the operator function by its name
	operator<<(std::cout, "Hello world"); // Calling the operator function by its name
}
```

8. The operator functions can't take a default argument except the function call operator function.

```cpp
class Myclass;

Myclass operator+(Myclass, int x = 10); // Syntax ERROR. An operator function can't take a default argument (except the function call operator function)
```

9. A non-existing operator can't be overloaded. That means we can't create an operator.

10. We can't change the **arity** of the operators. Arity of an operator means if it is unary or binary. So we have to overload an operator as binary if the operator is a binary operator.

Logical not "!" is an unary operator. There are two possibilities, it can be a member operator function or a global operator function. 

It is called for the object (instance) when it is a member-operator function. The compiler turns it into "x.operator!()" when it is "!x". Because the function is called for the object that is the left operand of the dot operator, the function can't have a parameter. It is a syntax error if we add a parameter to the operator function.
```cpp error:operator!(int) valid:operator!()
class Myclass {
public:
	bool operator!(Myclass); // Syntax ERROR: too many parameters for this operator function, unary "operator!" has too many parameters
	bool operator!(); // OK
};
```

If we overload the logical "!" as a global operator function, the function has to take the object as a parameter. The compiler turns "!x" into "operator!(x)".

The result that is concluded:
- The unary operator functions have no parameters as they are overloaded as a member operator function.
- The unary operator functions take one parameter as they are overloaded as a global operator function.

The operator% is a binary operator. 

If it is overloaded as a member operator function, the compiler takes the left object as \*this. So, the compiler turns "a % b" into "a.operator%(b)". So, it has to take one parameter.

If it is overloaded as a global operator function, the function needs two parameters. So, the compiler turns "a % b" into "operator%(a, b)". So, it has to take two parameters.

The result that is concluded:
- The binary operator functions take one parameter as they are overloaded as a member operator function.
- The binary operator functions take two parameters as they are overloaded as a global operator function.

> [!warning]  Warning: The difference between the plus sign operator and the addition operator is the parameter counts of them. Because the plus sign operator is a unary operator and the addition operator is a binary operator. So be careful when overloading in this kind of situation.
> There are 4 different tokens that are both binary and unary. These tokens are:
> - \+ : "+x" sign operator, "a + b" addition operator
> - \- : "-x" sign operator, "a - b" subtraction operator
> - \* : "\*p" dereferencing operator, "x \* y" multiplication operator
> - & : "&x" address of operator, "x & y" bitwise and operator
> The compiler determines which operator is overloaded by looking at the parameter count when we overload one of these operators.

```cpp
class Myclass {
public:
	Myclass operator+(Myclass); // addition operator
	Myclass operator+(); // Sign operator
	Myclass operator-(Myclass); // subtraction operator
	Myclass operator-(); // Sign operator
};
```

```cpp
class Myclass {
public:
	Myclass& operator*(); // Dereference operator (member operator function)
	Myclass& operator*(Myclass); // Multiplication operator (member operator function)
};

Myclass& operator*(Myclass); // Dereference operator (global operator function)
Myclass& operator*(Myclass, Myclass); // Multiplication operator (global operator function)
```

11. There are two increment operators, post-increment and pre-increment, and there are differences between them. The values that the post-increment and pre-increment operators are different. The value categories of them are different. The pre-increment operator is an L-value expression and the post-increment operator are PR-value expression. The value the pre-increment operator produces is one more of the object and the value the post-increment operator produces is the value of the object. It is obvious the operator functions of these operators are different. The same situation is exist with the decrement operators.

A rule is used to make the distinction between these pre-increment and post-increment functions. A dummy parameter is added to the post-increment operator function to distinguish it from the pre-increment operator. This dummy parameter isn't used.

```cpp
class Myclass {
public:
	Myclass& operator++(); // pre-increment operator (member operator function)
	Myclass operator++(int); // post-increment operator (member operator function)
};

Myclass& operator++(Myclass); // pre-increment operator (global operator function)
Myclass operator++(Myclass, int); // post-increment operator (global operator function)
```

12. We cannot change the priority rules and associativity rules that are defined for the operators with the operator overloading mechanism.

```cpp
class Myclass {
public:
	Myclass operator>>(int);
	Myclass operator*(const Myclass&);
	Myclass operator+(const Myclass&);
	bool operator>(const Myclass&);
};

int main()
{
	Myclass n1, n2, n3, n4, n5;

	// n5 = n1 + n2 * n3 > n4 >> 5
	// n5 = (n1 + (n2 * n3)) > (n4 >> 5)

	n5.operator=(n1.operator+(n2.operator*(n3)).operator>(n4.operator>>(5)));
}
```

```cpp
class Myclass {
public:
};

Myclass operator>>(const Myclass&, int);
Myclass operator*(const Myclass&, const Myclass&);
Myclass operator+(const Myclass&, const Myclass&);
bool operator>(const Myclass&, const Myclass&);

int main()
{
	Myclass n1, n2, n3, n4, n5;

	// n1 + n2 * n3 > n4 >> 5
	// n1 + (n2 * n3)) > (n4 >> 5)

	operator>(operator+(n1, operator*(n2, n3)), operator>>(n4, 5))
}
```

13. Operator functions can be overloaded #Cpp_FunctionOverloading 

```cpp
class Myclass {
public:
	Myclass operator+(int);
	Myclass operator+(double);
	Myclass operator+(Myclass);
};

int main()
{
	Myclass n1, n2, n3;
	(n1 + 5) + (n2 + 3.4)
}
```

```cpp
class Myclass {
public:
	Myclass& operator=(const Myclass&); // Special member functions, copy assignment
	Myclass& operator=(Myclass&&); // Special member functions, move assignment
	Myclass& operator=(int); // Assignment operator function
	Myclass& operator=(double); // Assignment operator function
};
```
### Three-way comparison operator
#Cpp_Operator_ThreeWayComparison
With C++20, some comprehensive changes are made. There are 6 comparison operators in C and there were 6 in C++. Another comparison operator called the **three-way comparison operator** "<=>" (or called **spaceship comparison operator**) was added to the language with C++20. We mentioned that the binary member operator functions are called for the left operand (by using this pointer of the left operand). But, this has an exception for the three-way operator. The three-way operator function can be called for the right operand.  #Cpp20

## Return Types of the Operator Functions
There are no language rules for the return types of the operator functions. However, the operators itself are make require some return types.

For example, if we use the ">" operator with primitive types, the return value of the functions would be the bool type. Therefore it is more convenient and intuitive to make the return type of the operator> function bool when we overload it for the class types. The compiler doesn't care if we make the return type different. However, this wouldn't be intuitive.

```cpp
class Myclass {
public:
	double operator>(Myclass) const; // Valid but not intuitive
};
```

If we make the addition of the two matrices it makes sense that the result is a matrix. Thus the operator+ (addition operator) function of the Matrix class should return a matrix.
```cpp
class Matrix {
public:
	Matrix operator+(const Matrix&) const;
};
```

```cpp
class Date {
public:
	Date operator+(int n); // Adds the integer value to the Date and return the Date
	int operator-(const Date&)const; // Returns the day diff between dates
	Date operator-(int n); // Subtracts the integer value from the Date and return the Date
};

int main()
{
	Date today, past_date;

	// today - 20
	// today -past-date
	// today + 20
}
```

In conclusion, the determining factor of the return type of the operator functions is the problem domain, not the language rules.
### Should the return type of the operator function be the "T" or "T&"?
This is related to the value category of the expression.

For example, the value category of the expression "x + y" should be the PR value. Therefore, the return type of the operator+ function should be the "T".
```cpp
class Matrix {
public:
	Matrix operator+(const Matrix&) const;
};
```

However, the expressions that are created with the assignment operator are L-value expressions. Therefore, the return type of the operator= should be the "T&".
```cpp
class Matrix {
public:
	Matrix operator+(const Matrix&) const;
	Matrix& operator=(const Matrix&);
};
```

The expression of the "++x" is an L-value reference, yet the expression of the "X++" is a PR value expression. 
```cpp
class Myclass {
public:
	Myclass& operator++();
	Myclass operator++(int);
};
```

We use the "a\[5\]" syntax at the left side of the assignment operator like "a\[5\] = 10;", therefore the expression "a\[5\]" is an L-value expression. So the return type of the operator\[\] function should be the "T&".
```cpp
class Array {
public:
	Array(int);
	int& operator[](int);
};

int main()
{
	Array a(10);

	a[5] = 20;
	a.operator[](5) = 20;
}
```
## Operator Functions and Const Semantic
Does the expression "i1 + i2" have a side effect? Does a change in the operands do? No. Thus the operator function should access the i1 and i2 objects for reading them. Therefore, the operator+ function should take these as a const argument.
```cpp
class Matrix {
public:
	Matrix operator+(const Matrix&)const;
};

Matrix operator+(const Matrix&, const Matrix&);
```

The below example is a syntax error because the operator+ is not a const member function, therefore we cannot call it for the const objects.
```cpp hl:3 error:11
class Matrix {
public:
	Matrix operator+(const Matrix&); // Not a const member function
};

int main()
{
	const Matrix m1;
	const Matrix m2;

	m1 + m2; // Syntax Error. Because the call is m1.operator+(m2) we cannot call a non-const function for a const member.
}
```

Not making the parameters const is semantically wrong and if we did make a call to it with a const object it would be a syntax error.

## Some Examples
```cpp
class Matrix {
public:
	Matrix operator+()const; // Sign operator
};

Matrix operator+(const Matrix&);
```

```cpp
class Count {
public:
	bool operator!()const;
};

bool operator!(const Count&);
```

```cpp
class Matrix {
public:
	Matrix& operator+=(const Matrix&);
};

int main()
{
	Matrix m1, m2;
	m1 += m2;
}
```

## Why do we need the global operator functions?
> [!question] Question: Why do global operator functions exist? Why do we need them instead of just using the member-operator functions? #Cpp_Interview_Question 
> A1: Consider the "b" as a class type object and the "ival" as a primitive integer type object. We can write a member operator function for the expression "b + ival", but we cannot write a member operator function for the expression "ival + b" because the member operator function is always called for the first operand. So the answer is: the existence of the global operator functions is an obligation to make the binary operators symmetric in case one of the operands is an arithmetic type and the other is a class type.
> A2: The "cout" is a class object of the standard library and we can write "cout << ival;". The "Counter" is a class that we are defined and the "mycounter" is and object of type "Counter. How could we write "cout << mycounter"? The "iostream" class can't have a member operator function that takes "Counter" type as a parameter. We don't write the class "ostream". The global operator functions are a need here. Therefore, if the operands of the operator function are different class types, it isn't preferred to make it a member-operator function. These functions are all global operator functions in the standard library.

```cpp
// A1:
operator+(ival, b);

// A2:
std::ostream& operator<<(std::ostream&, const Counter&);
```

```cpp
#include <iostream>
#include <string>

int main()
{
	using namespace std;

	string str{"Hello"};

	cout << str;
	// We make a call like: operator<<(cout, str). The global operator function is provided by the string library:  std::ostream& operator<<(std::ostream&, const std::string&)
}
```

## The operator overloading requires the reference semantic
Consider to write "&m1 + &m2". Is it convenient? Or consider to write "\*a\[5\]". Therefore, the reference semantic was needed to make the operator overloading meaningful. #Cpp_ReferenceSemantic 

---
# Terms
- friend declaration
- operator overloading
- operator functions
- global operator function
- member operator function
- operator keyword
- three-way comparison operator - spaceship comparison operator

---
Return: [[00_Course_Files]]

