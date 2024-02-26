[[00_Course_Files]]
#Cpp_Course 
# C and C++ Function Overload Disagreement - Extern C Declaration
#Cpp_externCdeclaration #Cpp_C_difference 
**What is function overload disagreement between C and C++?**
There is no function overloading in C, but there is in C++. This brings some problems with the linker.

Compilers write an external reference to the linker for function calls if the function call is not inlined (If the compiler doesn't do the "inline expansion"). The linkers look for function references to bind the function call to the function in case a function call is made. Since C doesn't have function overloading and there are no existing same-named functions in C, the compiler creates the function reference only depending on the function name. However, the function reference cannot created with only function names in C++, because the function overloading brings the same named functions. The C++ compiler writes function references to the linker depending on the function names and the function's parameters, not only depending on the function names.

This mismatch leads to compile and link issues in situations where codes are written in C and compiled with the C compiler, but the function call is made in code that will compiled with the C++ compiler. The C++ compiler generates C++-style links to the linker for function calls if the compiler doesn't know the function code is written in C. This concludes with the linker cannot find the function link to bind the function call. 

> [!note] Note: Inline expansion
> "Inline expansion" means to expanding the function code by the compiler, where the place the function call done.

**How we can solve that mismatch?**
We must tell to the C++ compiler that the function compiled in C to solve the mismatch problem. We do this with a declaration. The compiler can write the function reference with C style instead of C++ style after that declaration. This declaration is called **extern C declaration**.

We can make extern C declaration for one function like following.
```cpp
extern "C" int foo(int, int);
```

We can make extern C declaration for more than one function like following.
```cpp
extern "C" {
	int foo1(int, int);
	int foo2(int, int);
	int foo3(int, int);
	int foo4(int, int);
}
```

We generally want that the header file can be included from both C and C++ codes. The C compiler give error if we write the extern C declaration because C syntax does't has that. There is a "**predefined symbolic constant**" that shows the compiler is C++ compiler. **\_\_cplusplus** is considered predefined if the compiler is a C++ compiler. C compilers doesn't have that predefined constant. So we can write the header file that compatible with both C and C++, with using the "**conditional compilation directives**".
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
The main tool for the **data abstraction** and the **object oriented programming** is the class in C++.  Classes are frequently used and also frequently used in standard libraries. 

There isn't a corresponding tool to the classes in C, but the most similar one is structures. Structures in C++ are not the same as the C, mostly have commonalities but structures in C++ are like classes and structures have little difference from classes. So the structures and the classes are both kinds of class types in C++.

We use "class" keyword to create a class. Following is a "class definition", not a class declaration.
```cpp
class Myclass {

};
```

**Empty struct difference:** A struct cannot be empty in C, this would be a syntax error. But a struct or class can be empty in C++, and this is called "empty class". Empty classes has special purposes and they are used.
#Cpp_C_difference 

**Tag name difference:** The name used as a tag in C does not correspond to the type and isn't used as a type name. We must use "struct" keyword and the tag to mention a structure type. We must use the "struct" keyword and the tag to mention a structure type. We need to define a typedef to make the tag correspond to the type. However in C++, The tag corresponds to the name and can be used as type name. So in C++, we can use directly the tag name to create a struct or class.
Note: We can define the typedefs for the structures or the classes in C++, just like C.However this would be unnecessary, so nobody uses it.
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
#Cpp_C_difference 

**Class Members:** The space between a class's curly braces is open to declaration. The name that is declared in that area is called **class members**. The class members divide into three categories. These categories are:
- data member
- member function
- type member (nested types)
#Cpp_class_members

**Kind of Functions in C++**: There is only one meaning of "function" in C. But there are two meanings of the "function" in C++, the second came with the addition of the classes. The first meaning of the function (that came from C) is called a **global function** or **free function** or **stand-alone function**. The second meaning of the function is the function that is defined in a class as a **member function**. The declaration of a member function is done in class scope.

**Type Member:** A type member (Also called "member type" or "nested type") can be a class, struct or enum. 

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
Scope is a attribute related to identifiers. Identifiers have scopes, expressions don't have scopes.

What is the scope? The scope is code space where the identifier can be used. The compiler can search and find an identifier if we use the identifier in its scope. The compiler searches for the identifier and can't find it if we use the identifier out of its scope.

The important part of C++'s rules is related to scope and name lookup. Scope and name lookup are different perspectives on the same matter. Scope means there is an identifier and where I can use this identifier. Name-lookup means that we use an identifier and how the compiler understands who's identifier is it at compile time.

## Name Lookup
Name lookup is a process that the compiler searches the name that we used in an expression to determine whose name is it. It is a compile-time process. The compiler must find the names declaration, other ways this would be a syntax error. The name lookup process is finished after the finding of the name. The compiler determines if the usage of the name in the expression is valid or not after the name lookup process is done.
#Cpp_name_lookup

There is complexity difference between C and C++ name lookup rules. C's name lookup rules are so simple in comparison to C++ name lookup rules. So we will see the name lookup in many topics as a subtopic through the course.


> [!example]  Exmaple: Name Lookup Example
```cpp
int x();

int main()
{
	int x = 10;
	x();
}
```
With the "x();" syntax the compiler looks for x (means makes name lookup) in upper example. The compiler finds the "int x=10;" and at that point the name lookup finishes. Than the compiler checks the syntax if is it legal or not. The "x();" is a function call so the compiler throws a syntax error. If the "int x=10;" doesn't exist the compiler would find the "int x();" at the name lookup and that isn't a syntax error due to "x();" expression.

>[!tip] Tip: Tip: How the name lookup can conclude?
>The name lookup can be concluded in two different ways in C++. These situations are:
>1. The name that was searched for is not found. This is a syntax error. The compiler searched for a name and did not find the declaration of the name.
>2. More than one declaration is found as the result of the search process (name lookup), but the rule of the language is not a determinator of which one is to choose. This situation does not exist in C. We looked it through in the function overloading topic. This situation is named as **ambiguity**.

> [!tip] Tip: There are two important rules of name lookup. (C and C++)
> 1. Name lookup is done in a order that is determined by the language rules. For example the compiler first looks the local scope then looks the global scope if it don't find the name in local scope.
> 2. Name lookup is finishes upon finding the looked name and it don't start again after that. After name lookup the **context control** is started. The compiler doesn't make a second search if the found declaration causes a syntax error. This is not same in every programming language)

>[!tip] Tip: Scope vs Name Lookup
>Scope: Where the name can be used in aspect of the programmer.
>Name Lookup: Where the name's declaration can be found in aspect of the compiler. A name must be used in an expression for mentioning a name lookup.

### C and C++ Scope Rule Differences
#Cpp_C_difference 
Scope rules differ in C++ in comparison to C. First let's remember the scope rules of the C.

The scope categories of C:
1. File scope
2. Block scope
3. Function prototype scope
4. Function scope

 The scope categories of C++:
1. namespace scope (instead file scope of C)
2. class scope (corresponding of this is not exist in C)
3. block scope
4. function prototyp scope
5. function scope

**Class Scope:** Scope of the names that declared in a class definition (a member) is class scope. These names are subjected to different rules. The following conditions must be provided for searching a name in the class scope:
1. The name must be used as qualified, and that name is called as **qualified name**.

**Qualified Name:** A name that used as operand of the following operators is a qualified name.
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
Scope resolution operator is one of the most used operator in C++. There are two different usage of this operator. These are:
1. unary scope resolution operator
2. binary scope resolution operator

### The unary scope resolution operator
 A unary operator means the operator takes one operand. The name qualified with a unary operator is searched in the namespace scope.  Considering we haven't looked at the namespaces yet, the name qualified with unary resolution operator is searched at global scope. No longer, there is no searching order like the first search at the block and then search at the comprehensive block. The name is directly searched at the namespace that comprehends the block that name is in (global namespace in below example).

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
1. used with a class name
2. used with a namespace name

The name is searched in the class scope if the binary resolution operator used with the class name. The name is searched in the namespace scope if the binary resolution operator used with the namespace name. 

> [!warning]  Warning: Binary scope resolution operator static member example
```cpp
class Myclass {
public:
	int x; // Case 1
	static int x; // Case 2
};

int main()
{
	Myclass::x = 10; // Case 1: // Error the object is not defined
	Myclass::x = 10; // Case 2: // OK. x is static
}
```

# Control Phases in C++
#Cpp_code_control_phases
Code control is done most of the case in three phase in C++. This topic is frequently asked in interviews. These controls are done in following order:
1. name lookup
2. context control
3. access control

**Name lookup:** Searching for the name.
**Context control:** The checks for code legality according to the language rules after name lookup.
**Access control:** The access control does not exist in C (every code can access data members in C). Languages like C++, C#, and Java check which codes have the rights to use the name in case of a name use. 
## Access Control
#Cpp_access_control
Class members can be in three different statuses to the access control that the compiler is done. These statutes are:
1. **public members**
2. **protected membres**
3. **private members**

The whole that all public members exist is called **public interface**. The whole that all protected members exist is called **protected interface**. The whole that all private members  exist is called **private interface**.

A class member must be belong to one of these statuses.

**Public members**: All code can use the public members without a access restriction.

**Private members**: Private members are only open to the implementation of the class and closed to the clients. That means only the class codes themselves can use the private members. The principle that is called **data hiding** or **information hiding** is related to private. We acquire a lot of advantages by making the members private. The most important advantage is clients can't use that member, If they try to access private members that would be a syntax error. The clients aren't affected by the changes because they are not using that member when we do changes on private members.

**Protected members**: Protected members are related to the **inheritance** tool set. Inheritance is a tool that is frequently used in object-oriented programming. We can automatically create a new class that takes the public interface of another class with inheritance mechanism. The inherit class can't use the private members of its parent class but can use protected members of its parent class. That means a protected member can be used by an inherited class but can't be used by an outsider code.

**Access Specifier:** There are three keywords for access control, and these keywords are called **access specifiers**. These keywords are public, private, and protected. These keywords represent an area in the class, not a member in C++. These keywords are most common between programming languages and they represent a member in some languages. We can use these keywords multiple times in a class definition.

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

The default area is private in case no access specifier is specified with class keyword, but the default area is public in case no access specifier is specified with struct keyword.

>[!example] Example: Access control
```cpp
class Myclass {
	int x;
};

int main()
{
	Myclass m;
	m.x = 12; // Access control: Syntax error, x is not public member
}
```

> [!warning]  Warning: 
>A class's public, protected and private areas are not a scope. So we cannot declare same name members between these areas. For example following is a syntax error.
```cpp
class Data{
public:
	int x;
private:
	double x; // Syntax error, x is already declared. The scope is same with public area.
};
```

# Member Functions
#Cpp_memberFunctions
There are two kind of member functions:
1. **non-static member function**
2. **static member function**

A member function can access all the class members (including the private members).

>[!example] Example: static and non-static member functions
```cpp
class Myclass{
public:
	void func(); // a non-static member function
	static void foo(); // a static member function
};
```

## Non-static Member Function:
**Hidden Parameter:** A non-static member function takes a hidden parameter that the address of a class's object and access the class's members via that address.  That means if a non-static function has one parameter in visual, actually has two parameters with that hidden parameter. When we call the member function with the dot '.' operator (or arrow "->" operator) we pass the object address to the member function as the hidden parameter. This is similar to the following C syntax:

```cpp
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
>There is no global function in programming languages like C# and Java. That means there are only the member functions in these languages. The functions are called methods in general terms in these languages. But, global functions are exist and frequently used in C++. There are a lot of global function usage in the standard library. Global functions and class member functions serve in cooperation most of the time. So, when we say public interface, we mean the member functions and global functions that came with the class's header file.
>
>**Why we use global functions instead of member functions some times?** 
>Short answer is, being member function of some functions has disadvantages or not compatible with language's other tools in some cases that related to language's syntax. 

**Scope Difference:** The member function is searched in class scope when we use the dot "." operator (or arrow "->" operator).

**Access Control Difference:** A member function can access the private members of the class, but global functions can't. This doesn't mean the member function is only access the called object's private members. A member function can access all object's private members. For example it can access a global class object's private members. For example:

```cpp
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
	gm.gx = 120; // Member functon access member of the another object of the same class. This is OK.
}
```

## Definition of member function
The definition of the class is wrote in header file as general approach. Member functions and global functions are generally written into the code file. Are all class definitions wrote in header file? No, if the class is not a public interface, it can be written in code file. In that case the class would not open to client and only open for implementation usage.

**How we define a member function?** Member functions are generally defined in the code file. We define the function with scope resolution operator.
```cpp
#include "Myclass.h"

void Myclass::foo(int, int)
{

}
```

**Inlined member functions:** Member functions that defined inside the class definition are inlined functions. So, we define a member function inside class definition that inside the header file if we want to define the member function as inlined.
```cpp
// Myclass.h
class Myclass{
public:
	void func(int);
	void foo(int, int) // Inlined function
	{
		
	}
private:
	int mx, my;
};
```

> [!note] Note: About function definition inside class definition syntax
> Function definition is done inside class definition in C# and Java, and there can't be outside class definition. But the logic of this is different in C++. That kind of syntax makes the function inline in C++.
## Name lookup rules inside a member function
#Cpp_name_lookup
**How an unqualified name that inside a member function is searched?**
The name is searched in following order if we use a unqualified name in a member function:
1. block that the name is used (actually the from the start of block and the point that name is used)
2. class scope
3. namespace scope

> [!warning]  Warning: 
> An unqualified name that in a member function is searched in the class scope if the name isn't found at block scope. That rule differs comparing to global functions. The name that in global function was searched in the global scope if the name isn't found in block scope.

```cpp
#include "myclass.h"

int mx = 45;

void Myclass::func(int ×)
{
	int mx = 5;
	mx = x; // First look at local scope, than look at class scope, than look at namespace scope
	::mx = x; // Direcly look at namepace scope
	Myclass::mx = x; // Direcly look at class scope
}
```
An unqualified name is first searched in local scope, than searched in class scope, than searched in namespace scope.
The name is searched in namespace scope with unary scope resolution operator.
The name is searched in class scope with binary scope resolution operator.

**Are member functions subjected to function overloading?**
Member functions can be overloaded. And don't forget all member functions are in the class scope. And the access specifiers don't make the scope different.
```cpp
class Myclass {
private:
	void func(double);
public:
	void func(int); // Function overload, two functions are in class scope
};
```

>[!warning] Warning: There is no function redeclaration for the member functions

```cpp
class Myclass {
private:
	void func(int);
	void func(int); // Syntax ERROR. A menber function cannot be re-declared.
};
```

>[!warning] Warning: Access controls is done last

> [!question] Question: 
> Q: Is there a error in below code?
> A: Yes, because access control is done last. The access control is done lastly so, the compiler first chose the private function as the result of the function overload resolution then makes the access control and throws the error.
> #Cpp_Interview_Question 
```cpp
class Myclass{
private:
	void func(int);
public:
	void func(double);
};

int main()
{
	Myclass m;
	
	m.func(12); // syntax ERROR.
}
```


> [!question] Question: 
> Q: Is there a error in below code?
> A: The compiler first searches the name in the class scope and find the "non-static foo()" function declaration in the class scope. 
 Keep caution in followings on this example:
>  1. There isn't a function overloading in this example. On of the rule for function overloading was that the function must be in same scope. The class scope and namespace (global in this case) scope are different.
> 2. The name lookup. The function foo is searched in class scope first. We can call the global function with syntax "::foo();".
> 3. "foo" is called for the object that which object func is called.
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
```cpp
class Myclass{ 
private:
public: 
	void func(double); 
}; 

int main(void)
{
	Myclass::func(1.2); // Syntax ERROR.
}
```

>[!note] Note:
>Below syntax is legal though it is unnecessary until we see inheritances.
>```cpp
>m.Myclass::func(1.2); // This is valid.
>```

## "this" pointer
#Cpp_thisPointer
'this' is a keyword in C++. Some other programming languages uses 'self' keyword instead if 'this' keyword. We can use 'this' keyword only in a non-static member function. Usage of 'this' keyword in a global function or a static member function is a syntax error. 'this' keyword allow us to use the hidden pointer variable of the non-static member function. 'this' is address of the object that the non-static member function is called for. 'this' represent the address of the object. In some other programming languages, 'self' don't represent the address, directly represents the object. If we need such kind of approach we can use "\*this". 

'this' is a PR value expression, not a L value expression. 

All four examples in below code are same. But if there is a local variable called "mx" inside "func()" only the unqualified mx means the local mx.
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
> Bazen kodlayıcılar statik olmayan üye işlevi içindeki bir adın üye olduğunu belirtmek isterler.
> Indication the being member with 'this' keyword is a wrong approach. Instead of this approach, we can consider indicate the members with a naming convention.
> There are two widespread naming convention for member naming:
> 1. usage of 'm_' prefix
> 2. usage of '_' postfix

**Why 'this' pointer exist?**
1. Let's think a situation, a member function calls a global function and passes own object's address as a parameter.
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
> To make possible the syntax "x.foo().func().f();". This syntax is same as:
> x.foo();
> x.func();
> x.f();
> The question is that how the "x.func();" corresponds to he object itself? The answer the return of the function must be a L value reference to the object.
```cpp
class Myclass{ 
private:
public: 
	Myclass& foo();
	Myclass& func();
	Myclass& bar();
}; 

Myclass& Myclass::foo()
{

	return (*this);
}

int main()
{
	Myclass m;
	m.bar().foo().func();
}
```
First function bar is called, then function foo is called, then function func is called.
If we use pointer instead of reference we would make the chain call with the "->" operator.

3. If there is a local object that has same name as a member and we want to access object in the class scope. We can use 'this' or resolution operator for do this.

>[!note] Note:
>'this' is a PR value expression. So we cannot assign it a another object's address. The following is a syntax error:
```cpp
void Myclass::func() 
{ 
	Myclass m;
	this = &m; // Syntax ERROR, this is a PR value expression
}
```

But the following syntax is legal.
```cpp
void Myclass::func()
{ 
	Myclass m;
	*this = m; // OK
}
```
If "\*this" is be a const object, then that would be illegal. We will look through that.

> [!note] Note:  Chaining and operator overloading
>We used fluent API/chaining before with syntax "std::cout << x << d;".
>**Operator overloading**: the compiler converts the object expression that is operand of an operator to a function call if a convenient function is declared when class object be a operators operand. The function called in this situation (The situation that an object is an operand of an operator) are called **operator functions**. This functions can be a global function or a member function.
>"cout" actually is a object and its type is iostream. "iostream" is a class.
>Note: There is function overloading in here, too. The overloaded function is "operator<<".
>Note: "cout" is a global variable. We are including the extern declaration of the "cout" when we are include the "iostream" header file.
>The syntax is like following:
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
- object oriented programming
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
