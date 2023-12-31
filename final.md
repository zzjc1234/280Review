# Final

Author: Zhaojiacheng Zhou

---

## Subtypes; Inheritance; Virtual Functions

### Subtype

- Notation
  - `S<:T`: S is subtype of T
  - `S>:T`: T is supertype of S

- Substitution Principle

  Suppose S is a subtype of T, then for any instance where an object of type T is expected, an object of type S can be supplied without changing the correctness of the original computation. (In other words, code written to correctly use T is still correct if it uses S)

  **Subtype can be called by any function which originally call the supertype.**

- Caution

  Subtypes are different from the notion of "type-convertible". If you use a subtype where a supertype is expected, it is not converted to the supertype.

### Creating Subtypes

1. Add one or more operations.

   Any code using the original supertype expects only the "old" methods, which are still available. The new method makes no difference.

2. Strengthen the postcondition of one or more operations.
   - The postconditions of a method are formed by two things: The EFFECTS clause and its return type.
   - One way to do so is to promising everything you used to, plus extra.
3. Weaken the precondition of one or more operations.
   - The preconditions of a method are formed by two things: The REQUIRES clause and its argument type.
   - One way of weakening the precondition is to weaken the REQUIRES clause.

### Inheritance

- Private

  Only accessible for current class

- Protected

  Accessible for current class and derived class

- Public

  Accessible for all

- Private Inheritance

  All members of the base class are private in the derived class. For private attributes, you can only access them with public method of base class (now private method of derived class).

- Public Inheritance

  All public members of the base are also public in the derived class; all private members of the base are also private in the derived class

- The Disadvantage of Protected

  If we change the implementation of base class, it might make method of derived class fail while the program can compile.

- Substitution Principle for subclasses
  - Strengthen the postconditions

  The new method must do everything the old method did, but it is allowed to do more as well.

  - Weaken the preconditions

  It must require no more of the caller than the old method did, but it can require less.

- Override of previous method

  The program will choose the method based on the **apparent type**.

  To solve this problem, we introduce virtual function.

### Virtual Function

- Mechanism

  - When we define a virtual function, we will generate a vtable (virtual table)
  - Each instance of a class with virtual methods has both the class' state, plus a pointer to the appropriate vtable
  - Thus, when we call the virtual function, the code will check the actual type of the object and calls the right function at runtime

## Interfaces; Invariants

### Interfaces

- Problem of class definition

  The class definition includes data members and method implementations. However, data members are part of implementation, however, can't separate from the definition. This makes class definition

  - It complicates the class definition, making it harder to read and understand.
  - It communicates information to the programmer that s/he shouldn’t know.

Thus, we need create an "interface-only" class as a base class, from which an implementation can be derived. Such a base class is called an Abstract Base Class, or sometimes a Virtual Base Class,

Because there will be no implementation, we need to declare its methods in a special way:

- Declare each method as a virtual function.
- “Assign" a zero to each of these virtual functions

These functions are called pure virtual functions and are declared not to exist. Think about them as a set of function pointers, all of which point to NULL.

We can't create instance for virtual base class, however, we can create pointer or reference to it.

- The interface (the abstract base class) is typically defined in a public header (*.h) file
- The implementation (the derived class) is defined in a source (*.cpp) file
- Users of the interface only link against (i.e., compile the file into object code and link with other object codes)
- So, a user of the IntSet abstraction never sees the definition for class IntSetImpl.

To give user a implementation of class needed, the *.h file typically includes the following declaration of an access function which provide a pointer pointing to the implementation if only one implementation is needed.

For more implementation needed condition, we need dynamic allocation.

### Invariants

An invariant is a set of conditions that must always evaluate to true at certain well-defined points; otherwise, the program is incorrect. For ADT, there is so called **representation invariant**.

- It describes the conditions that must hold on those members for the representation to correctly implement the abstraction.
- It must hold immediately before exiting each method of that implementation – including the constructor.

For each method of class can assume the invariant is true on entry if:

- The representation invariant holds immediately before exiting each method (including the constructor), and
- Each data element is truly private.

## Dynamic Memory Allocation; Overloading, Default Arguments; Destructor

The variable type in cpp.

- Static
  - local
  - global
- Dynamic

- Syntax and Mechanism

  ```cpp
  // INFO: creates a new space for an integer, and returns a pointer to that space, assigning it to ip
  int *ip = new int;

  // INFO: initialize it to a specific value with an "initializer"
  int *ip = new int(5);
  ```

  The `new` function allocate a memory with sizeof(int) and then we use a pointer to int points to this memory.

  For memory allocated by `new`, we need to use `delete` to destroy it, else it will lead to memory leak.

- Heap and Stack

  - Heap

    The space for objects created via new comes from a location in memory called the heap

  - Stack

    For function calls.

### Dynamic Arrays

Similarly, we can use `new` and `delete` to allocate and destroy memory for array with

```cpp
new int *ia = new int[5]
delete []ia
```

The `new` function will allocate a space to record the array size and memory for array elements.

- Constructor for dynamic array

  For class definition

  ```cpp
  class IntSet {
  int *elts; // pointer to dynamic array
  int sizeElts; // capacity of array
  int numElts; // current occupancy
  public:
  ...
  ```

  ```cpp
  IntSet::IntSet(): elts(new int[MAXELTS]),
  sizeElts(MAXELTS), numElts(0)
  { }
  ```

### Overloaded Constructor and Default Argument

- Function Overloading
  - Two different functions with exactly the same name, but different argument count and/or argument types
  - Compiler tells which function to call based on the actual argument count and types.

One disadvantage of it is that it may cause duplicated code. To solve it, we can use **default argument**

**Caution**: There could be multiple default arguments in a function, but they must be the last arguments

### Destructor

We use destructor to solve the memory leak.

**Caution**:the destructors for any ADTs declared locally within a block of code are called automatically when the block ends.

- When you call delete on an instance of a class with a destructor
  - First the destructor is called (deallocates the array)
  - Then the object itself is deleted

## Deep Copy

### Motivation

Consider condition that we pass a class by value to a function. If there exists a dynamic array in the class. When the copy class is destroyed by the destructor, the memory of dynamic array will also be destroyed which is not we intend to do. Thus we need to introduce shallow copy and deep copy.

- Shallow copy

  If we just copy the "members of the class", we get a shallow copy

- Deep copy

  A full copy of everything.

We use copy constructor to enable deep copy and assignment constructor to enable shallow copy.

### The Rule of the Big Three

- Specifically, if you have any dynamically allocated storage in a class, you must provide:
  - A destructor
  - A copy constructor
  - An assignment operator

### Deep Copy (Copy Constructor)

Copy constructor creates an object of this class by copying from another object of this class

The copy constructor need to

- Allocate an array of the same size as the source set'
- Copy each element from the source array to the new array
- Copy the numElts/sizeElts fields

Since the copy part is also needed by assignment operator, thus we can create a private fucntion to implement it.

```cpp
IntSet::IntSet(const IntSet &is):
    elts(nullptr), sizeElts(0), numElts(0) {
        copyFrom(is);
    }

void IntSet::copyFrom(const IntSet &is) {
    if (is.sizeElts != sizeElts) { // Resize array
        delete[] elts;
        sizeElts = is.sizeElts;
        elts = new int[sizeElts]
    }
    // Copy array
    for (int i = 0; i < is.sizeElts; i++) {
        elts[i] = is.elts[i];
    }
    // Establish numElts invariant
    numElts = is.numElts;
}
```

### Assignment Constructor

Assignment operator copies the contents from one object (source) to another existing object (target)

- Assignment operators binds right-to-left
- Use `()` to change the sequence.

To avoid shallow copy problem, we need to define the assignment operator, which is called operator overloading.

```cpp
IntSet &operator= (const IntSet &is)
```

- Like the copy constructor, the assignment operator takes a reference to a const instance to copy from.
- However, it also returns a reference to the copied-to object.

```cpp
IntSet &operator= (const IntSet &is);

IntSet &IntSet::operator= (const IntSet &is) {
    copyFrom(is);
    return *this;
}
```

#### Why in this way?

The reason for returning reference in a `(*this)` is that we want to enable the chain assignment as well as improve efficiency. E.g. when we write

```cpp
x=y=z
```

The `y=z` will return a reference to y and pass the value to x.

It is better to modify the code as follows:

```cpp
IntSet &IntSet::operator= (const IntSet &is)
{
    if(this != &is)
        copyFrom(is);
    return *this;
}
```

## Dynamic Resizing

### Motivation

Create an object as large as we want.

### Implementation

Create a `grow` function to enable enlarging the object.

1. Allocate a bigger array.
2. Copy the smaller array to the bigger one.
3. Destroy the smaller array.
4. Modify elts/sizeElts to reflect the new array.

However, if we just grow by one, the efficiency is slow. If a client creates an IntSet of capacity 1, and then inserts N elements into it, it will lead to $/frac{N(N-1)}{2}$ times copy.

The solution is grow by 2 times of original size.

## Linked List

When creating a linked list, we need **Big Three** to make sure that

- The invariants hold during object creation.
- All dynamic resources are accounted for

Here are some basic workflow for single linked back end linked list.

### Insert

1. Create a new node to hold the new "first" element
2. Establish the invariants on the new node (setting the value field to v), and the next field to the “rest of the list"
3. Setting the value field to v, and the next field to the “rest of the list"

### Remove

1. Use local variable `victim` to remember the `first` node.
2. Delete the node after it is skipped by first.
3. Use another local variable, result, to remember the result that we will eventually return
4. Throw exception if encounter empty list

### Big Three

We need write a recursive copy helper to handle the copy.

```cpp
void IntList::copyList(node *list) {
    if (!list) return; // Base case
    copyList(list->next);
    insert(list->value);
}
```

### Doubly Linked List

Handle `insertBack` condition more efficiently.

## Template and container

- Container
  - `contain` other objects
  - no intrinsic meaning on their own
- Polymorphism
  - Reusing code for different types

We can use `template` to write polymorphic container.

The template receive a type name as parameter during compilation. The `class T` is a type name instead of a class name.

```cpp
template <class T>
```

To give a representation for the node contained by this List, and that representation must also be parameterized by T.

We can do so by creating a private type, which is part of this class definition

Each method must be declared as a "templated" method and we do that in much the same way as we do for the class definition.

```cpp
template <class T>
void List<T>::insert(T v) {
    node *np = new node;
    np->next = first;
    np->v = v;
    first = np;
}
```

- The `#include` and compiling of templates are a little bit different.
- You should put your class member function definition also in the .h file, following class definition. So, there is no .cpp for member functions
- To use templates, you specify the type T when creating the container object.

### Container of Pointers

For large things, we usualy pass them by reference to save memory.

To avoid the bugs related to container of pointers, one usual "pattern" of using container of pointers has an invariant, plus three rules of use:

`At-most-once invariant`: any object can be linked to at most one container at any time through pointer.

1. Existence: An object must be dynamically allocated before a pointer to it is inserted.
2. Ownership: Once a pointer to an object is inserted, that object becomes the property of the container. It can only be modified through the methods of the container.
3. Conservation: When a pointer is removed from a container, either the pointer must be inserted into some container, or its reference must be deleted after using

Two ways to destroy a container

1. The destructor: Destroys an existing instance.
2. The assignment operator: Destroys an existing instance before copying the contents of another instance.

```cpp
template <class T>
List<T>::~List() {
    while (!isEmpty()) {
        T *op = remove();
        delete op;
    }
}
```

```cpp
template <class T>
void List<T>::copyList(node *list) {
    if (!list) return;
    copyList(list->next);
    T *o = new T(*list->value);
    insert(o);
}
```

### Polymorphic containers

We can create a dummy class (or so-called base class) container so that we can store information of any derived class.

Caution: a virtual destructor is needed so that when we delete the derived class, the destructor of derived class can be called.

```cpp
class Object {
    public:
        virtual ~Object() { }
};
```

We define a container `List`

```cpp
struct node {
    node *next;
    Object *value;
};

class List {
    ...
    public:
        void insert(Object *o);
        Object *remove();
        ...
};
```

Then we define a class `Bigthing` and we use it in the following way.

```cpp
class BigThing : public Object {
    ...
};

BigThing *bp = new BigThing;
l.insert(bp);
```

To handle the typecast problem between base class and derived class, we need to use `dynamic_cast`. `dynamic_cast` will return NULL if typecast fails.

```cpp
Object *op;
BigThing *bp;

op = l.remove();
bp = dynamic_cast<BigThing *>(op);
...
```

And we need to provide a copy constructor to enable a deep copy. However, we can't simply write the copy constructor in the following way

```cpp
void List::copyList(node *list) {
    if(!list) return;
    copyList(list->next);
    Object *o = new Object(*list->value); // BUG: Object does not have a constructor that takes BigThing as an argument
    insert(o);
}
```

To fix the problem, we need o use something called the “named constructor idiom”.

- named constructor: A method that (by convention) copies the object, returning a pointer to the "generic" base class
- The name of this method (again, by convention) is usually “clone”

```cpp
class Object {
    public:
        virtual Object *clone() = 0;
        // EFFECT: copy this, return a pointer to it
        virtual ~Object() { }
};
```

Then we implement clone in `BigThing`

```cpp
class BigThing : public Object {
    ...
    public:
        Object *clone();
        ...
        BigThing(const BigThing &b);
}

Object *BigThing::clone() {
    BigThing *bp = new BigThing(*this);
    return bp; // Legal due to substitution
               // rule
}

void List::copyList(node *list){
    if(!list) return;
    copyList(list->next);
    Object *o = list->value->clone();
    insert(o);
}
```

## Operator Overloading

Redefining the meaning of the operators when applied to objects of class type

- Basic Syntax

  ```cpp
  A operator+(const A &l, const A &r);
  ```

- Most overloaded operators may be defined as ordinary nonmember functions or as class member functions

  ```cpp
  A operator+(const A &l, const A &r);
  // returns l “+” r
  A A::operator+(const A &r);
  // returns *this “+” r
  ```

- Operator Type
  - Unary operator: Only need one operand if non-member function, none if a member function.
  - Binary operator: Two operand if non-member function, one if a member function.

```cpp
class Complex {
  // OVERVIEW: a complex number class
  double real;
  double imag;
  public:
  Complex(double r=0, double i=0); // Constructor
  Complex &operator += (const Complex &o);
  // MODIFIES: this
  // EFFECTS: adds this complex number with the
  // complex number o and return a reference
  // to the current object.
}

// INFO: Member Function
Complex &Complex::operator += (const Complex &o){
    real += o.real;
    imag += o.imag;
    return *this;
}

// INFO: Non-member Function
// BUG: A non-member function access the class attributes
Complex operator + (const Complex &o1,
        const Complex &o2){
    Complex rst;
    rst.real = o1.real + o2.real;
    rst.imag = o1.imag + o2.imag;
    return rst;
}
```

### Friend

The "friend" declaration allows you to expose the private state of one class to another function (and only that function) explicitly.

```cpp
// INFO: A Demo of friend declaration
class foo {
    friend void baz();
    int f;
};
void baz() { ... }
```

We can also declare a class as `friend`.

```cpp
class foo {
    friend class bar;
    friend void baz();
    int f;
};
class bar { ... };
void baz() { ... }
```

- Caution
  - `friend` declaration may appear anywhere in the class.
  - It is a good idea to group friend declarations together either at the beginning or end of the class definition.

```cpp
class Complex {
    // OVERVIEW: a complex number class
    double real;
    double imag;
    public:
    Complex(double r=0, double i=0);
    Complex &operator += (const Complex &o);
    friend Complex operator+(const Complex &o1,
            const Complex &o2);
};
```

### Some special operators

- Overloading Operator `[]`

  ```cpp
  // INFO: 
  const int &IntSet::operator[](int i) const {
      if(i >= 0 && i < numElts) return elts[i];
      else throw -1;
  }

  int &IntSet::operator[](int i) {
      if(i >= 0 && i < numElts) return elts[i];
      else throw -1;
  }
  ```

- Why we need a nonconst version that returns a reference to int?
  - We need to assign to an element through subscript operation eg. `is[5] = 2`;
- Why we need a const version that returns a const reference to int?
  - We may call the subscript operator with some const IntSet objects or within some const member function. Const objects/const member function can only call their const member functions.
  - Furthermore, the return type should be const reference because we cannot use a const object (elts[i] in this case is a const int) to initialize a non-const reference.

- Overloading Output Operator `<<`

  To print out all the elements of class member, we can redefine the operator `<<`

  ```cpp
  ostream &operator<<(ostream &os, const IntSet &is){
      for(int i = 0; i < is.size(); i++)
          os << is[i] << " ";
      return os;
  }
  ```

  `cout << “hello ” << “world!” << endl;` i.e.

  ```cpp
  cout << “hello ”;
  cout << “world!”;
  cout << endl;
  ```

  - operator<< must be a **nonmember function** because the first operand is not of the class type

- Overloading Input Operator `>>`

  ```cpp
  istream &operator>>(istream &is, foo &obj){
      ...
      return is;
  }
  ```

## Linear List; Stack; Queue

### Linear List

- A collection of zero or more integers; duplicates possible
- Supports insertion and removal by position

### Stack

- LIFO access: last in, first out.
- Restricted form of a linear list: insert and remove only at the end of the list

- Method of stack
  - `size()`: number of elements in the stack.
  - `isEmpty()`: checks if stack has no elements.
  - `push(Object o)`: add object o to the top of stack.
  - `pop()`: remove the top object if stack is not empty; otherwise, throw stackEmpty.
  - `Object &top()`: return a reference to the top element

- Array version *not important*
  - Maintain an integer `size` to record the size of the stack.
  - `size()`: return size;
  - `isEmpty()`: return (size == 0);
  - `push(Object o)`: add object o at index size of the array and increment size. Allocate more space if necessary.
  - `pop()`: If isEmpty(), throw `stackEmpty`; otherwise, decrement size.
  - `Object &top()`: return a reference to the top element `Array[size-1]`

- Linked list version
  - `size()`: LinkedList::size();
  - `isEmpty()`: LinkedList::isEmpty();
  - `push(Object o)`: insert object at the beginning LinkedList::insertFirst(Object o);
  - `pop()`: remove the first node linkedList::removeFirst();
  - `Object &top()`: return a reference to the object stored in the first node

- Array
  - not memory-efficient: need to allocate a big enough array
- Linked list with a size data member
  - memory-efficient: a new item just needs extra constant amount of memory
  - All operations are of constant runtime, same as array

### Queue

- Restricted form of a linear list: insert at one end and remove from the other.
- FIFO access: first in, first out.

- Methods of Queue
  - `size()`: number of elements in the queue.
  - `isEmpty()`: check if queue has no elements.
  - `enqueue(Object o)`: add object o to the rear of the queue.
  - `dequeue()`: remove the front object of the queue if not empty; otherwise, throw queueEmpty.
  - `Object &front()`: return a reference to the front element of the queue.
  - `Object &rear()`: return a reference to the rear element of the queue

- Double-ended singly-linked list is sufficient for linkedList

- Array version
  
  To make implementation more efficient, we use a circular array.

## STL

C++ has a standard template library (STL) that provides us with an easy way to define containers

- Divided into three components:
  - Containers: data structures that hold a collection of objects of a specified type
  - Iterators: used to examine and navigate container elements
  - Algorithms: searching, sorting and many others

There are three types of stl containers

- Sequential Containers: let the programmer control the order in which the elements are stored and accessed. The order does not depend on the values of the elements
- Associative Containers: store elements based on their values. The order depends on the value of the elements
- Container Adapters: take an existing container type and make it act like a different type

- There are three sequential containers:
  - vector: based on arrays.
    - Supports fast random access.
    - Fast insert/delete at the back. Inserting or deleting at other position is slow.
  - deque (double-ended queue): based on arrays.
    - Supports fast random access.
    - Fast insert/delete at front or back.
  - list: based on a doubly-linked lists
    - Supports only bidirectional sequential access.
    - Fast insert/delete at any point in the list.

### Vector

To use a vector, include the appropriate header and namespace.

```cpp
#include <vector>
using namespace std;
vector<int> ivec; // holds ints
vector<IntSet> isvec; // holds IntSets
vector<int> v1; //default constructor gives an empty vector
vector<int> v2(v1); // Copy constructor
vector<int> v3(10, -1); // Construct v3 that has n elements with value t
vector<string> v4(2, “abc”);
```

- Method of `vector`
  - `size`

    `size()` return a value of `size_type` corresponding to the vector type.

  - `vector<int>::size_type`
    - A companion type of vector
    - Essentially an unsigned type (unsigned int or unsigned long)
    - Note: not vector::size_type
    - size_type makes the type machine-independent
    - Usualy we convert `size_type` into `unsigned` type e.g. `int`

  - `empty()`
  - `push_back()`
  - `pop_back()`
  - `[]`: changing the value get by `[]` won't change the value of vector. We can change the value of vector using `v[i]=num`. We can't increase the size of vector with `[]`
  - `clear()`: makes vector v empty
  - `v.front()`: Returns a reference to the first element
  - `v.back()`: Returns a reference to the last element in v
  - `v.insert(p,t)`: Inserts element with value t **right before** the element referred to by **iterator** p.
  - `v.erase(p)`:
    - Removes element referred to by iterator p
    - Returns an iterator referring to the element after the one deleted, or an off-the-end iterator if p referred to the last element
    - p cannot be an off-the-end iterator

### Iterator

- Each container type has a companion iterator type which lets us examine elements and navigate in the container.
- Iterators are more general than subscripts: All of the library containers define iterator types, but only a few of them support subscripting
- An iterator is a generalization of pointer. They are pointers to the elements of containers

```cpp
vector<int>::iterator it;
```

- `v.begin()`: return an iterator pointing to the first element of vector
- `v.end()`: returns an iterator positioning to one-pastthe-end of the vector
- If the vector is empty, the iterator returned by begin is the same as the iterator returned by end
- We can access the the element to which the iterator refers with `*iter`
- Increment/decrement operator
  - `++iter`, `iter++`: advance to the next item in vector
  - `--iter`, `iter--`: go back to the previous item
- We use `iter == iter2` and `iter != iter2`: test whether two iterators point to the same data item

We can use const iterator to avoid changing the value.

Vector support `iterator arithmetic`, however, **not all type** support this

```cpp
iter+n, iter-n
```

Similarly, vector support `relational operation on iterator`, however, **not all containers** support relational operation on iterator

### Deque

- Pronounced as “deck”. Means double-ended queue
- Based on arrays
- Supports fast random access.
- Fast insert/delete at front or back.
- To use, `#include <deque>`

- Initialization method
- `deque<T> d`; `deque<T> d(d1)`;
- `deque<T> d(n,t)`: create d with n elements, each with value t
- `size()`, `empty()`
- `push_back()`, `pop_back()`
- random access through subscripting: `d[k]`
- `begin()`, `end()`, `insert(p, t)`, `erase(p)`
- Operations on iterators
  - `*iter`, `++iter`, `--iter`, `iter1 == iter2`, `iter1 != iter2`, etc
- It supports insert and remove at the beginning
  - `d.push_front(t)`: Add element with value t to front of d
  - `d.pop_front()`: Remove the first element in d

### List

- Based on a doubly-linked lists
- Supports only bidirectional sequential access.
  - If you want to visit the 15th element, you need to go from the beginning and visit every one between the 1st and the 15th.
- Fast insert/delete at any point in the list.
- To use, `#include <list>`

- Initialization method
- `list<T> l`; `list<T> l(li)`;
- `list<T> l(n,t)`: create l with n elements, each with value t
- `size()`, `empty()`
- `push_back()`, `pop_back()`
- `begin()`, `end()`
- Operations on iterators
  - `*iter`, `++iter`, `--iter`, `iter1 == iter2`, `iter1 != iter2`, etc.

- Does not support subscripting
- No iterator arithmetic for list
- No relational operation <, <=, >, >= on iterator of list
- Supports insert and remove at the beginning
  - supports insert and remove at the beginning: Add element with value t to front of l
  - `l.pop_front()`: Remove the first element in l

### Choosing Sequential Container

- `vector` and `deque` are fast for random access, but are not efficient for inserting/removing at the middle
  - For example, removing leaves a hole and we need to shift all the elements on the right of the hole
  - For `vector`, only inserting/removing at the back is fast
  - For `deque`, inserting/removing at both back and front is fast
- `list` is efficient for inserting/removing at the middle, but not efficient for random access
  - It is based on linked list. Accessing an item requires traversal

### General Rules of Thumb

- Use `vector`, unless you have a good reason to prefer another container.
- If the program requires random access to elements, use a `vector` or a `deque`.
- If the program needs to insert or delete elements in the middle, use a `list`.
- If the program needs to insert or delete elements at the front and the back, but not in the middle, use a `deque`.
- If the program needs both random access and inserting/deleting at the middle, the choice depends to the predominant operation (whether it does more random access or more insertion or deletion)
