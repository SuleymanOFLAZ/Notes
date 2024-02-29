# Everything You Ever Wanted To Know About Move Semantics
#Cpp_MoveSemantic 

Howard Hinnant - 2014

## Outline
- The genesis of move semantics
- Special member functions
- Introduction to the special move members
- Best practices for the move members
- Details, details...

## How Did Move Semantic Get Started?
To make the fastest vector ever
- It was all about optimizing std::vector<\T> 
- And everything else just rode along on its coattails.

You can learn everything you want to know about move semantics just by looking at vector.
## What is std::vector?
Anatomy of a vector (simplified)

![[001_AnatomyOfVector|700]]

## How does a vector copy?
![[002_VectorCopy|700]]

Allocating and deallocating are very expensive in C++.

## How does a vector move?
![[003_VectorMove|700]]

## How Did Move Move Semantic Get Started?
- Remember these fundamentals about move semantic and vector, and you will have a basic understanding of all of move semantic.
- The rest is just details...

## Special Members
- Special members are those member functions that the compiler can be asked to automatically generate code for.

- They are:
	- default constructor  X();
	- destructor                 ~X();
	- copy constructor.    X(X const&);
	- copy assignment     X& operator=(X const&);
	- move constructor    X(X&&);
	- move assignment    X& operator=(X&&);

The move constructor and the move assignment are added to the language with C++11. && is called double ampersand and r-value reference.

- The special members can be:
	- not declared
	- implicitly declared
	- user declared

- Implicitly declared special members can be:
	- deleted
	- defaulted

- User declared special members can be:
	- deleted
	- defaulted
	- user-defined

- What counts as user-declared?
```cpp
struct X
{
	X() {}         // user-declared
	X();           // user-declared
	X() = default; // user-declared
	X() = delete;  // user-declared
};
```

- What is the difference between not-declared and deleted?
Consider:
```cpp
struct X
{
	template <class ...Args>
		X(Args&& ...args);
	// The default constructor is not declared.
}
```
This thing will take any number of parameters. Important case for this particular topic is a case where this takes zero parameters. When this takes zero parameters that means you are constructing this  X without any parameters which is a default construction. But also you may recall from C++98 or C++03 if you declare any constructor at all the default constructor is not provided by the compiler. It's not declared at all it does not exist. The default constructor is not deleted in this example it is not declared.
- X can be default constructed by using the variadic constructor even though it does not have a default constructor because this user-defined variadic template can get used.

Now if we wanted to do we could user-declare a default constructor.
```cpp
struct X
{
	template <class ...Args>
		X(Args&& ...args);

	X() = default;
}
```
- Now X() binds to the default constructor instead of the variadic constructor.
Now we get into overload resolution when you try to default construct this thing. The overload resolution rules are non-templated function is always preferred. It has a user-declared default constructor.


```cpp
struct X
{
	template <class ...Args>
		X(Args&& ...args);

	X() = delete;
}
```
- Now X() binds to the deleted default constructor instead of the variadic constructor.
- X is no longer default constructible.

So the takeaway here is deleted members participate in overload resolution. Members that are not declared do not participate an overload resolution. 

---

- Under What circumstances are special members implicitly provided?

|         | default constructor | destructor | copy constructor | copy assignment | move constructor | move assignment |
| ------- | ------------------- | ---------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing | defaulted           | defaulted  | defaulted        | defaulted       | defaulted        | defaulted       |
- if the user declares no special members or constructors, all 6 special members will be defaulted.
- This part is no different from C++98/03.
---

|                 | default constructor | destructor | copy constructor | copy assignment | move constructor | move assignment |
| --------------- | ------------------- | ---------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing         | defaulted           | defaulted  | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor | not-declared        | defaulted  | defaulted        | defaulted       | defaulted        | defaulted       |
- If the user declares any constructor, this will inhibit the implicit declaration of the default constructor.

---

|                     | default constructor | destructor | copy constructor | copy assignment | move constructor | move assignment |
| ------------------- | ------------------- | ---------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted  | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted  | defaulted        | defaulted       | defaulted        | defaulted       |
| default constructor | user declared       | defaulted  | defaulted        | defaulted       | defaulted        | defaulted       |

- A user-declared default constructor will not inhibit any other special members.
---

|                     | default constructor | destructor    | copy constructor | copy assignment | move constructor | move assignment |
| ------------------- | ------------------- | ------------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| default constructor | user declared       | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| destructor          | defaulted           | user declared | * defaulted      | * defaulted     | not declared     | not declared    |
- A user-declared destructor will inhibit the implicit declaration of the move members.
- The implicitly defaulted copy members are deprecated.
	- If you declare a destructor, declare your copy members too, even though not necessary.
- The copy constructor and the copy assignment are provided by they are deprecated.
---

|                     | default constructor | destructor    | copy constructor | copy assignment | move constructor | move assignment |
| ------------------- | ------------------- | ------------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| default constructor | user declared       | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| destructor          | defaulted           | user declared | * defaulted      | * defaulted     | not declared     | not declared    |
| copy constructor    | not declared        | defaulted     | user declared    | * defaulted     | not declared     | note declared   |

- A user-declared copy constructor will inhibit the default constructor and move members
---

|                     | default constructor | destructor    | copy constructor | copy assignment | move constructor | move assignment |
| ------------------- | ------------------- | ------------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| default constructor | user declared       | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| destructor          | defaulted           | user declared | * defaulted      | * defaulted     | not declared     | not declared    |
| copy constructor    | not declared        | defaulted     | user declared    | * defaulted     | not declared     | note declared   |
| copy assignment     | defaulted           | defaulted     | * defaulted      | user declared   | not declared     | not declared    |
- A user-declared copy assignment will inhibit the move members.
---

|                     | default constructor | destructor    | copy constructor | copy assignment | move constructor | move assignment |
| ------------------- | ------------------- | ------------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| default constructor | user declared       | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| destructor          | defaulted           | user declared | * defaulted      | * defaulted     | not declared     | not declared    |
| copy constructor    | not declared        | defaulted     | user declared    | * defaulted     | not declared     | note declared   |
| copy assignment     | defaulted           | defaulted     | * defaulted      | user declared   | not declared     | not declared    |
| move constructor    | not declared        | defaulted     | deleted          | deleted         | user declared    | not declared    |

- A user-declared move member will implicitly delete the copy members.
---

|                     | default constructor | destructor    | copy constructor | copy assignment | move constructor | move assignment |
| ------------------- | ------------------- | ------------- | ---------------- | --------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| default constructor | user declared       | defaulted     | defaulted        | defaulted       | defaulted        | defaulted       |
| destructor          | defaulted           | user declared | * defaulted      | * defaulted     | not declared     | not declared    |
| copy constructor    | not declared        | defaulted     | user declared    | * defaulted     | not declared     | note declared   |
| copy assignment     | defaulted           | defaulted     | * defaulted      | user declared   | not declared     | not declared    |
| move constructor    | not declared        | defaulted     | deleted          | deleted         | user declared    | not declared    |
| move assignment     | defaulted           | defaulted     | deleted          | deleted         | not declared     | user declared   |

- A user-declared move member will implicitly delete the copy members.
---
Note: This defaulted means that these functions will be generated only if they are being used.

---

This is C++98/03:

|                     | default constructor | destructor    | copy constructor | copy assignment |
| ------------------- | ------------------- | ------------- | ---------------- | --------------- |
| Nothing             | defaulted           | defaulted     | defaulted        | defaulted       |
| Any constructor     | not-declared        | defaulted     | defaulted        | defaulted       |
| default constructor | user declared       | defaulted     | defaulted        | defaulted       |
| destructor          | defaulted           | user declared | defaulted        | defaulted       |
| copy constructor    | not declared        | defaulted     | user declared    | defaulted       |
| copy assignment     | defaulted           | defaulted     | defaulted        | user declared   |
#Cpp98-03

## What does a defaulted move constructor do?
```cpp
class X
	:public Base
{
	Member m_;

	X(X&& x)
		: Base(static_cast<Base&&>(x))
		, m_(static_cast<Member&&>(x.m_))
	{}
};
```

## What does a typical user-defined move constructor do?
```cpp
class X
	:public Base
{
	Member m_;

	X(X&& x)
		: Base(std::move(x))
		, m_(std::move(x.m_))
	{
		x.set_to_resourceless_state();
	}
};
```

> [!note] Note: Writing the move members are depending on our class. For example, if the member in the upper example was a vector, the default move constructor would be enough for me. Because the default move constructor calls the vector's move constructor and it makes the job.

## What does a defaulted move assignment do?
```cpp
class X
	:public Base
{
	Member m_;

	X& operator=(X&& x)
	{
		Base::operator=(Base(static_cast<Base&&>(x));
		m_ = static_cast<Member&&>(x.m_);
		return *this;
	}
};
```

## What does a typical user-defined move assignment do?
```cpp
class X
	:public Base
{
	Member m_;

	X& operator=(X&& x)
	{
		Base::operator=(std::move(x));
		m_ = std::move(x.m_);
		x.set_to_resourceless_state();
		return *this;
	}
};
```

## Can I define one special member in terms of another?
AT 35
