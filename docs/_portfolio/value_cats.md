---
title: "Value Categories in C++: an attempt to decipher definitions"
date: 2024-08-27
last_modified_at: 2024-08-27
# excerpt: 
header:
  # image: 
  teaser: /assets/brain_filling.jpg
sidebar:
  - title: "Keywords"
    text: "programming"
---

INCOMPLETE...

I've been programming in C++ casually since high school, but I never really got the time to *really* understand it at a deep level. I have finally decided to actually learn ....

# What they are not
Value categories are a property of C++ *expressions*, meaning that the value category is determined at compile time. This is a common point of confusion because the terminology *value* category suggests that it describes *values*, which is dynamic.

# The taxonomy
1. Each C++ expression belongs to exactly one of following categories: *lvalue*, *xvalue*, and *prvalue*. 
2. lvalues and xvalues are together called *glvalues* (for "*generalized* lvalues").
3. xvalues and prvalues are together called *rvalues*.

# Cppreference, 1st try
> * a *glvalue* ("generalized" lvalue) is an expression whose evaluation determines the identity of an object or function;
> * a *prvalue* (“pure” rvalue) is an expression whose evaluation
>   * computes the value of an operand of a built-in operator (such prvalue has no result object), or
>   * initializes an object (such prvalue is said to have a result object);
> * an *xvalue* (an “eXpiring” value) is a glvalue that denotes an object whose resources can be reused;
> * an *lvalue* is a glvalue that is not an xvalue.

There are a few terms in this definition that require clarification. Let's take for granted that we know what an "expression" is. You know, the strings of characters built up in some recursive way defined by the C++ grammar production. 

But what about "identity", "evaluation", and "reuse"? 
1. What's the identity of an object or function? I guess it intuitively means its address in memory?
2. Given that the value category of an expression is determined at compile time, why does its evaluation matter at all (spoiler: it doesn't)?
3. What does it exactly mean to reuse?

Let's start by finding a reference for the term *identity*. Typically, cppreference is a great source. But there is no entry on this. Ugh! 

What about the C++ standard draft? Nope, also not defined. Lukily, Stroustrup somewhat defined the identity of an object in his book. Let's take a short excursion into pre C++17 value categories.

# *Identity*

## The literature, pre C++17
Stroustrup's classic reference book *The C++ Programming Language* explains:
> There are two properties that matter for an object when it comes to addressing, copying, and moving:
> - Has identity: The program has the name of, pointer to, or reference to the object so that it is possible to determine if two objects are the same, whether the value of the object has changed, etc.
> - Movable: The object may be moved from.

Recall that "moving" an object in C++ involves potentially changing it. Basically (paraphrasing Stroustrup), moving is a way to recycle an object's resources by moving its value to another location and potentially leaving the object in a valid but unspecified state.

Letting `i` denote having an identity and `m` movable, he then goes on to explain that lvalues correspond to `i&&!m` (have identity and not movable), xvalues correspond to `i&&m` (have identity and movable), and prvalues correspond to `!i&&m` (have no identity and movable). Therefore, a glvalue has an identity, whereas an rvalue is movable.

## Complete definition
We can naturally extend this definition of identity to functions: the identity of a function is just its address. Convention: non-static member functions are not functions. Note that for an object, we cannot just define its identity to be its address (why? bit-fields!). Hence, name is added as an option for object identity.

Okay! So we have a definition of identity. On to the next term.

# *Evaluation*

The Standard provides only a rough definition of evaluation (https://eel.is/c++draft/basic.exec#def:evaluation):
> Evaluation of an expression (or a subexpression) in general includes both value computations (including determining the identity of an object for glvalue evaluation and fetching a value previously assigned to an object for prvalue evaluation) and initiation of side effects.

This still does not shed light on the issue here: why does evaluation have anything to do with the value category? Short answer: it doesn't, because without evaluating an expression, we can already know whether an evaluation determines identity.

How?

Let's look at the definition of an *expression* in C++. From [[expr.pre]](https://eel.is/c++draft/expr.pre):
> An expression is a sequence of operators and operands that specifies a computation.

This means a C++ expression either has an "outermost" operator (these are called compound expressions) or is a primary expression [[expr.prim]](https://eel.is/c++draft/expr.prim). Note that "operator" includes function calls and overloaded operators.

For compound expressions (special case mention first?: MEMBER (. and .*), cast, A{...} init -> pr, AWAIT, noexcept,sizeof, alignof, new, delete, CONDITIONAL, co_yield, throw, comma), the value category 

literals:
string literals -> lvalue (explain why)
user defined -> see the operator
other -> pr

this -> pr

paren -> dun change nothin

names -> all lvalue except enum and concept specialization Hashable<int>

lambda -> pr

fold -> gone after templ dedu

requires -> pr bool



The most basic type of expression is an [*`id-expression`*](https://eel.is/c++draft/expr.prim.id.general#nt:id-expression), which is always an lvalue expression. This makes sense since the `id-expression` names the thing it refers to. Note that the name of an rvalue reference is an lvalue expression:
```
void f(int&& c) {
  c;  // an lvalue id-expression
}
```



Every other type of expression is built up from 

# Examples
## lvalues
The prototypical example of an lvalue is the **name of a variable** (called its [*id-expression*](https://eel.is/c++draft/expr.prim.id.general#nt:id-expression) in the C++ grammar):
```
int c;

c;    // an lvalue expression
```

It is clear that `c` lives in the memory.

Another example is a **function call whose return type is an [lvalue reference](https://en.cppreference.com/w/cpp/language/reference)**:
```
int& f(); 

f();  // an lvalue expression
```

## prvalues
The main example of a prvalue is a **function call whose return type is not a reference**:
```
int g();

g();  // a prvalue expression
```
Starting in C++17, prvalues no longer moved from,


values! where is it even defined 