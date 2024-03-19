#Cpp_Course 
[[00_Course_Files]]

# Overloading the Dereferencing "\*" Operator and the Arrow "->" Operator
#Cpp_OperatorOverloading_DereferenceOperator #Cpp_OperatorOverloading_ArrowOperator
The **dereferencing operator** allows us to access an object at an address. Why do we overload the dereferencing operator?  It has to be intuitive. The class has to be a pointer-like class. The classes that are used like a pointer. We mentioned about the pointer-like classes. There are two types of pointer-like classes these are iterator classes and smart pointer classes.
- The iterator classes hold the location information in the containers.
- The smart pointer classes are used to control the life of the dynamic objects.

Why do we need the classes where the objects of the classes act like pointers when the raw pointers exist?
1. Performing tasks that raw pointers cannot do with function calls.
2. Creating more safe code. For example, throwing an exception can be ensured in case of a wrong situation.

An example task of the raw pointers can't be done. Let's say we have a double-linked list. And we want to access the next element of the linked list with "++ptr" and the previous element of the linked list with "--ptr". Is this possible with the raw pointer? No, it isn't. Because it has to be consecutive.

 The dereferencing operator function can be global. However, the arrow operator function has to be a member function.

"\*ptr" expression is an L-value expression. Therefore the return type of the operator\* function has to be an L-value reference to make it possible.

"unique_ptr" type an object is used as a pointer that controls the life of a dynamic object. The difference between the unique_ptr and the raw pointer is it deletes the dynamic object that the life of it is controlled when the life of the unique_ptr ends.

```cpp
#include <iostream>

class Myclass{
public:
	Myclass() = default;
	Myclass(int x) : mval{x} {}
	~Myclass()
	{
		std::cout << "Myclass Destructor releases resources\n";
	}
	friend std::ostream& operator<<(std::ostream& os, const Myclass& m)
	{
		os << m.mval;
	}
	void set(int x)
	{
		mval = x;
	}
private:
	int mval{};
};

int main()
{
	{
		Myclass mx{35};
		std::cout << mx << "\n";
		mc.set(111);
		std::cout << mx << "\n";
	}

	Myclass* pmx = new Myclass{123};
	std::cout << *pmx << "\n";
	pmx->set(111);
	std::cout << *pmx << "\n";

	delete pmx;
}
```

The dynamic object has to be deleted at the end of its task. We didn't need to make a delete call if the "pmx" was a pointer like a class object (just like unique_ptr). Because the dynamic object was to be deleted at the end of the pointer-like class object's life.

```cpp
#include <memory>


int main()
{
	{
		std::unique_ptr<Myclass> pmx(new Myclass{123});
		// std::unique_ptr<Myclass> pmx = new Myclass{123}; // This is a syntax error because the constructor is explicit and the copy-initialization is prohibited.
		
		std::cout << *pmx << "\n";
		pmx->set(111);
		std::cout << *pmx << "\n";
	}
	std::cout << "In main\n";
}
```

The destructor is called before the "In main" output. First, the destructor of the unique_ptr object is called because it is an automatic lifespan object. Then the destructor of the unique_ptr deletes the dynamic object.

The unique_ptr overloads the dereferencing operator, therefore we could write "\*pmx". And overloads the arrow operator therefore we could write "pmx->set(111)".

> [!note] Note: The unique_ptr class is inside the "memory" header.

The strategy that the unique_ptr implements is called "**unique ownership**" in programming. Unique ownership means only one pointer points to a dynamic object. A second pointer can't point to the object. The first pointer must break its ownership to be able to point the object with the second pointer. #Cpp_UniqueOwnership #Cpp_unique_ptr 

The unique_ptr provides:
- It controls the life of the dynamic object.
- It protects against mistakes in using more than one pointer.
- It gives assurances about exception handling.




AT: 1:25

---
# Terms
- unique ownership

---
Return: [[00_Course_Files]]