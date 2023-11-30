# Mid

Author: Zhaojiacheng Zhou

---

## Linux

`ls -l` long format and their meaning.

```bash
total 8
-rw-r--r--  1 zhouzhaojiacheng  staff  153 Nov  4 21:29 mid.md
```

`diff` compare two files: `a` for add, `d` for delete, `c` for change, `<` for first file, `>` for second file.

```bash
10a11,12
> test
> 
15,16d16
< 
< `diff` compare two files
```

## Compile

### Flags

- `-o` indicates the name of output file.
- `-Wall` turn on all warnings
- `-g` put debugging information into the executable file
- `-c` object code.

### Object code

Portion of machine code that has NOT yet been linked into a complete program. Just machine code for one particular library or module.

Split a large project into small files can

- Speed up compilation – changing a single line only requires recompiling a single small source file. Much faster!
- Increase organization – make it easier for you to find functions, variables, etc.
- Facilitate code reuse
- Split coding responsibilities among programmers.

In C++, the preprocessor replaces each `#include` by the contents of the specified file.

Head guard is used to avoid reprocessing the contents of a header file if the header has already been seen. (multi-declaration)

```cpp
#ifndef TEST
#define TEST
//some content
#endif
```

### Advantage and Disadvantage of compiling the cpp files separately

- Advantage: Only changed files need to be recompile.
- Advantage: It facilitates code reuse.
- Disadvantage: It requires a lot of typing!
- Disadvantage: It requires us to remember which files have been changed

Thus, we use makefile to solve this problem.

## C++ Basics

lvalue and rvalue

Function declaration (or function prototype)

- Shows how the function is called.
- Must appear in the code before the function can be called.
- Syntax:

  ```cpp
  Return_Type Function_Name(Parameter_List);
  //Comment describing what function does
  ```

Function definition

- Describes how the function does its task.
- Can appear before or after the function is called.
- Syntax:

```cpp
Return_Type Function_Name(Parameter_List)
{
//function code
}
```

Tells:

- return type
- how many arguments are needed
- types of the arguments
- name of the function
- formal parameter names

```cpp
double total_cost(int number, double price);
// Compute total cost including 5% sales tax on
// number items at cost of price each
```

### Function Call Mechanism

!!!Pass by value and pass by reference

array is passed by `reference`.

### Pointer

A pointer stores the value variable's address.

### Reference

- Reference is an alternative name for an object.

- Reference must be initialized using an lvalue of the same type.

- There is no way to rebind a reference to a different object after initialization

### Why Pointer/Reference

Reference is more convenient and less likely to make mistakes while pointer is more flexible.

Pointer can work with array and create structures whose size is unknown.

```cpp
arr=&arr[0]
```

### Structs

A compound type.

Use `.` to access members and use `->` when it is a pointer.

## Const

const is indended to avoid magic numbers which reduce the readability and make the code harder to maintain.

- Property
  - Cannot be modified later on
  - Must be initialized when it is defined

However, const reference can be initialized with rvalue while the reference can't.

Pass a const reference can avoid changing variable we don't we want to modify.

### Const Pointer

Value can't change `const T* ptr`

Object can't change `T* const ptr`

Neither can chagne `const T* const ptr`

### Typedef

Give an alias to the type.

The alias can be passed.

You can pass none const pointer to function requires a const pointer but can't pass a const pointer to a function requires non-const.

### Procedural Abstraction

- Abstraction
  - Provides only those details that matter.
  - Eliminates unnecessary details and reduces complexity

- Procedural abstractions, done properly, have two important properties:
  - Local: the implementation of an abstraction does not depend on any other abstraction implementation.
    - To realize an implementation, you only need to focus locally.
  - Substitutable: you can replace one (correct) implementation of an abstraction with another (correct) one, and no callers of that abstraction will need to be modified.

Abstraction is different from abstraction implementation.

- Abstraction foucses on what
- Abstraction implementation foucses on how
- The same abstraction may have different implementation

### Type Signature & Specification

Function is a way of providing procedure abstractions.

The type signature of a function can be considered as part of the abstraction

- Recall: type signature includes return type, number of arguments and the type of each argument.
- If you change type signature, callers must also change.

- There are three clauses to the specification:
  - REQUIRES: the pre-conditions that must hold, if any.
  - MODIFIES: how inputs are modified, if any.
  - EFFECTS: what the procedure computes given legal inputs.

- Functions without REQUIRES clauses are considered complete; they are valid for all input.
- Functions with REQUIRES clauses are considered partial

Modify is needed if any global variable or reference is changed

## Recursion; Function Pointers; Function Call Mechanism

### Enum

## Testing

- End to End Testing
  - Errors made early tend to be pervasive and fixing them requires re-writing a large fraction of the existing program
  - Putting off testing until the program is "finished" increases your workload
- Incremental Testing
  - test individual pieces of your program (such as functions) as you write them

- Advantage of incremental testing
  - You are testing smaller, less complex, easier to understand units.
  - You just wrote the code, so you have a firm expectation of at it should do. If it's broken, it is fresh in your mind, you can more easily fix it

- Five Steps in Testing
  - Understand the specification
    - Read specification
    - Break down into small parts
  - Identify the required behaviors
  - Write specific tests
    - For each of your required behaviors, write one or more test cases that check them
    - To the extent possible, the test case should check exactly one behavior – no more! That way, if the case fails, you know where to start looking.
    - Simple Inputs: “normal” for the problem at hand
    - Boundary Condition: at the edges of what is expected, or formed to exploit some detail of implementation
    - Nonsense: cases are those that are clearly unexpected
  - Know the answers in advance
  - Include stress tests
    - Driver code

Assert and macro

## Exception

We want a means of recognizing and handling unusual conditions in the program at runtime

Eg. open non-existing file or invalid input for partial functions.

Though invalid input can be solved by REQUIRES clauses, it is just a comment and can't enforce the specification.

A better way is **Runtime Checking**

- My Problem
  - Try to “fix” things and continue execution by “enforcing” legitimate inputs from illegitimate ones by
    - either modifying the inputs
    - or returning default outputs that make sense in the context
- Give Up
  - Use assert to terminate the program.
- Your Problem
  - Encode “failure” in the return values.
  - Example: factorial() use 0 to encode negative input.
  - Unfortunately, you often can't encode “failure” elegantly in the return values.
  - For example, list_first() can return any integer, so no special value is available to encode “the list is empty!”.
  - Compared to the other two, this is usually the strategy that you use.
