[[00_Course_Files]]
#Cpp_Course 
The existence that is created using a class definition is called an "**object**" or "**instance**". Creating act that object is called "**instantiate**".

A class is a definition that describes how can we use that type. We create objects that suits class definition. Instantiate means creating objects that suits that definition. The object that consisted in end of this process is an instance.

So we can consider following expressions are same: class object, class variable, class instance.
# const member functions
#Cpp_constMemberFunctions
const keyword is one of the most important keyword of the C++ programming language. One of the quality measurement of a program is the usage of the const keyword in places that needed a const keyword. This is described with  "**const correctness**" term.

> [!note] Note:  Const Correctness
>Const correctness means that the object that needs to be const must be const.

The objects are divided in to two categories depending on the changeable. These categories are:
1. **Mutable**: Variables that their values can be changed.
2. **Immutable**: Variables that their values cannot be changed.

> [!note] Note: Advantages of using immutable objects
> There are several advantages of using immutable objects. Some of these:
> - Compiler can do better optimization
> - No need to synchronization in parallel programming

---
Can a non-static member function change the object? How would we know this? Remember the first parameter (an address to object itself) is a hidden parameter. Ho can we know a non-static member function is a accessor or a mutator? We declare a member function that the hidden parameter of the function is a const with a const keyword after the function parentheses. We call that function as **const member function**, and we call the function that declared without const keyword as **non-const member function**.

```cpp
class Myclass{
public:
	void func(int, int); // non-const member function
	void foo(int) const; // const member function
};
``` 

> [!note] Note: The First thing we should pat attention when we are creating a public inter face
We must ask that question first is this member function is a accessor or mutator when we are creation a public interface of a class. If the member function don't created for changing the object than we must declare it with const keyword.

> [!note] Note: 
>The const keyword of a member function is actually qualifying the hidden object address parameter. For example:
```cpp
class Myclass{
	public:
	void foo(int) const;
	// Means
	void foo(const Myclass*, int); // This is a representation, "const Myclass*" is the hidden parameter.
};
```

A non-static member function can be either a const member function or a non-const member function.

Compiler controls for const member functions

> [!warning]  Warning: The declaration and the definition of a const member function must contain the "const" keyword
The compiler throws syntax error, if we declare a const member function but we give a non-const member function for its definition, or vice versa. That means a const member function and a non-const member function are totally different from each other. 

```cpp
// Myclass.h
class Myclass{
	public:
	void foo(int) const;
};

// Myclass.cpp
void Myclass::foo(int) // Syntax ERROR. Because the "foo" declared as const.
{

}
```

> [!warning]  Warning:  A const member function cannot change a non-static data member of the class.
> A const member function cannot change any class non-static data member. If the function tries to change, this would be a syntax error.

```cpp
// Myclass.h
class Myclass{
public:
	void foo(int) const;
private:
	int mx;
};

// Myclass.cpp
void Myclass::foo(int) const // Syntax ERROR. Because the "foo" declared as const.
{
	mx = 45; // Syntax ERROR.
	this->mx = 45; // Same as this syntax. We cannot change the data of low-level const pointer.

}
```

> [!warning]  Warning: const only qualifies the object itself.
>The const keyword in a member function declaration is only qualifies the called object address. So following syntax would be legal:
```cpp
// Myclass.h
class Myclass{
public:
	void foo(int) const;
private:
	int mx;
};

// Myclass.cpp
void Myclass::foo() const
{
	Myclass m;
	m.mx = 20; // OK. "m" is a different object. "const" only qualifies the "this" pointer.
}
```

> [!warning]  Warning:  A const member function shouldn't call a non-const member function
> -> A non-const member function of the class can invoke a const member function of the class.
> -> A const member function of the class cannot invoke a non-const member function of the class.
> This is because there isn't implicit type conversion from "const T\*" to "T\*". Let's explain. First look the syntax of how a member function calls another member function of the same class.
```cpp
// Myclass.h
class Myclass {
public:
	void foo() const;
	void func();
};

// Myclass.cpp
void Myclass::func()
{
	foo(); // This is OK. There is implicit type conversion from "T*" to "const T*".
}

void Myclass::foo()
{
	func(); // Syntax ERROR. There is no implicit type conversion from "const T*" to "T*"
}
```
To understand that we need to look verbose syntax (C style) of the member function call. The compiler searches the function name in case of a function call in according to rules that we mentioned. When it find the function as non-static member function than calls that function with hidden pointer of itself. That a non-static member function calls another non-static member function for the object (hidden object address) that which itself called for.
```cpp
void foo(const Myclass*);

void func(Myclass* p)
{
	foo(p); // OK. But vice versa would be the syntax error. 
}
```

> [!warning]  Warning: A non-const member function shouldn't called for a const object
>What if we call a non-const member function for a const object. This would be a syntax error.

```cpp
 class Myclass {
 public:
 	void accessor() const;
 	void setter();
 };

void main()
{
	const Myclass cm;
	cm.setter(); // Syntax ERROR. There isn't implicit type conversion from "const T*" to "T*".
}
```
But of course if we call a const member function for non-const object that wouldn't be a syntax error. Because there is implicit type conversion from "T\*" to "const T\*".

> [!warning]  Warning: Chaining / Fluent API with a const member function
>'this' pointer is a low level const pointer if the non-static member function is a const function, and vice versa. In chaining syntax, we would return '\*this' as reference syntax or 'this' as pointer syntax. So, if the function is a const function, the return value of the function should be type of const object address or reference. This is because, there is no implicit type conversion from 'const T\*' to 'T\*'.

```cpp
// Myclass.h
class Myclass {
public:
	Myclass& foo() const;
};

// Myclass.cpp
Myclass& Myclass::func()
{
	return *this; // Syntax ERROR. 'this' is a low-level const poiner. So, there isn't implicit type conversion from "const T*" to "T*".
}
```
This syntax error situation can be turned into a valid situation with followings:
- Both the function and return type can be const 
- Both the function and return type can be non-const.
The situation is same in pointer syntax.

> [!warning]  Warning: A const member function can be a const overload
>Following syntax is a const overloading.  This syntax is widely used and used in standard library, too. If the object is const than the only viable function is cost function. If the object not const than two functions is viable but non-const is called. If the non-const function not exist than the const function is called with a non-const object.
>Functions are chosen depending on the constness of the object that overloaded function is called for.
```cpp
class Myclass {
public:
	void func()const;
	void func();
};

int main()
{
	Myclass m;
	m.func(); // non-const member function is called.

	Myclass cm;
	cm.func(); // const member function is called.
}
```

>[!example] Example: Const member function overloading in standard library
>'front' and 'back' functions of the vector class are overloaded. If we call these functions with a const vector object, this wold be an error. But if we call these functions with a non-const vector object, this would be ok. But how the compiler knows we called these functions with a const object and throw an error? Because these functions have const overloading. If we call the function 'front' with a const object, the overload with const return type is chosen. So, the function returned a const object and we use it in left side of the assignment operator. That causes the error.

```cpp
#include <vecto>
int main
{
	std::vector<int> vec{ 1, 2, 3, 5, 7 };
	const std::vector<int> vec2{ 1, 2, 3, 5, 7 };

	vec.front() = 99; // OK. The object is a non-const
	vec.back() = 333; // OK. The object is a non-const

	vec2.front() = 99; // Syntax ERROR. The object is a const
	vec2.back() = 333; // Syntax ERROR. The object is a const
	
	for (auto val : vec)
		std::cout <<val<<" ";
}

// The corresponding syntax is 
class vector{
public:
	int &front();
	const int &front() const;
};
```

# The mutable keyword (data member usage)
#Cpp_mutable
There are such member functions that these functions do not change the perceived value/status of the class object in the problem domain. Therefore, from a semantic perspective, it must be a const member function.

Let's assume, we have a data member that represents the call count of the member functions for debug purposes. Is the change in the value of the debug object related to the meaning of the class object in the problem domain? The answer is no. But if we try to change the value of debug object in the const member function that would be syntax error. Here is a conflict between the syntax rules of the language and the semantic side. The debug variable can be changed according to semantic side of the language because it is not related to value of object that in problem domain. But the syntax of the language prohibits this.

```cpp
class Myclass { 
public: 
	void func()const; 
private:
	int debug_call_count = 0; 
};

void Myclass::func()const
{
	++debug_call_count; // Syntax error. A const member function can't change it.
}
```

We had to do some tricks to reconcile syntax and semantics in early times of C++ (before the standards). For example, we were doing type conversion. We need to change the type of 'this' pointer to non-const in this example.

```cpp
void Myclass::func()const
{
	++const_cast<Myclass *>(this)->debug_call_count; // In early C++
}
```

But we never do it like that. There is a keyword used for this purpose. This keyword is '**mutable**'.  This keyword indicates the value of the variable is not related to value of class object. This indication is for compiler and the code reader. A mutable variable can be changed by non-const member functions.

```cpp
class Myclass { 
public: 
	void func()const; 
private:
	mutable int debug_call_count = 0; 
};

void Myclass::func()const
{
	++debug_call_count; // OK. The variable is mutable.
}
```

> [!question] Question: What is meaning of 'mutable' keyword when we use it for a class data member?
> A: We declare to the compiler and the reader that, this variable member of the class is not related to value of the class (value in the problem domain). So, the compiler don't give syntax error in case of value change of this variable in const member function.
> #Cpp_Interview_Question 

> [!warning]  Warning: Mutable is a overloaded keyword
> Mutable is overloaded keyword. There is another use case of 'mutable' keyword, too. But we will mention it later.

# One Definition Rule - ODR
#Cpp_ODR

ODR is an acronym. ODR means "**One Definition Rule**". Defining an presence more than one causes a syntax error or a undefined behavior depending in the situation.  ODR means one definition for each presence.

"Declaration" and "definition" are different concepts in C and C++. Declaration can be made more than once, but the definition has to be unique (For each presence). But if we define a presence more than once, it will be a syntax error or undefined behavior depending on the usage. So breaking the one definition rule is causes a syntax error or a undefined behavior.

```cpp
class Myclass {
	// ...
};

class Myclass { // Syntax ERROR. Same definiton of one object
	// ...
};
```

If we define a presence more than one time in a source file (means same scope - global scope), it will syntax error. The compiler has to see the breaking the one definition rule to give a syntax error.

But if we define a presence two times in different source files, it will not a syntax error. Because the compiler compiles each source files separately. But we break the one definition rule. This is undefined behavior.

**Consequences of breaking one definition rule:**
1. Syntax error (If definitions are in same source files)
2. Undefined behavior (If definitions are in separate source files)

> [!note] Note: ODR and static
> If we define the object as static, it won't lead undefined behavior

> [!warning]  Warning: Don't define a function in a header file
> Don't define a function in header file, it leads to function definition in each source file that included the header. It will leads to undefined behavior. The linker can give error or not depending on working environment, it is undefined behavior in every case.

**EXCEPTIONS TO ONE DEFINITION RULE**
The rules of the language says there are some exceptions of the one definition rule. And this exceptions are used frequently.

There are entities that, although defined in more than one module within the project, do not cause undefined behavior if their definitions are the same **token-by-token**. The linker is guaranteed to see at worst one of these definitions from the linking phase.

The rules for ODR exception:
1. The definitions have to be in different source files.
2. The definitions have to be same token-by token.

```cpp
// Myclass.cpp
class Myclass{
public:
	void func(int);
	void foo(int);
};

// main.cpp
class Myclass{ // There is no undefined behavior. The definitions are same token-by-token and in different files.
public:
	void func(int);
	void foo(int);
};
```

```cpp
// Myclass.cpp
class Myclass{
public:
	void func(int);
	void foo(int);
};

// main.cpp
class Myclass{ // UNDEFINED BEHAVIOR. Because the definitions are not same token-by-token
public:
	void func(int);
	void foo(int);
	void g(int);
};
```

 **CONCLUSION**
 If an entity has this status, defining these entities in the header file does not create undefined behavior. Because every source file include same header and same definition. That means every instance of the definition is same token-by-token in source files.
 
Due to this rule we get the right to define some entities in the header file.

**WHICH ENTITIES DO NOT CAUSE UNDEFINED BEHAVIOR IF WE DEFINE THEM INSIDE A HEADER FILE**
1. class definitions
2. inline function definitions
3. inline variables (since C++17)
4. constexpr functions (Because, implicitly inline)
5. template codes (All type of templates. class templates, function templates, variable templates, type templates etc.)

> [!warning]  Warning: What we cannot do due to ODR?
> - We cannot define a non-inline function in header file.
> - We cannot define classes that has same name but has different token syntax in different source files.

>[!note] Acronym
>C++ has wide acronym library. Acronyms are abbreviations that created with first letter of the expressions.
>Some C++ acronyms
> - AAA: Almost Always Auto. Describes a coding style
> - ADL: Argument Depended Lookup
> - EBO: Empty Base Optimization
> - SFINAE: Substitution Failure Is Not An Error

# Inline Expansion & Inline Functions
## Inline Expansion
#Cpp_InlineExpansion
C and C++ compilers are optimizing compilers. These compilers have a optimizer module. **Inline expansion** is a optimizing method.

The compiler writes a function reference to linker and it is called **external reference**. The compiler can directly use the function code instead of giving reference to the linker if the compiler knows the function definition in case of a function call. The linker step is eliminated in this situation. The resulting code becomes more effective due to optimization.

The technique where the compiler eliminates the linker and uses the compiled code of the function at the location of the function call is called "inline expansion".

The questions for inline expansion are:
1. Can it done?
2. Is there a benefit?

The compiler have to see the definition of the function to make inline expansion.

Another question is if the compiler have capability to make inline expansion? Some compilers can do that and some others can't. The compilers have switches, and have different switches for inline expansion optimization, too. This switches can differ between compilers, for example there can be a switch for always use inline expansion. Or there can be switch for never inline expansion.

Another point is inline expansion can't be possible for every function even if the compiler has ability to do it. For example the function can be so complicated (For example if there are a lot of recursive calls and nested loops inside the function).

Can we get benefit of a specific inline expansion? This question is hard to answer, because there are a lot of factor. For example we think the effectiveness is increased in case of inline expansion but more costly code can be created due to factors we don't understand. There can be situations the inline expansion bring disadvantage instead of benefit.

So it can't be possible to say inline expansion bring benefit every time. But we can say getting benefit from inlining can be possible with the functions that have few statements and called frequently.

Ideal situation is taking the decision to make inline expansion by compiler. Because it is not possible to say every time it is beneficially. Already the compiler can take decision to make inline expansion by take charge of the situation, even if we don't give a instruction for inline expansion.
## Inline Functions
#Cpp_InlineFunctions
We use **inline keyword** before the return value type of a function to define inline function. We tell to the compiler make inline expansion in case of a function call if it is possible. This is not an obligation to the compiler make a inline expansion. The compiler can make inline expansion on a function that is not stated with inline keyword, too. Or cannot make inline expansion on a function that is stated with inline keyword. The compiler make an analysis about inline qualified function to determine if is there a benefit of making inline expansion and can decide to not make a inline expansion. In conclusion, the compiler can take decision better than us about making a inline expansion.

**Importance of Inline Keyword**
So what is the meaning of using inline keyword if the compiler doesn't care about it. The importance of using inline keyword is we create a situation that don't break the one definition rule. In other words, no matter how many code files an inline expansion definition is in, only one will be seen at the linker stage. This means we can put the inline functions inside the header files.

```cpp
// something.h
inline int foo(int x, int y)
{
	return x*x - y*y; // Don't break the ODR. More than one source code can include it without undefined behavior
}
```
Let's say the compiler didn't make inline expansion for this inline expansion. But anyways, it is guarantee to the linker sees only one compiled function code in linkage phase.

**Separate the two meaning of inline keyword.**
1. Compiler does inline expansion if it finds this directive appropriate. This is lost its meaning. Because it can take this decision itself, if it sees the definition.
2. A inline qualified function can be defined inside header files and it isn't break the ODR rule. Because the linker sees only one definition at linkage phase. This is guaranteed. (Important one)

**Where is inline keyword used?**
One of the most applicant for inline functions is that member functions of the classes that has few process like a simple get function. Making inline expansion in these function is important. But the compiler has to see the code for making inline expansion. Compiler can see it if we define the function inside the header file.

Compiler can't make inline expansion if we don't define the functions inside header file because the compiler can't see the function definitions at compile time. The example code:
```cpp
// Myclass.h
class Myclass{
public:
	void set(int);
	int get()const;
private:
	int mx;
};

// Myclass.cpp
#include "Myclass.h"

void Myclass::set(int x) // Not inlined. Other source files can't see the definition
{
	mx = x;
}

int Myclass::get()
{
	return mx;
}

// main.cpp
#include "Myclass.h"

int main()
{
	Myclass m;

	m.set(12);
	auto m.get();
}
```

We have to show the functions definitions to the compiler to give making inline expansion change to the compiler. We take the functions to header file and make them inline. We can write "inline" keyword at declaration or definition or both of them.
```cpp
// Myclass.h
class Myclass{
public:
	void set(int);
	int get()const;
private:
	int mx;
};

inline void Myclass::set(int x) // OK Inlinde. We can give the "inline" keyword at definition or decleration or both of them
{
	mx = x;
}

inline int Myclass::get()
{
	return mx;
}

// main.cpp
#include "Myclass.h"

int main()
{
	Myclass m;

	m.set(12);
	auto m.get();
}
```

The following syntax is undefined behavior because the functions aren't inline.
```cpp
// Myclass.h
class Myclass{
public:
	void set(int);
	int get()const;
private:
	int mx;
};

void Myclass::set(int x)  // UNDEFINED BEHAVIOR. The ODR rule is broke. Functions are not inline and the definitions of them are inside the header file
{
	mx = x;
}

int Myclass::get()
{
	return mx;
}

// main.cpp
#include "Myclass.h"

int main()
{
	Myclass m;

	m.set(12);
	auto m.get();
}
```

> [!note] Note: constexpr functions and function templates are implicitly inline.
> We can define a constexpr function or a function template inside a header file without the "inline" keyword because they are implicitly inline. We can use the "inline" keyword with them but this is unnecessary. But the inline expand meaning (not related one with the ODR) of the keyword in valid in case of usage it. Since constexpr function is not need inline expansion in case of constexpr usage, it is meanless in that case. But, it can also used as non-constexpr function. In non-constexpr function usage "inline" keyword can make sense if the compiler cares about it.

**There are two way to define a member function inline.**
1. We define the function inside the header file but outside the class definition. But we have to use "inline" keyword at the definition or declaration or both of them.
```cpp
// Myclass.h
class Myclass{
public:
	void set(int);
	int get()const;
private:
	int mx;
};

inline void Myclass::set(int x)
{
	mx = x;
}
```

2. We define the function directly inside the class definition. We don't need to use "inline" keyword with that syntax. But we can use the keyword too. No matter if we use the "inline" keyword or not, the functions are inline. Defining inline functions inside class definition is more readable.
```cpp
// Myclass.h
class Myclass{
public:
	void set(int) // This function is inline. No need to "inline" keyword.
	{
		mx = x;
	}
	int Myclass::get()
	{
		return mx;
	}
private:
	int mx;
};
```

**How do we determine to give a function definition inside the header file as inline?**
The answer depends on the situation. Let's look at some considerations about making a function inline.
**Implementation details will be exposed:** Heaving the code in the header file means the code is "exposed". This means exposing the code to the outside. Inline functions leak the implementation details. This can be important or not depending on the situation.
**Dependency is increased:** The inline function can be using another data type and we have to include the header file that defines the used type in our header files. In this case, we increase the dependency. The files that include our file will include the header file of the data type and files that the data type's header file make include.

**HEADER ONLY LIBRARY**
Header-only library means there is no code file associated with the header file or a compiled version of that code file. The client code can use the library just by including the header file. The file that the client code needs for using the library is only that header file. If all the member functions are inline, and the global functions are inline, the ODR is not violated. These are design choices and there is not one right way or the best way.

**Which class functions can be defined as inline?**
- non-static member functions of the classes
- static member functions if the classes
- global functions
- 'friend functions' of the classes

# Constructor and Destructor 
There are two important non-static member functions. These functions are 'constructor' and 'destructor'. Constructor is used to bring the class object alive. Destructor is used to end the life of the class object. The constructor has to be called to bring a class object to life, and the destructor has to be called to end the life of the class object. There is no other way to create or destroy a class object.

> [!note] Note: RAI Idiom
**Resource Acquisition is Initialization - RAI**
'RAI' is an idiom that means objects are brought to life with a constructor and end their life with a destructor.
#Cpp_RAI
## Constructor
#Cpp_constructor
The creation of a class object occurs with a call to a class function. This function is a non-static member function and is called '**constructor**'. There is a constructor that is called to bring a class object to life, not the other way around. The constructor has a different state and different syntax rules.

```cpp
class Myclass{

};

int main()
{
	Myclass m; // Constructor is called.
	Myclass a[10]; // Constructor is called 10 times for 10 objects.
}
```

**The constructors are subjected to some special rules.**
1. Name of the constructor: We cannot choose the constructor's name. The constructor has to bear the same name as the class.
2. Return concept of the constructor: There is no return concept of the constructor. That doesn't mean there is no return value of the constructor. We have to leave empty the return type of the constructor.
3. The constructor has to be a non-static member function of the class. (There is a static constructor concept in some other programming languages, but there is no such concept in C++).
4. Constructor can't be const.
5. Constructor cannot be called like other member functions.

> [!note] Note: Return concept and return value
> Return concept and return value are different things in C++. It can be possible that a function has no return concept but has a return value. For example, constructor and destructor.

A class object has storage in the memory and can have data elements. A class object has to be ready to do its job to start being used by client codes. The thing that makes a class object ready to use is the constructor. The constructor initializes the non-static data members of the class. The constructor can even do some jobs if some jobs are needed to use the constructor. For example, these jobs can be creating a log file if the class object needs to use it, connecting a database, or setting a serial port.

We have to write a constructor for the class that we write. If we don't write a constructor, some other rules of the language will be in charge, we will mention them later.

The constructor can be overloaded and have different parametric structures. The function overload rules are valid for the constructor.

```cpp
class Myclass{
public:
	Myclass();    // Constructor
	Myclass(int); // Constructor
	void func(int);
};

int main()
{
	Myclass m; // Constructor is called.

	m.func(12);
	m.Myclass(); // Syntax error. A constructor cannot be called like other non-static member functions.
}
```

The constructor can be public, private, or protected. The access control is valid for a constructor. We can make the constructor private, but if we write a code that the client code calls the constructor, this concludes with a syntax error. Are there cases in which we choose to make the constructor private? Yes, there are. We can choose to make the constructor private in the implementation of some design patterns. But these cases are rare. Note that, member functions of the class can access the private constructor.

```cpp
class Myclass{
	Myclass(int);    // Constructor. OK, a constructor can be private.

	void foo()
	{
		Myclass m(23); // OK. 'foo' has rights to access the constructor.
	}
};

int main()
{
	Myclass m(12); // Syntax ERROR. It gets caught by access control because the constructor is private.
}
```

### Default Constructor
There is a special constructor overload called 'default constructor'. A default constructor is a constructor that has no argument or all of the arguments take the default value. This means the default constructor can be called without an argument.

```cpp
class Myclass{
	Myclass();    // Default constructor, has no argument.
};

class Myclass2{
	Myclass(int = 10);    // Default constructor, all of the argument has a default value.
};
```
 
## Destructor 
#Cpp_destrunctor
A non-static member function of the class is called to end the life of the class object. This function is called '**destructor**'.

The destructor is subjected to the same special rules that are mentioned with the constructor.

A destructor can't be overloaded and can't have parameters.

The destructor can be public, private, or protected. This is the same as the constructor.

The destructor has a '~' character in front of its name.
```cpp
class Myclass{
	Myclass(); // Constructor
	~Myclass(); // Destructor
};
```

The destructor can be called by its name, unlike the constructor. But don't do this. Calling the destructor is valid for only very special cases.

---
# Terms
- object
- instance
- instantiate
- const correctness
- mutable
- immutable
- const member function
- non-const member function
- mutable keyword
- ODR "One Definition Rule"
- token-by-token
- acronym
- inline expansion
- inline keyword
- header-only-library
- include dependency
- constructor
- destructor
---
Return: [[00_Course_Files]]