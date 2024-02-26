[[00_Course_Files]]
#Cpp_Course 

 > [!note] Note: About the "endl"
 > "endl" is a function (actually, a function template). These kinds of functions are called "manipulators". "endl" manipulates the output stream and these types of manipulators are called "ostream manipulators". It takes an ostream object as a parameter  "endl(cout)". It gives a new line to the ostream object and flashes its buffer.
 > parameter: std::ostream &
 > return: std::ostream &
 > global function template
 
 An object can have one of the four life spas in C++.
 - automatic storage
 - static storage
 - dynamic storage
 - threadlocal storage (This is also added to C in standard C11)

> [!note] Note: Rule of Zero - A C++ Idiome
> Let the compiler write all special member functions. #Cpp_Terms 

# Copy Assignment - Special Member Function
#Cpp_CopyAssignment
We will continue on the Sentence example that we wrote with the copy constructor topic. What if we make an assignment instead of an initialization?

The below code causes undefined behavior because the assignment operation is done with another special member function which is the "**copy assignment function**". The function is implicitly declared. The same situation happens when we use assignment syntax.
```cpp
int main()
{
	using namespace std;

	Sentence s1 {"Hello message"};

	s1.print();
	cout << "length: " << s1.length() << "\n";

	if(1)
	{
		Sentence s2 {"Welcome message"};
		s2 = s1; // Assigment syntax. The copy assignment function will be called.
		s2.print();
	} // Destructor frees the memory (Destructor is called at the end of the scope)

	s1.print(); // Undefined Behavior. The pointer member of the object is a dangling pointer now. And there is a resource leak too, the allocated area of the s2 object wasn't given back.
}
```

Before modern C++, the name of the function was "**assignment operator function**". Why has this changed? Because one more function which is the "**move assignment function**" was added with modern C++. There was one assignment operator before modern C++. This has changed with the addition of the move semantic to the language. #Cpp11 #Cpp_Terms 

> [!warning]  Warning: This is not a constructor
> Both two of the objects are live. Don't confuse it with the constructors. We were creating objects with constructors.

**How does the compiler write the copy assignment function?**
The compiler writes a copy assignment function when appropriate conditions are provided. It has to be non-static, public, and inline.

```cpp
class Myclass {
public:
	Myclass& operator=(const Myclass&); // The copy assignment operator

private:
	T tx;
	U ux;
	M mx;
};
```

The functions called when a class object is the operand of an operator is called "**operator functions**". The name of the operator functions has to include the "operator" keyword. The copy assignment function is a special member function yet it is an operator function too. It takes const class reference and the returns class reference. 

> [!note] Note: How does the compiler call this function?
> When we write " x = y;" the compiler converts it into "x.operator=(y);" function call. Writing both of them means the same thing.

**How does the compiler write the copy assignment function?**
```cpp
class Myclass {
public:
	Myclass& operator=(const Myclass& other)
	{
		tx = other.tx;
		ux = other.ux;
		mx = other.mx;
		
		return *this;
	}
private:
	T tx;
	U ux;
	M mx;
};
```

**How do we write the copy assignment function to prevent the undefined behavior in the example?**
```cpp
// sentence.h
class Sentence {
public:
	Sentence(const char* p);
	~Sentence();
	Sentence& operator=(const Sentence& other);
	void print()const;
	std::size_t length()const;

private:
	char* mp;
	std::size_t mlen;
};

// sentence.cpp

Sentence& Sentence::operator=(const Sentence& other)
{
	std::free(mp); // Free the own resource
	mlen = other.mlen;
	
	mp = static_cast<char*>(std::malloc(mlen+1));
	if(!mp) {
		std::cout << "malloc failure\n";
		exit(EXIT_FAILURE);
	}
	std::strcpy(mp, other.mp);

	return *this;
}
```

> [!warning]  Warning: We can deduce the meaning of if we are writing the destructor (instead of writing by the compiler), we are probably freeing one or more resources, thus we should write the copy constructor and the copy assignment function. The "**big three**" terms were used to describe that rule before modern C++. (Big three: destructor, copy constructor, operator assignment). After the addition of the move semantic, this changed. Now we can call it like "big five" because two more functions are added, we will look at it. #Cpp_Terms #Cpp11

> [!note] Note: Shallow Copy and Deep Copy
> The copy constructor and the copy assignment functions that the compiler writes make "**shallow copy**". This means they are only copying the pointers if the class has a pointer. However, we wrote these functions instead of the compiler to prevent the mentioned problem. We allocate an area and copy the data to that area. In conclusion, we had two allocated areas and two different addresses. This copying method is called "**deep copy**". #Cpp_Terms 

**Self Assignment Problem**
Assigning an object to itself is called "self-assignment". So, what if we assign an object to itself? This causes undefined behavior. We are freeing the memory first to make another allocation for the copying inside the copy assignment function. So, we are freeing the area that we will copy from. This concludes with undefined behavior. So what to do to solve the problem? We should check object addresses to prevent self-assignment.
```cpp
Sentence& Sentence::operator=(const Sentence& other)
{
	if(this == &other) // We add the self-assignment check
		return *this;
		
	std::free(mp); // Free the own resource
	mlen = other.mlen;
	
	mp = static_cast<char*>(std::malloc(mlen+1));
	if(!mp) {
		std::cout << "malloc failure\n";
		exit(EXIT_FAILURE);
	}
	std::strcpy(mp, other.mp);

	return *this;
}
```

> [!warning]  Warning: First we should check the self-assignment inside the copy assignment function.

**Why the return type of the copy assignment operator is L value reference of the class type?**
1. Because the value category of the expression created with the assignment operator is the L value expression in C++ (unlike C). Note that, the expression created with a function call that the return type is L value reference is L value expression.
2. Because of the chaining.

> [!note] Note: Chaining with the assignment operator is actually related to a C syntax. The associativity of the assignment operator is from right to left.

```cpp
int foo();

int main()
{
	int x,y,z,t;

	x = y = z = t = foo(); // We assigned the return value of the "foo" to all variables. This is valid in C and C++.
}
```

> [!note] Note: About the similarity of the destructor, the copy constructor, and the copy assignment function.
> In general, the destructor, the copy constructor, and the copy assignment functions have similar codes. For example, releasing the resource, deep copy. So we can write functions (as private member functions) for these operations and make common of them.

**Copy Restriction**
There can be classes that have restrictions to copy operations. The designer of the class doesn't want to make the objects of the class copy between them by initializing or assigning. Why the designer can have this kind of approach? Because it can be unmeaningful or cause unwanted results. Some classes of the standard libraries and the third-party libraries have restrictions to copy operations. Examples in the standard libraries are ostream class, mutex class, unique_ptr class, etc. These classes are called "**non-copyable**". The copy restriction feature was added to the language after modern C++. #Cpp11 

A standard library example about copy restriction:
```cpp
int main()
{
	using namespace std;

	ostream mycount = cout; // Syntax ERROR because the ostream class has copy restriction. (Note: ostream is a class and cout is object of the class)
}
```

How a class is closed to copy operations? We can delete the class functions that make copy operations.
```cpp
class Myclass {
public:
	//...
	Myclass() = default;
	Myclass(const Myclass&) = delete;
	Myclass& operator=(const Myclass&) = delete;
};

int main()
{
	Myclass m1; // OK.
	Myclass m2(m1); // Syntax ERROR. We close the copy operations. "Attempting to reference a deleted function" (Copy ctor)
	Myclass m3;
	m3 = m1; // Syntax ERROR. (Copy assignment)
}
```

> [!note] Note: Closing a class to copy operations brings some side effects. For example, it can't be used as pass-by-value.

# Move Semantic
#Cpp_MoveSemantic
Think about an object that is at the end of its life. The destructor will be called. But, if we use the object as a resource when initializing another object or we assign it to another object.

What would happen in this type of situation in old C++? What would happen in this type of situation in old C++? The copy constructor would be called in this situation if the compiler didn't make an optimization. The copy constructor copies the resource. However, the object is at the end of its life and already it will give its resources back. Would it be better if the object gave its resources to the other object instead of copying it?

Taking the resources of an object whose life is about to end is called "**stealing**". This can be done when initializing or assigning to an object with another object that its life is about to end. This is named "**Move Semantic**".

Move semantic is a very important mechanism to improve code efficiency. The stealing of an object's resources that there is no possibility to used by another code instead of copying it, and doing it at compile time is the Move Semantic.

There was no tool to support the Move Semantic before modern C++. Why? The compiler has to understand at the compile time if the life of the objects is ending. However, there was no such tool to make this possible. The R-value references were added to the language to make it possible. #Cpp11 

**Move Semantic and Value Categories**
If the life of the object expression which is on the right side of the assignment operator is ending (nobody can use its value after that), the value category of the expression is the X-value or PR-value. The union set of these is the R-value expression. If the value category of the right side is the R-value expression we have to make a code to steal the object's value, if the value category is the L-value expression we have to copy its value. R-value references added to the language make it possible.

```cpp

void func(const Myclass&r);
void func(Myclass &&);

int main()
{
	Myclass m;

	func(m); // The function that takes L-value reference is called.
	func(Myclass); // The function that takes R-value reference is called.
}

```

Due to function overload rules, we can call different overload functions depending on the value category of the argument. This is the same with the special member functions.
```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(const Myclass&);
	Myclass(Myclass&&);
};

int main()
{
	Myclass m1;

	Myclass m2(m1); // The copy constructor is called.
	Myclass m3(Myclass{}); // The move constructor is called.
}
```

# Move Constructor - Special Member Function
#Cpp_MoveConstuctor
First, we should change the destructor. We add a null-pointer check. (The Sentence example is continuing)
```cpp
// sentence.cpp
Sentence::~Sentence()
{
	if(mp){ // Checks if it is a null pointer.
		free(mp);
	}
}
```

Now, let's write the **move constructor**.
```cpp
// sentence.h
class Sentence {
public:
	Sentence(const char* p);
	Sentence(Sentence&& other); // Move constructor
	~Sentence();
	Sentence& operator=(const Sentence& other);
	void print()const;
	std::size_t length()const;

private:
	char* mp;
	std::size_t mlen;
};

// sentence.cpp

// The move constructor
Sentence::Sentence(Sentence&& other) : mlen(other.mlen), mp(other.mp)
{
	other.mp = nullptr; // Assign "nullptr" to other's "mp"
}
```

If we didn't assign "nullptr" to the other's "mp", when the destructor is called for the other, the resource would be given (Note that, we added a "nullptr" check inside the destructor).

# Move Assignment - Special Member Function
#Cpp_MoveAssignment
The situation with the move assignment is valid with the assignment situations. We will overload the assignment operator function. This is called "**move assignment**".
```cpp
// sentence.h
class Sentence {
public:
	Sentence(const char* p);
	Sentence(Sentence&& other);
	~Sentence();
	Sentence& operator=(const Sentence& other);
	Sentence& operator=(Sentence&& other); // Move assignment
	void print()const;
	std::size_t length()const;

private:
	char* mp;
	std::size_t mlen;
};

// sentence.cpp

// The move assignment
Sentence& Sentence::Sentence(Sentence&& other)
{
	std::free(mp);
	mlen = other.mlen;
	mp = other.mp;
	
	other.mp = nullptr; // Assign "nullptr" to other's "mp"

	return *this;
}
```

> [!note] Note: If we don't write move functions, still we can call copy functions with an R-value expression (or an L-value expression). This is called "**fall-back**". Because we can assign an R-value expression to const L value Reference. So writing move functions is depends on us.

# The Move Function
#Cpp_MoveFunction 
Let's say we have a L-value object and we want to call the move functions instead of the copy functions. Why do we want this? Because if we steal the resources of an object that is not at the end of its life (an L-value expression), it would be undefined behavior if we try to use it. This is normal if we don't use it. Maybe we will assign another value to it. This can be necessary in some situations.

We can do it by making the L-value expression to R-value expression. How can we do the conversion?
- Using static type cast operator. #Cpp_TypeCastOperators 

However, using the static cast operator in this situation
- is open to faults
- is hard to write
- has some corner cases (problems)
Thus, a function template was created. The function is "move".

The move function template is a type conversion function. If we give it an L-value reference it converts it into an R-value reference and if we give it an R-value referent, it returns the R-value without any change. The move function is a compile-time function. The move is inside the "utility header file". A lot of header files make include that header, for example, "iostream".

```cpp
class Myclass {
public:
	Myclass() = default;
	Myclass(const Myclass&);
	Myclass(Myclass&&);

	Myclass& operator=(const Myclass&);
	Myclass& operator=(myclass&&);
};

// With static cast operator
int main()
{
	Myclass m1;

	Myclass m2{ static_cast<Myclass &&>(m1) };

	Myclass m3;
	m3 = static_cast<Myclass &&>(m2);
}

// With "move" function
int main()
{
	Myclass m1;

	Myclass m2{ std::move(m1) };

	Myclass m3;
	m3 = std::move(m2);
}
```

The objects that we stole its resource (with the usage of the "move") are called "**moved-from state**". How do we use an object after we stole its resource? The "moved-from state" is a valid state if we write the code correctly. The object can be used. We don't know the value of the object that is in the moved-from state but it is valid. For example, we can use it after making an assignment to it, or we can use a get function of it. Note that, this guarantee is given for standard libraries.

For example, the standard string library:
```cpp
#include <iostream>
#include <string>

using namespace std;

int main()
{
	string str{"Today is a good day"};

	string s = std::move(str); // str is a moved-from state

	cout << str.lenght() << "\n";
	cout << "[" << str << "]" << "\n";
}

/*
Output:
0
[]
*/
```
So a moved-from state object is valid and consistent but we can't know its value (this is valid for standard library objects, the code has to be written correctly to be consistent )

We mostly don't use a moved-from state object because we moved it. But, some idioms use the moved-from object. For, example:
```cpp
#include <iostream>
#include <string>
#include <fstream>

using namespace std;

void func(string);

int main()
{
	ifstream ifs{ "deneme.txt" };
	string sline;
	vector<string> ;

	while(getline(ifs, sline)) {
		func(sline); // (This line is an alternative to the below line). The object will be copied.
		func(std::move(sline)); // The object will be moved -> look at copy elision.
	}
}
```
The object is copied if we use the L-value expression. However, if we use an R-value expression, the object is moved. There are significant efficiency differences between these situations. There is no problem with using the moved-from state object here because we assign it a new value at every loop. #Cpp_CopyElision #Cpp17 #Cpp_MoveFunction #Cpp_SpecialMemberFunctions  

> [!question] Question: An interview question can be asked related to the upper explanation. #Cpp_Interview_Question 

```cpp
#include <iostream>
#include <string>
#include <fstream>

using namespace std;

void func(string);

int main()
{
	ifstream ifs{ "deneme.txt" };
	string sline;
	vector<string> svec;

	while(getline(ifs, sline)) {
		svec.push_back(sline); // (This line is an alternative to the below line). The object will be copied.
		svec.push_back(std::move(sline)); // The object will be moved -> look at copy elision.
	}
}
```
# Using Move Semantic in Own Implementation
#Cpp_MoveSemantic 
We can implement the move semantic to our implementations. Why do we want to use it? Because we may want to copy or move depending on the value category in our implementations.

```cpp
void bar(const Myclass& r)
{
	Myclass m;

	m = r;
}

void bar(Myclass&& r)
{
	Myclass m;
	
	Myclass = std::move(r); // Don't forger "r" is a L value expression
}

int main()
{
	Myclass m;

	bar(Myclass{});
}
```

> [!warning]  Warning: Don't forget the difference between the value category and the expression
> We have to use the name with the "move" function inside the function that takes the R-value reference. Because the name itself is an L-value reference this concludes with copy instead of move. So, we have to use the "move" to make the move. Using "move" on the name that passed as the R-value reference is not wrong because the function is already called with an R-value reference.

---
# Terms
- copy assignment function
- assignment operator function
- move assignment function
- operator functions
- shallow copy
- deep copy
- big three
- non-copyable class
- stealing
- move semantic
- move constructor
- move assignment
- fall-back
- the move function
- utility header
- moved-from state

---
Return: [[00_Course_Files]]