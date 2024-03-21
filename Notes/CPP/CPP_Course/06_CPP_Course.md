[[00_Course_Files]]
#Cpp_Course 
# C and C++ Function Overload Disagreement - Extern C Declaration
#Cpp_externCdeclaration #Cpp_C_difference 
**What is function overload disagreement between C and C++?**
There is no function overloading in C, but there is in C++. This brings some problems with the linker.

Compilers write an external reference to the linker for function calls if the function call is not inlined (If the compiler doesn't do the "inline expansion"). The linkers look for function references to bind the function call to the function in case a function call is made. Since C doesn't have function overloading and there are no existing same-named functions in C, the compiler creates the function reference only depending on the function name. However, the function reference cannot created with only function names in C++, because the function overloading brings the same named functions. The C++ compiler writes function references to the linker depending on the function names and the function's parameters, not only depending on the function names.

This mismatch leads to compile and link issues in situations where codes are written in C and compiled with the C compiler, but the function call is made in code that will compiled with the C++ compiler. The C++ compiler generates C++-style links to the linker for function calls if the compiler doesn't know the function code is written in C. This concludes with the linker cannot find the function link to bind the function call. 

> [!note] Note: Inline expansion
> "Inline expansion" means expanding the function code by the compiler, where the place the function call is done.

**How we can solve that mismatch?**
We must tell the C++ compiler that the function is compiled in C to solve the mismatch problem. We do this with a declaration. The compiler can write the function reference in C style instead of C++ style after that declaration. This declaration is called the **extern C declaration**.

We can make an extern C declaration for one function like the following:

```cpp
extern "C" int foo(int, int);
```

We can make an extern C declaration for more than one function like the following:

```cpp
extern "C" {
	int foo1(int, int);
	int foo2(int, int);
	int foo3(int, int);
	int foo4(int, int);
}
```

We generally want the header file can be included from both C and C++ codes. The C compiler gives an error if we write the extern C declaration because C syntax doesn't have that. There is a "**predefined symbolic constant**" that shows the compiler is a C++ compiler. **\_\_cplusplus** is considered predefined if the compiler is a C++ compiler. C compilers don't have that predefined constant. So we can write the header file that is compatible with both C and C++, using the "**conditional compilation directives**".

```cpp
#ifdef __cplusplus
extern "C" {
#endif
	int foo1(int, int);
	int foo2(int, int);
	int foo3(int, int);
	int foo4(int, int);
#ifdef __cplusplus
}
#endif
```
# The class
#Cpp_class
Class is the main tool for **data abstraction** and **object-oriented programming** in C++.  Classes are frequently used and also frequently used in standard libraries. 

There isn't a corresponding tool to the classes in C, but the most similar one is structures. Structures in C++ are not the same as the C, they mostly have commonalities but structures in C++ are like classes and structures have little difference from classes. So the structures and the classes are both kinds of class types in C++.

We use the "class" keyword to create a class. Following is a "**class definition"**, not a class declaration. #Cpp_keyword_class

```cpp
class Myclass {

};
```

> [!note] Note: **Empty struct difference** 
> A struct cannot be empty in C, this would be a syntax error. But a struct or class can be empty in C++, and this is called an "**empty class**". Empty classes have special purposes and they are used. #Cpp_C_difference 

**Tag name difference:** 
The name used as a tag in C does not correspond to the type and isn't used as a type name. We must use the "struct" keyword and the tag to mention a structure type. We need to define a typedef to make the tag correspond to the type. However, in C++, the tag corresponds to the name and can be used as a type name. So in C++, we can use directly the tag name to create a struct or class. #Cpp_C_difference 

> [!note] Note: We can define the typedefs for the structures or the classes in C++, just like C. However this would be unnecessary, so nobody uses it.

```cpp
struct Data {
	int x;
};

int main()
{
	Data myData; // OK in C++ but syntax error in C
	struct Data myData; // OK in C
}
```

**Class Members:** The space between a class's curly braces is open to declaration. The name that is declared in that area is called **class members**. The class members are divided into three categories. These categories are: #Cpp_class_members
- data member
- member function
- type member (nested types)

**Kind of Functions in C++**: There is only one meaning of "function" in C. But there are two meanings of the "function" in C++, the second came with the addition of the classes. The first meaning of the function (that came from C) is called a **global function** or **free function** or **stand-alone function**. The second meaning of the function is the function that is defined in a class as a **member function**. The declaration of a member function is done in class scope.

**Type Member:** A type member (Also called "**member type**" or "**nested type**") can be a class, struct, or enum. 

```cpp
void f(int);

class Data {
	int mx, my; // Data members 
	
	void foo(int); // Member function
	
	class Nested { // Nested is a type member of Data class
		//
	};
};
```
# Scope
#Cpp_scope
**Scope** is an attribute related to identifiers. Identifiers have scopes, expressions don't have scopes.

What is the scope? The scope is code space where the identifier can be used. The compiler can search and find an identifier if we use the identifier in its scope. The compiler searches for the identifier and can't find it if we use the identifier out of its scope.

The important part of C++'s rules is related to scope and name lookup. Scope and **name lookup** are different perspectives on the same matter. Scope means there is an identifier and where I can use this identifier. Name-lookup means that we use an identifier and how the compiler understands whose identifier is it at compile time.
## Name Lookup
#Cpp_name_lookup
Name lookup is a process in which the compiler searches the name that we used in an expression to determine whose name is it. It is a compile-time process. The compiler must find the name declaration, other ways this would be a syntax error. The name lookup process is finished after the finding of the name. The compiler determines if the usage of the name in the expression is valid or not after the name lookup process is done.

There is a complexity difference between C and C++ name lookup rules. C's name lookup rules are so simple in comparison to C++ name lookup rules. So we will see the name lookup in many topics as a subtopic throughout the course.

```cpp error:6
int x();

int main()
{
	int x = 10;
	x(); // Syntax ERROR. The compiler looks for the "x" and finds the variable declaration first.
}
```

With the "x();" syntax the compiler looks for x (means makes name lookup) in the upper example. The compiler finds the "int x=10;" and at that point the name lookup finishes. Then the compiler checks the syntax if it is legal or not. The "x();" is a function call so the compiler throws a syntax error. If the "int x=10;" doesn't exist the compiler would find the "int x();" at the name lookup and that isn't a syntax error due to the "x();" expression.

**How the name lookup can conclude?**
The name lookup can be concluded as a syntax error in two different ways in C++. These situations are:
1. The name that was searched for is not found. This is a syntax error. The compiler searched for a name and did not find the declaration of the name.
2. More than one declaration is found as the result of the search process (name lookup), but the rule of the language is not a determinator of which one is to choose. This situation does not exist in C. We looked it through in the function overloading topic. This situation is called **ambiguity**. #Cpp_C_difference 

**There are two important rules of name lookup. (C and C++)**
1. Name lookup is done in an order that is determined by the language rules. For example, the compiler first looks at the local scope and then looks at the global scope if it doesn't find the name in the local scope.
2. Name lookup finishes upon finding the looked name and it doesn't start again after that. After name lookup, the **context control** is started. The compiler doesn't make a second search if the found declaration causes a syntax error. (This is not the same in every programming language)

> [!note] Note: Scope vs Name Lookup
> Scope: Where the name can be used in the aspect of the programmer.
> Name Lookup: Where the name's declaration can be found in the aspect of the compiler. A name must be used in an expression for mentioning a name lookup.
## C and C++ Scope Rule Differences
Scope rules differ in C++ in comparison to C. First, let's remember the scope rules of C. #Cpp_C_difference 

The scope categories of C:
1. File scope
2. Block scope
3. Function prototype scope
4. Function scope

 The scope categories of C++:
1. namespace scope (instead of file scope of C)
2. class scope (corresponding to this does not exist in C)
3. block scope
4. function prototype scope
5. function scope

**Class Scope:** The scope of the names that are declared in a class definition (a member) is class scope. These names are subjected to different rules. The following conditions must be provided for searching for a name in the class scope:
1. The name must be used as qualified, and that name is called a **qualified name**.

**Qualified Name:** A name that is used as an operand of the following operators is a qualified name.
1. Dot operator: '.' (A member selection operator)
2. Arrow operator: '->' (A member selection operator)
3. Scope resolution operator: '::'

```cpp
class Myclass{
	int mx, my;
	void foo(int);
};

int main()
{
	Mycalss x;
	x.mx = 10; // Qualified name

	Myclass* px{&x};
	p->my = 12; // Qualified name
}
```
## Scope Resolution Operator (::)
#Cpp_ScopeResolutionOperator
The scope resolution operator is one of the most used operators in C++. There are two different uses of this operator. These are:
1. unary scope resolution operator
2. binary scope resolution operator
### The unary scope resolution operator
 A unary operator means the operator takes one operand. The name qualified with a unary operator is searched in the namespace scope. Considering we haven't looked at the namespaces yet, the name qualified with unary resolution operator is searched at global scope. No longer, there is no searching order like the first search at the block and then search at the comprehensive block. The name is directly searched at the namespace that comprehends the block that name is in (global namespace in the below example).

```cpp
int x = 34;

int main()
{
	int x = 12;
	x++; // Local x is increased
	::x++; // Global x is increased
}
```
### The binary scope resolution
The binary scope resolution operator is used in two cases:
1.  with a class name
2.  with a namespace name

The name is searched in the class scope if the binary resolution operator is used with the class name. The name is searched in the namespace scope if the binary resolution operator is used with the namespace name. 

> [!warning]  Warning: We can't use the binary scope resolution operator with the non-static members. This is a syntax error. We need an object instance to use the non-static class members. However, we can use the binary scope resolution operator to access the static members of the classes.

```cpp error:9 valid:10
class Myclass {
public:
	int x; // Case 1
	static int y; // Case 2
};

int main()
{
	Myclass::x = 10; // Case 1: // Error the object is not defined (it is a non-static data member).
	Myclass::y = 10; // Case 2: // OK. x is static
}
```
# Code Control Phases in C++
#Cpp_CodeControlPhases
Code control is done in most cases in three phases in C++. This topic is frequently asked in interviews. These controls are done in the following order: #Cpp_Interview_Topic 
1. **Name lookup:** Searching for the name.
2. **Context control:** The checks for code legality according to the language rules after name lookup.
3. **Access control:** The access control does not exist in C (every code can access data members in C). Languages like C++, C#, and Java check which codes have the rights to use the name in case of a name use.  #Cpp_C_difference 
## Access Control
#Cpp_access_control
Class members can be in three different statuses to the access control that the compiler is done. These statutes are:
1. **public members**
2. **protected membres**
3. **private members**

The whole that all public members exist is called the **public interface**. The whole that all protected members exist is called the **protected interface**. The whole that all private members exist is called the **private interface**.

A class member must belong to one of these statuses.

**Public members**: The public members can be used by all codes without access restrictions.

**Private members**: Private members are only open to the implementation of the class and closed to the clients. That means only the class codes themselves can use the private members. The principle that is called **data hiding** or **information hiding** is related to being private. We acquire a lot of advantages by making the members private. The most important advantage is clients can't use that member, If they try to access private members that would be a syntax error. The clients aren't affected by the changes because they are not using that member when we do changes on private members.

**Protected members**: Protected members are related to the **inheritance** tool set. Inheritance is a tool that is frequently used in object-oriented programming. We can automatically create a new class that takes the public interface of another class with the inheritance mechanism. The inherit class can't use the private members of its parent class but can use protected members of its parent class. That means a protected member can be used by an inherited class but can't be used by an outsider code. #Cpp_Inheritance

**Access Specifier:** There are three keywords for access control, and these keywords are called **access specifiers**. These keywords are public, private, and protected. These keywords specify an area in the class, not a member in C++. These keywords are most common between programming languages and they specify a member in some languages. We can use these keywords multiple times in a class definition. #Cpp_keyword_accessSpecifiers

```cpp
class Myclass {
public:
	// public interface area
private:
	// private interface area
protected:
	// protected interface area
};
```

The default area is private in case no access specifier is specified with the class keyword, but the default area is public in case no access specifier is specified with the struct keyword.

```cpp title:"Access Control Example" error:8
class Myclass {
	int x;
};

int main()
{
	Myclass m;
	m.x = 12; // Access control: Syntax ERROR, x is not public member
}
```

> [!warning]  Warning: 
>A class's public, protected, and private areas are not a scope. So we cannot declare the same name members between these areas. For example, the following is a syntax error.

```cpp error:double
class Data{
public:
	int x;
private:
	double x; // Syntax ERROR, x is already declared. The scope is the same as the public area.
};
```
# Member Functions
#Cpp_memberFunctions
There are two kinds of member functions:
1. **non-static member function**
2. **static member function**

A member function can access all the class members (including the private members).

```cpp
class Myclass{
public:
	void func(); // a non-static member function
	static void foo(); // a static member function
};
```
## Non-static Member Function:
#Cpp_Non-staticMemberFunction
**Hidden Parameter:** A non-static member function takes a hidden parameter that is the address of a class's object and accesses the class's members via that address.  That means if a non-static function has one parameter in visual, actually has two parameters with that hidden parameter. When we call the member function with the dot '.' operator (or arrow "->" operator) we pass the object address to the member function as the hidden parameter. This is similar to the following C syntax:

```cpp hl:11,13
class Myclass{
public:
	void func(); // a non-static member function
	void foo(); // a static member function
};

int main()
{
	Myclass m;

	m.func(10); // "func" is a qualified name

	func(&m, 10); // It is similar to that C syntax.
}
```

>[!note] Note: About the usage of member functions and global functions
>There is no global function in programming languages like C# and Java. That means there are only the member functions in these languages. The functions are called methods in general terms in these languages. However, global functions are exist and frequently used in C++. There are a lot of global function usage in the standard library. Global functions and class member functions serve in cooperation most of the time. So, when we say public interface, we mean the member functions and global functions that came with the class's header file.
>
>**Why do we use global functions instead of member functions sometimes?** 
> The short answer is that being a member function of some functions has disadvantages or is not compatible with the language's other tools in some cases that are related to the language's syntax. 

**Scope Difference:** The member function is searched in class scope when we use the dot "." operator (or arrow "->" operator).

**Access Control Difference:** A member function can access the private members of the class, but global functions can't. This doesn't mean the member function only can access the called object's private members. A member function can access all object's private members. For example, it can access a global class object's private members. For example:

```cpp hl:gm.gx
class Myclass{
public:
	void func(int); // a non-static member function
	void foo(int, int);
private:
	int mx, my;
};

Myclass gm;

int Myclass::func(int x)
{
	gm.gx = 120; // Member function access member of another object of the same class. This is OK.
}
```
## Definition of member function
The definition of the class is written in the header file as a general approach. Member functions and global functions are generally written into the code file. Are all class definitions written in the header file? No, if the class is not a public interface, it can be written in a code file. In that case, the class would not open to the client and only open for implementation usage.

**How do we define a member function?** Member functions are generally defined in the code file. We define the function with the scope resolution operator. #Cpp_ScopeResolutionOperator
```cpp
#include "Myclass.h"

void Myclass::foo(int, int) // Definition of a non-static member function.
{

}
```

**Inlined member functions:** Member functions that are defined inside the class definition are inlined functions. So, we define a member function inside the class definition that inside the header file if we want to define the member function as inlined.

```cpp
// Myclass.h
class Myclass{
public:
	void func(int);
	void foo(int, int) // Inlined non-static member function
	{
		
	}
private:
	int mx, my;
};
```

> [!note] Note: About function definition inside class definition syntax
> Function definition is done inside class definition in C# and Java, and there can't be outside class definition. But the logic of this is different in C++. That kind of syntax makes the function inline in C++.
## Name Lookup Rules Inside A Member Function
#Cpp_name_lookup
**How an unqualified name inside a member function is searched?**
The name is searched in the following order if we use an unqualified name in a member function:
1. block that the name is used (actually from the start of the block and the point that the name is used)
2. class scope
3. namespace scope

> [!warning]  Warning: An unqualified name that in a member function is searched in the class scope if the name isn't found in the block scope. That rule differs compared to global functions. The name that in the global function was searched in the global scope if the name wasn't found in the block scope.

```cpp hl:8-10
#include "myclass.h"

int mx = 45;

void Myclass::func(int ×)
{
	int mx = 5;
	mx = x; // First look at local scope, then look at class scope, then look at namespace scope
	::mx = x; // Directly look at namespace scope
	Myclass::mx = x; // Directly look at class scope
}
```

An unqualified name that is used inside a non-static member function is first searched in the local scope, then searched in the class scope, then searched in the namespace scope.
A qualified name with the unary scope resolution operator that is used inside a non-static member function is directly searched in the namespace scope.

**Are member functions subjected to function overloading?**
Member functions can be overloaded. And don't forget all member functions are in the class scope. And the access specifiers don't make the scope different. #Cpp_FunctionOverloading 

```cpp
class Myclass {
private:
	void func(double);
public:
	void func(int); // Function overload, two functions are in class scope
};
```

>[!warning] Warning: There is no function redeclaration for the member functions

```cpp error:4
class Myclass {
private:
	void func(int);
	void func(int); // Syntax ERROR. A member function cannot be re-declared.
};
```

>[!warning] Warning: Access control is done last. The order is name lookup, context control, and access control. So the function overload resolution is done before the access control. #Cpp_FunctionOverloading 

> [!question] Question: 
> Q: Is there an error in the below code? #Cpp_Interview_Question 
> A: Yes, because access control is done last. The access control is done lastly so, the compiler first chooses the private function as the result of the function overload resolution then makes the access control and throws the error.

```cpp error:ERROR
class Myclass{
private:
	void func(int);
public:
	void func(double);
};

int main()
{
	Myclass m;
	
	m.func(12); // syntax ERROR. The function overloading is done before the access control and this is a call to a private function.
}
```

> [!question] Question: 
> Q: Is there an error in the below code?
> A: The compiler first searches the name in the class scope and finds the non-static "foo()" member function declaration in the class scope. 
 Keep caution in the following on this example:
>  1. There isn't a function overloading in this example. One of the rules for function overloading was that the function must be in the same scope. The class scope and namespace (global in this case) scope are different.
>  2. The name lookup. The function foo is searched in the class scope first. We can call the global function with the syntax "::foo();".
>  3. "foo" is called for the object that which object the "func" is called.

```cpp
class Myclass{ 
private:
public: 
	void func(double); 
	void foo(double); 
}; 

void foo(int)
{

}

int Myclass::func(double d)
{ 
	foo(12); // OK.
}
```

> [!note] Note: We cannot call a non-static member function without a class object.

```cpp error:ERROR
class Myclass{ 
private:
public: 
	void func(double); 
}; 

int main(void)
{
	Myclass::func(1.2); // Syntax ERROR. We need a class instance to use the non-static member of a class.
}
```

>[!note] Note: The below syntax is legal though it is unnecessary until we see inheritances.

```cpp
m.Myclass::func(1.2); // This is valid. Meaningful with the inheritances.
```
## "this" pointer
#Cpp_thisPointer
'this' is a keyword in C++. Some other programming languages use the 'self' keyword instead of the 'this' keyword. We can use the 'this' keyword only in a non-static member function. Usage of the 'this' keyword in a global function or a static member function is a syntax error. 'this' keyword allows us to use the hidden pointer variable of the non-static member function. 'this' is the address of the object that the non-static member function is called for. 'this' represents the address of the object. In some other programming languages, 'self' doesn't represent the address, directly represents the object. If we need such kind of approach we can use "\*this" (to directly use the object, not the address of it). #Cpp_keyword_this

'this' is a PR value expression, not an L value expression. #Cpp_ValueCategory 

All four examples in the below code are the same. If there was a local variable called "mx" inside "func()" only the unqualified "mx" would mean the local mx. The other syntaxes always mean the member "mx".

```cpp
class Myclass{ 
private:
	int mx;
	void foo();
public: 
	void func(int); 
}; 

int Myclass::func(int x)
{ 
	mx = x; // Data member mx
	this->mx = x; // Data member mx
	(*this).mx = x; // Data member mx
	Myclass::mx = x; // Data member mx
}
```

> [!note] Note: Naming convention of members
> Sometimes coders want to indicate that a name within a non-static member function is a member.
> Indication of the membership with the 'this' keyword is a wrong approach. Instead of this approach, we can consider indicating the members with a naming convention.
> There are two widespread naming conventions for member naming:
> 1. usage of 'm\_' prefix
> 2. usage of '\_' postfix

**Why does 'this' pointer exist?**
1. Let's think of a situation, where a member function calls a global function and passes its own object's address as a parameter.

```cpp
class Myclass{ 
private:
public: 
	void func(int); 
}; 

void f(Myclass*);
void g(Myclass&);

int Myclass::func(int x)
{ 
	f(this);
	g(*this); // 'this' reference usage
}

int main()
{
	Myclass m;
	m.func(12);
}
```

2. The return of a member function is the object itself. This technique is used in C# and Java, too. And it provides some advantages. This is called **fluent API** in general, **chaining** in C++.

> [!note] Note:  Fluent API - Chaining
> To make possible the syntax "x.foo().func().f();". This syntax is the same as:
> x.foo();
> x.func();
> x.f();
> The question is how the "x.func();" corresponds to the object itself. The answer is that the return of the function must be an L-value reference to the object.

```cpp
class Myclass{ 
private:
public: 
	Myclass& foo();
	Myclass& func();
	Myclass& bar();
}; 

Myclass& Myclass::foo() // The return of the functions is an L-value reference to the object, making possible the chaining.
{

	return (*this); // "*this" is used because the return type is a reference.
}

int main()
{
	Myclass m;
	m.bar().foo().func(); // The chaining
}
```

First function bar is called, then function foo is called, then function func is called.

> [!note] Note:  If we use pointers instead of references we would make the chain call with the "->" operator.

3. If there is a local object that has the same name as a member and we want to access the object in the class scope. We can use 'this' or resolution operator to do this.

> [!warning]  Warning: The 'this' is a PR value expression. So we cannot assign it a another object's address. The following is a syntax error:

```cpp error:ERROR
void Myclass::func() 
{ 
	Myclass m;
	this = &m; // Syntax ERROR, The "this" is a PR value expression therefore we cannot assign to it.
}
```

But the following syntax is legal.

```cpp valid:OK
void Myclass::func()
{ 
	Myclass m;
	*this = m; // OK
}
```

If "\*this" was a const object, then the upper code would be illegal. We will look through that.
# Chaining And Operator Overloading
#Cpp_Chaining 
We used fluent API/chaining with the syntax "std::cout << x << d;".

**Operator overloading**: The compiler converts the object expression that is an operand of an operator to a function call if a convenient function is declared when the class object is an operator operand. The functions called in this situation (the situation is that an object is an operand of an operator) are called **operator functions**. This function can be a global function or a member function. #Cpp_OperatorOverloading 

"cout" actually is an object and its type is iostream. "iostream" is a class.

There is function overloading in here, too. The overloaded function is "operator<<". #Cpp_FunctionOverloading 

> [!note] Note: "cout" is a global variable. We are including the extern declaration of the "cout" when we include the "iostream" header file.

The syntax is like the following:

```cpp
m1 + m2
m1.operator+(m2)

std::cout << x << d;
std::cout.operator<<(x).operator<<(d);
```

---
# Summary
>[!summary] Section Summary:
>1. Member functions can be overloaded. (In class scope)
>2. A member function cannot be redeclared.
>3.  Overload between a member function and a global function is not possible
>4. Access control is always done at last
>5. This pointer
>6. *this
>8. Fluent API - Chaining

---
# Terms
- inline expansion
- extern C declaration
- C and C++ function overload disagreements
- predefined symbolic constant
- \_\_cplusplus
- conditional compilation directives
- class
- data abstraction
- object-oriented programming
- class member
- data member
- member function
- type member
- global function / free function / stand-alone function
- scope
- name-lookup
- context control
- qualified name
- unqualified name
- member selection operator
- scope resolution operator
- Control Phases
- access control
- public member
- protected member
- private member
- public interface
- protected interface
- private interface
- data hiding
- information hiding
- access specifier
- non-static member function
- static member function
- fluent API
- chaining
- this pointer
---
Return: [[00_Course_Files]]
