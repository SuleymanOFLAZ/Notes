#Cpp_Course 
[[00_Course_Files]]

# Dynamic Objects
#Cpp_DynamicObjects
Objects with dynamic lives are objects:
 - whose lives we can start at any point we want
 - whose lives we can end at any point we want
Their life is not restricted between a block.

Note that, **dynamic memory management** is a separate concept from the objects that have dynamic lifespans. Dynamic Memory Management is obtaining the memory needed by the program by a code at run-time. We need dynamic memory management to create a dynamic object.

Dynamic objects are created with an operator that is called **new**. This expressions are called "**new expression**". #Cpp_Operator_new

Creating a dynamic object has different syntax and tools between C and C++. Typically, we obtain storage with the help of dynamic memory management functions in C, then we use the storage to create an object, this is generally assigning values to the members of the object. However, dynamic objects are created with the "new" in C++. #Cpp_C_difference 

Some operations are needed to end dynamic objects in C, these operations are called "**cleanup code**", for example giving back the resources that the object uses (other objects that the objects use) if it exists. After that, we give back the memory area allocated with the dynamic memory management to the system. The function that does this job in C is "**free**".

We end the life of the dynamic objects by using an operator that is called "**delete**" in C++. #Cpp_operator_delete

> [!note] Note: It is mandatory to be dynamic all class objects in a lot of OOP programming languages like JAVA and C#. Don't confuse it with the C++.

Dynamic objects are much more costly (time and space cost) in comparison to objects that have automatic life span. Thus, we shouldn't use dynamic objects if it isn't necessary. C++ is a language that is used at places the speed is needed, if we don't consider those concepts we can't get the benefit of C++

Since there is no garbage collector (at least there is no coming ready) in C++, and the dynamic objects are used with pointer semantic, using a dynamic object:
- makes the code complex
- increases the possibility of making mistakes

> [!note] Note: The "new" and "delete" are actual operators, but the standards call them "new expressions".

```cpp
class Myclass {
	//...
};

int main()
{
	Myclass* p1 = new Myclass; // OK
	Myclass* p2(new Myclass); // OK
	Myclass* p3{new Myclass}; // OK

	auto p4 = new Myclass; // OK. Type of p4 is "Myclass*"
	auto *p5 = new Myclass; // OK Type of p5 is "Myclass*", and the type that corresponds the auto is "Myclass"

	auto p6(new Myclass); // OK
	auto p7{new Myclass}; // OK
}
```
#Cpp_keyword_auto 

The type of the expression is a pointer type when we use the new operator. Thus, we use a pointer variable to use the object. We can use different initialize syntaxes with the new. And we can use the auto with the new.

## New Operator
#Cpp_Operator_new 
The compiler generates code like the following when we create an object with the new expression:
A storage as many sizes of the type is allocated with **the new operator function** which is a standard global function of C++. This is a function that has the same parametric structure as the "malloc" function in C.
```cpp
void* operator new(size_t); // The operator new function
```

Why is the "operator new function" called instead of the "malloc"? The "operator new function" makes some operations that differ from the "malloc". For example, the malloc returns a null pointer if it fails. However, the operator new function throws an exception in the type of **std::bad_alloc** class if it fails. So, a new function is added to the language that has the name "operator new" because the "malloc" is not compatible with the other tools of the language. Maybe the operator new function calls the malloc, this can depend on the compiler and implementation.

```cpp
class Myclass {
	//...
};

int main()
{
	auto p = new Myclass;
	/*
		(Myclass*)(operator new(sizeof(Myclass)))->Myclass();
	*/
}
```
The compiler calls the operator new function with the size of the object. If it fails the compiler throws an exception. If it allocates, returns a void address. Then the compiler type casts the address to the object address and calls the constructor of the class using the address as the this pointer. #Cpp_constructor #Cpp_TypeCastOperators #Cpp_Exception #Cpp_thisPointer 

We can use the address with the arrow operator:
```cpp
class Myclass {
public:
	void func();
};

int main()
{
	auto p = new Myclass;

	p->func();
}
```

Or we can use it like the following:
```cpp
class Myclass {
public:
	void func();
};

int main()
{
	auto& x = *new Myclass;

	x.func();
}
```

> [!note] Note: We almost never use the new operator in modern C++. Instead of directly using the new operator, we make calls to the standard functions that use the new operator. #Cpp11

> [!note] Note: We use/manage the dynamic objects that are created with the new operator with the class objects that are called **smart pointers** instead of pointer variables. #Cpp_SmartPointers

> [!note] Note: Smart Pointers are a class type that has a pointer-like interface, but it is not a pointer. There are standard smart pointers and non-standard smart pointers. Some standard pointers:
> - unique_ptr
> - shared_ptr

> [!note] Note: They are two different pointer concepts in C++. These are
> - **raw pointers** (or **naked pointers**) - these are pointer variables like in C.
> - **smart pointers**

> [!example] Example: smart pointer example

```cpp
#include <memory> // To use the smart pointer

class Myclass {
public:
	void func();
};

void func()
{
	auto p = std::make_unique<Myclass>();
} // We don't delete it. It will be deleted automatically when it comes to the end of the function code
```

> [!warning]  Warning: The "new operator" and the function named "operator new" are different things.

## Delete Operator
#Cpp_operator_delete 
We give the address of the object that we want to end its life as an operand to the delete operator.

The compiler first calls the destructor of the class. Then make a call to the "**operator delete function**". The "operator delete function" corresponds to the "free" function in C. The function is a standard global function of C++. The parametric structures of the functions are the same.

```cpp
void operator delete(void *vp);
```

```cpp
class Myclass {
	//...
};

int main()
{
	auto p = new Myclass;
	///...

	delete p;
	/*
		p->~Myclass();
		operator delete(p);
	*/
}
```

When we delete a dynamic object, the destructor of the object is called first, then the operator delete functions is called. #Cpp_Destructor 

```
new Myclass        operator new(sizeof(Myclass)) + Myclass::Myclass()
delete p           p->~Myclass() + operator delete(p)
```

> [!warning]  Warning: Calling the "free" function instead if the delete operator is undefined behavior.

## How do we call the default constructor with the new operator?

The below examples make a call to the default constructor.
```cpp
class Myclass {
	//...
};

int main()
{
	Myclass* p1 = new Myclass;
	Myclass* p2 = new Myclass(); // Value initializa, zero initialize
	Myclass* p3 = new Myclass{}; // Value initializa, zero initialize
}
```

What it would be with the build-in types?
```cpp
int main()
{
	int* p = new int;   // Garbage value
	int* p = new int(); // Zero initialize
	int* p = new int{}; // Zero initialize
}
```

## How do we call any constructor with the new operator?
```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(int, int);
};

int main()
{
	auto p1 = new Myclass(4, 6); // The second constructor will be called.
	auto p2 = new Myclass{4, 6}; // The second constructor will be called.

	delete p;
}
```

> [!note] Note: We can use the const keyword after the new operator. In this case, the dynamic object is a const class object. The type type will be "const T*" #Cpp_keyword_const

```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(int, int);
};

int main()
{
	Myclass* p1 = new const Myclass{4, 6}; // Syntax ERROR because there is no type conversion from "const T*" to "T*"
	const Myclass* p2 = new const Myclass{4, 6}; // OK. The object is const. The type is "const Myclass*"

	auto p3 = new const Myclass{4, 6}; // OK. The type of p3 is "const Myclass *". The deduced type with the auto is "const Myclass*" (same).
	auto* p4 = new const Myclass{4, 6}; // OK. The type of p4 is "const Myclass *". The deduced type with the auto is "const Myclass"
	
	delete p;
}
```
#Cpp_TypeDeduction_Auto

## When do we use dynamic objects?
We shouldn't use dynamic objects if really need them. But, what are the necessary scenarios?

1. There are situations in which the type of the object is determined during the run-time. Usage of the dynamic object is necessary in this situation.

2. There are situations in which the number of objects is determined during the run-time.

3. A function brings an object to life and passes it on to the calling code, the object that is brought to life is used by the calling code, and its life is ended by the calling code. This type of function is called the "**factory function**".

One of the risks with the usage of dynamic objects is the **dangling pointer**.

```cpp
class Myint {
public:
	Myint(int x) : mx(x) {}
	~Myint()
	{
		std::out << "destructor\n";
	}
	void print() const
	{
		std::cout << "mx = " << mx << "\n";
	}
private:
	int mx;
};

int main()
{
	Myiny* p = new Myint(100);

	p->print();

	delete p;
	// Now, p is a dangling pointer
	p->print(); // Undefined behavior
}
```

Double deletion is an undefined behavior.
```cpp
int main()
{
	Myiny* p1 = new Myint(100);

	auto p2 = p1;

	delete p1;
	delete p2; // Double deletion, undefined behavior
}
```

> [!note] Note: The "resource leak" and the "memory leak" are different things. The memory leak occurs when we allocate an area on the memory and we don't give it back. The resource leak occurs when we don't give back the resources that are obtained by the constructor of the class. A memory leak can be a resource leak.

```cpp
#include <cstdlib>
#include <memory>
class File {
public:
	File(const char* p) : mp(std::fopen(p,"w")) {
		// ...
	}
	// ...
	~File()
	{P
		std::fclose(mp);
	}
private:
	FILE* mp;
};

int main()
{
	FILE* p = new("file.txt");

	// If we don't delete the dynamic object, two mistakes occur.
	// 1. The operator delete function isn't called. Thus, A memory area that is the size of the File won't be given back. This is a memory leak and also a resource leak.
	// 2. The destructor isn't called. Thus, the file object won't be closed. This is a resource leak.
}
```

> [!question] Question: What will happen if we don't delete an object that was created with the new operator? #Cpp_Interview_Question 
> A: A memory block that has a size of T is allocated with the new operator. The operator delete function won't be called if we don't delete it. Thus, a memory leak that has a size of T is happened.
> The destructor of the class T won't be called. This means the resources obtained by the constructor (if it is obtained) won't be given back.

```cpp
#include <string>

using namespace std;

int main()
{
	string str(10'000, 'A'); // 10000 A characters are stored. These characters are stored in the heap. The constructor of the string class allocates this memory. Note that, the string object itself (size of string) isn't allocated on the heap, it is allocated on the stack.

	auto p = new string(10'000, 'A'); // The string object is created dynamically. Again, a memory area that holds 10000 A character is allocated on the heap. Another memory that is the size of a string is allocated on the heap too. Both the string object itself and the memory to store the characters are allocated on the heap
}
```

> [!note] Note: What happens when the program comes to the end with a not-deleted dynamic object?
> A memory leak isn't an issue after the end of the program. The heap area is allocated for the process. The heap is given back with the termination of the process.
> However, the destructor of the object isn't called. That can be a problem. For example, the destructor can be writing some log information to a file, if the destructor isn't called, the log information isn't written at the end of the program. Thus, this depends on our object.
> If the destructor is trivial, then there is no issue about not deleting the dynamic object if we are planning to use the object for all the program's life. However, this can be a bad implementation, deleting the object is the more convenient approach.

## We Can Overload the Operator New and Operator Delete Functions
We can write the operator new and operator delete functions. This means, our functions will be called whenever a call is needed to the operator new or operator delete functions instead of the compiler-defined ones.
```cpp
#include <iostream>
#include <cstdlib>
#include <memory>

void operator new(std::size_t sz)
{
	std::cout << "operator new called for the size of: " << sz << "\n";

	if(sz == 0)
		++sz;

	if(void* ptr = std::malloc(sz))
	{
		std::cout << "the address of allocated memory block is: " << ptr << "\n";
		return ptr;
	}

	throw std::bad_alloc{};
}

void operator delete(void* ptr) noexcept
{
	std::cout << "operator delete called for the address of: " << ptr << "\n";
	std::free(ptr);
}

class Myclass {
public:
	Myclass()
	{
		std::cout << "Myclass ctor this: " << this << "\n";
	}
	~Mycalss()
	{
		std::cout << "Myclass destructor this: " << this << "\n";
	}

private:
	unsigned char buffer[1024];
};

int main()
{
	Myclass* p = new Myclass;
	std::cout << "p = " << p << "\n";

	delete p;
}
```

The output is:
```
operator new called for size of: 1024
the address of allocated memory block is: 00AE5588
Myclass ctor this: 00AE5588
p = 00AE5588
Myclass destructor this: 00AE5588
operator delete called for the address of: 00AE5588
```

Let's look at an example with the string class:
```cpp
#include <iostream>
#include <cstdlib>
#include <memory>
#include <string>

void operator new(std::size_t sz)
{
	std::cout << "operator new called for the size of: " << sz << "\n";

	if(sz == 0)
		++sz;

	if(void* ptr = std::malloc(sz))
	{
		std::cout << "the address of allocated memory block is: " << ptr << "\n";
		return ptr;
	}

	throw std::bad_alloc{};
}

void operator delete(void* ptr) noexcept
{
	std::cout << "operator delete called for the address of: " << ptr << "\n";
	std::free(ptr);
}

int main()
{
	string str(10000, 'A');
	
	delete str;
}
```

The output is:
```
operator new called for size of: 10051
the address of allocated memory block is: 00AE5588
operator delete called for the address of: 00AE5588
```

If we use a dynamic object, the operator new and delete functions are called twice.
```cpp
int main()
{
	auto p = new string(10000, 'A');
	
	delete p;
}
```

The output is:
```
operator new called for size of: 24
the address of allocated memory block is: 00AE6644
operator new called for size of: 10051
the address of allocated memory block is: 00AE5588
operator delete called for the address of: 00AE5588
operator delete called for the address of: 00AE6644
```

## We Can Use the Operator New and Operator Delete Functions by Themselves
We can call the operator new and delete functions instead of malloc and free.

```cpp
int main()
{
	auto p = operator new(1000);
	operator delete(p);
}
```

## Array New and Array Delete Operators
#Cpp_Operator_ArrayNew #Cpp_Operator_ArrayDelete
There are one then more new and delete operators. The operators that we looked at are used to create one dynamic object.

The **array new operator** is used to create a dynamic array.  The **array delete operator** is used to delete a dynamic array. These are different operators from the new and delete operators.

```cpp
int main()
{
	int x {30};
	auto p = new int[x]; // 30 dynamic object will be created.
	// ...

	delete[]p;
}
```

```cpp
using namespace std;

int main()
{
	size_t n;

	std::cout << "how many integers: ";
	cin >> n;

	auto ip = new int[n];
	for(size_t i = 0; i < n; ++i) {
		ip[i] = i;
	}

	///
	for(size_t i = 0; i < n; ++i) {
		std::cout << ip[i] << " ";
	}

	delete[]ip; // Between the square brackets has to be empty
}
```

```cpp
int Myclass {
public:
	Myclass()
	{
		std::cout << "ctor this: " << this << "\n";
	}
	~Myclass()
	{
		std::cout << "destructor this: " << this << "\n";
	}
private:
	int a[4]{};
};

int main()
{
	size_t n;

	std::cout << "how many objects: ";
	cin >> n;

	auto p = new Myclass[n]; // The constructor will be called for each object
	std::cout << "\n\n";

	delete[] p; // The destructor will be called for each object
}
```

The output is:
```
how many objects: 10
ctor this: 00C35070
ctor this: 00C35080
ctor this: 00C35090
ctor this: 00C350A0
ctor this: 00C350B0
ctor this: 00C350C0
ctor this: 00C350D0
ctor this: 00C350E0
ctor this: 00C350F0
ctor this: 00C35100

destructor this: 00C35100
destructor this: 00C350F0
destructor this: 00C350E0
destructor this: 00C350D0
destructor this: 00C350C0
destructor this: 00C350B0
destructor this: 00C350A0
destructor this: 00C35090
destructor this: 00C35080
destructor this: 00C35070
```

> [!warning]  Warning: Terminating an object with array delete that is created with the new operator is an undefined behavior. Or vice versa.

We can create dynamic objects with different constructors by using the array new operator.
```cpp
class Myclass {
public:
	Myclass() {}
	Myclass(int) {}
	Myclass(double) {}
};

int main()
{
	auto p = new Myclass[3]{Myclass{}, Myclass{2}, Myclass{3.4}};
}
```

## Placement New Operators
#Cpp_Operator_PlacementNew
**Placement new** operators take extra operands and give us a chance to create dynamic objects at addresses that we desire.

For example, We have a memory area that is the size of "Myclass". Can we create a "Myclass" object in that memory area? That means the address of the area will be the this pointer for the class object.

```cpp
class Myclass {
public:
	//...
};

int main()
{
	char buffer[sizeof(Myclass)]; // A memory area tha is size of Myclass.
	std::cout << (void*)buffer << "\n"; // That address will be the this address for the object that created with the placement new operator.
	auto p = new(buffer)Myclass; // Placement new.

	delete p; // !Undefined Behavior
	p->~Myclass(); // OK. We have to make a manual call to the destructor.
}
```
A memory area isn't allocated with the placement new operator. The object is created in the memory that is already allocated and given to the placement new operator.

> [!warning]  Warning: Calling the delete operator for an object that is created with the placement new operator is an undefined behavior.

We can't call the delete operator for an object that is created with the placement new operator. This will be an undefined behavior. Because we didn't create the object with the new operator. This is one of the rare situations in which we need to make manual a call to the destructor. #Cpp_Destructor 

# Move Only Types
#Cpp_MoveOnlyTypes #Cpp_CopyConstructor #Cpp_CopyAssignment #Cpp_MoveAssignment #Cpp_MoveAssignment #Cpp_MoveSemantic 
These are types that the class objects can be moved but not copied. Some of the standard library classes are move-only types. For example, ostream, unique_ptr, mutex are move-only classes. 

How we can declare a move-only class.
If we declare a move member, the compiler deletes the copy members. So, there is no difference between deleting the copy members and not mentioning them.
```cpp
// Only declare the move members, not the copy members
class MoveOnly {
public:
	MoveOnly() = default;

	MoveOnly(Moveonly&&);
	MoveOnly& operator=(MoveOnly&&);
};

// Or we can delete the copy members
class MoveOnly {
public:
	MoveOnly() = default;
	MoveOnly(const MoveOnly&) = delete;
	MoveOnly& operator=(const Myclass&) = delete;
	
	MoveOnly(Moveonly&&);
	MoveOnly& operator=(MoveOnly&&);
};
```

```cpp
public:
	MoveOnly() = default;

	MoveOnly(Moveonly&&);
	MoveOnly& operator=(MoveOnly&&);
};

int main()
{
	MoveOnly x;
	MoveOnly y;

	auto z = x; // Syntax error: "attempting to reference a deleted function".

	x = y; // Syntax error: "attempting to reference a deleted function".

	x = std::move(y); // OK.
}
```

Example 1:
```cpp
#include <iostream>

int main()
{
	using namespace std;

	ostream mycout = cout; // Syntax error. Because the copy members of the ostream class are deleted.
}
```

Example 2:
```cpp
#include <memory>

int main()
{
	using namespace std;

	unique_ptr<int> uptr;

	auto x = uptr; // Syntax error. The copy members of the unique_ptr class are deleted.
	auto x = std::move(uptr); // OK.
}
```

> [!question] Question: What is the output for the following example?
> A: The compiler does the NRVO. Note that the NRVO is not mandatory. When we open the debug mode, the compiler doesn't do the NRVO. Only the default constructor will be called in case of the NRVO. When we use the debug mode, the move constructor will be called. The "x" expression is an L-value. It is a named and expiring object. The conversion from the L-value expression to the X-value expression is done at the return statement by the compiler. That means the called constructor is the move constructor instead of the copy constructor here even if the compiler doesn't do the NRVO.
> This brings that result if a function returns an object that has an automatic lifespan, the class can be the move-only class. There is no syntax error here. Note that, to mention a copy elision, there must be an object that is brought to life. #Cpp_NRVO #Cpp_CopyElision 
> 
```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(const Myclass&)
	{
		std::cout << "copy ctor\n";
	}
	Myclass(Myclass&&)
	{
		std::cout << "move ctor\n";
	}
};

Myclass foo()
{
	Myclass x;

	return x; // A conversion from the L-value expression to the X-value expression is done by the compiler
}

int main()
{
	Myclass y = foo(); // There is a copy elision here. The default constructor will be called in case of the NRVO. The move constructor will be called in case there is no NRVO.
}

// If the class is a move-only class
class Myclass {
public:
	Myclass() = default;
	Myclass(Myclass&&)
	{
		std::cout << "move ctor\n";
	}
	Myclass& operator=(Myclass&&)
	{
		std::cout << "move assignment\n";
		return *this;
	}
};

Myclass foo()
{
	Myclass x;

	return x; // A conversion from the L-value expression to the X-value expression is done by the compiler
}

int main()
{
	Myclass y;

	y = foo(); // There is no copy elision here because the object is already exist. The move assignment is called here.
}
```

This syntax is valid in C too. So the below code is valid in C:
```c
struct Data {
	int x, y;
};

struct Data foo()
{
	struct Data result{1, 3};

	return result;
}

int main()
{
	struct Data x = foo(); // OK in C.

	x = foo(); // OK in C.
}
```

---
# Terms
- Dynamic memory management
- new operator
- cleanup code
- free
- delete operator
- std::bad_alloc
- smart pointers
- raw/naked pointer
- factory function
- dangling pointer
- array new operator
- array delete operator
- placement new operator

---
return: [[00_Course_Files]]