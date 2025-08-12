---
title: "CppNorth 2025 Report"
date: 2025-07-24
last_modified_at: 2024-07-24
# excerpt: 
header:
  # image: 
  teaser: /assets/brain_filling.jpg
sidebar:
  - title: "Keywords"
    text: "programming"
---

INCOMPLETE...


Taming a Beast: Using ONNX Runtime in AAA Games by Jean-Simon Lapointe
====

Jean-Simon talks about Ubisoft's journey in incoporating ML in game engines. This is mainly for things that were dominated by traditional AI methods like navigation. The main advantage of ML over traditional methods is that the resulting behavior is more "human" out of the box, without requiring any behavior "patching".

The typical workflow is as follows.

* A team of data scientists train an ML model using their favorite framework.
* Trained model is exported to ONNX format, such a model is essentially a graph with input nodes, operator nodes, and output nodes. For example, the input nodes could be the player's position and the destination, the operator nodes could be matrix multiplication or various mathematical functions, and the output node is the action the bot should take in the game.
* In C++, ONNX runtime uses the exported model to compute things (aka "inferencing").

I liked this talk because it really gave me a good picture of the infrastructure for using ML in "production", where training and deployment of AI are decoupled.

Challanges:

* integrating ONNX with game engine's custom memory management
* for the shipped version, ONNX and all libraries it uses need to be compiled and various licenses checked with legal
* exceptions are not okay in game dev, but ONNX throws exceptions; this can be turned off, but then it would just abort instead in situations with exceptions; the workaround is to add prechecks for all the conditions that would trigger exceptions.

Advanced Ranges: Writing Modular, Clean, and Efficient code with Custom Views
====

I remember the talk to be quite technically advanced so I'll write about what I learned about the subject afterwards instead.

In programming, we often need to iterate over sequences. Ranges in C++20 is an abstraction for sequences, providing a way to operate on them in a declarative and functional manner.

Ranges are usually represented as a [begin, end) pair of iterators, but the C++ standard library provides helpful template specializations that recognize C-style arrays and strings as ranges.
  
The standard library provides [range algorithms](https://en.cppreference.com/w/cpp/algorithm/ranges.html) for operating on ranges. For example, `for_each` is a basic range algorithm that applies a function (possibly with side effects) to each element of the range.

<iframe width="800px" height="400px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:24,positionColumn:2,positionLineNumber:24,selectionStartColumn:2,selectionStartLineNumber:24,startColumn:2,startLineNumber:24),source:'%23include+%3Calgorithm%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cranges%3E%0A%0Astruct+CountEven+%0A%7B%0A++++void+operator()(int+n)+%7Bcount+%2B%3D+(n%2B1)+%25+2%3B%7D%0A++++int+count+%3D+0%3B%0A%7D%3B%0A+%0Aint+main()%0A%7B%0A++++int+arr%5B%5D+%3D+%7B1,+2,+3,+4,+5,+6%7D%3B%0A++++auto+print+%3D+%5B%5D(int+x)%7B+std::cout+%3C%3C+x%3B+%7D%3B%0A%0A++++//+for_each:+a+way+to+invoke+a+function+on+every+element+of+the+range%0A++++//+increment+every+element%0A++++std::ranges::for_each(arr,+%5B%5D(int%26+n)+%7B+%2B%2Bn%3B+%7D)%3B%0A++++//+print+every+element%0A++++std::ranges::for_each(arr,+%5B%5D(const+int%26+n)+%7B+std::cout+%3C%3C+n+%3C%3C+!'+!'%3B%7D)%3B+std::cout+%3C%3C+std::endl%3B%0A++++//+count+the+number+of+even+elements%0A++++auto+%5Be,+c%5D+%3D+std::ranges::for_each(arr,+CountEven())%3B%0A++++std::cout+%3C%3C+%22Counted+%22+%3C%3C+c.count+%3C%3C+%22+even+elements.%22+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g151,compilerName:'',compilerOutShown:'1',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B20',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+15.1+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>

<!-- 
#include <algorithm>
#include <iostream>
#include <ranges>

struct CountEven 
{
    void operator()(int n) {count += (n+1) % 2;}
    int count = 0;
};
 
int main()
{
    int arr[] = {1, 2, 3, 4, 5, 6};
    auto print = [](int x){ std::cout << x; };

    // for_each: a way to invoke a function on every element of the range
    // increment every element
    std::ranges::for_each(arr, [](int& n) { ++n; });
    // print every element
    std::ranges::for_each(arr, [](const int& n) { std::cout << n << ' ';}); std::cout << std::endl;
    // count the number of even elements
    auto [e, c] = std::ranges::for_each(arr, CountEven());
    std::cout << "Counted " << c.count << " even elements." << std::endl;
}
-->

In my opinion, what really makes C++20 ranges stand out are _views_. Views are ways to "look" at ranges; alternatively, think of views as ranges that do not own the data underneath. They are easily composable and performant. Here are some examples.

<iframe width="800px" height="400px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:17,positionColumn:2,positionLineNumber:17,selectionStartColumn:2,selectionStartLineNumber:17,startColumn:2,startLineNumber:17),source:'%23include+%3Calgorithm%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cstring%3E%0A%23include+%3Cranges%3E%0A+%0Aint+main()%0A%7B%0A++++int+arr%5B%5D+%3D+%7B1,+2,+3,+4,+5,+6%7D%3B%0A++++auto+print+%3D+%5B%5D(int+x)%7B+std::cout+%3C%3C+x%3B+%7D%3B%0A++++auto+isEven+%3D+%5B%5D(int+x)%7B+return+x+%25+2+%3D%3D+0%3B+%7D%3B%0A+%0A++++//+take_view:+take+the+first+3+elements%0A++++std::ranges::for_each(std::ranges::take_view(arr,+3),+print)%3B+std::cout+%3C%3C+std::endl%3B%0A%0A++++//+filter_view:+print+out+all+even+elements%0A++++std::ranges::for_each(std::ranges::filter_view(arr,+isEven),+print)%3B+std::cout+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g151,compilerName:'',compilerOutShown:'1',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B20',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+15.1+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>

<!--
#include <algorithm>
#include <iostream>
#include <string>
#include <ranges>
 
int main()
{
    int arr[] = {1, 2, 3, 4, 5, 6};
    auto print = [](int x){ std::cout << x; };
    auto isEven = [](int x){ return x % 2 == 0; };
 
    // take_view: take the first 3 elements
    std::ranges::for_each(std::ranges::take_view(arr, 3), print); std::cout << std::endl;

    // filter_view: print out all even elements
    std::ranges::for_each(std::ranges::filter_view(arr, isEven), print); std::cout << std::endl;
}
-->

How would this allow us to write composable and performant code, you may ask? Well, you may write `std::ranges::take_view(std::ranges::filter_view(arr, isEven), 3)` to compose the two views, giving the first three even elements in the range. Still not convinced? Then consider the pipe syntax.

<iframe width="800px" height="400px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:51,endLineNumber:10,positionColumn:51,positionLineNumber:10,selectionStartColumn:51,selectionStartLineNumber:10,startColumn:51,startLineNumber:10),source:'%23include+%3Calgorithm%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cstring%3E%0A%23include+%3Cranges%3E%0A+%0Aint+main()%0A%7B%0A++++int+arr%5B%5D+%3D+%7B1,+2,+3,+4,+5,+6,+7,+8,+9%7D%3B%0A++++auto+print+%3D+%5B%5D(int+x)%7B+std::cout+%3C%3C+x+%3C%3C+!'+!'%3B+%7D%3B%0A++++auto+isEven+%3D+%5B%5D(int+x)%7B+return+x+%25+2+%3D%3D+0%3B+%7D%3B%0A%0A++++std::cout+%3C%3C+%22The+first+three+even+elements+of+%22%3B%0A++++std::ranges::for_each(arr,+print)%3B%0A++++std::cout+%3C%3C+%22are:+%22%3B%0A++++//+pipe+syntax%0A++++std::ranges::for_each(arr+%7C+std::views::filter(isEven)+%7C+std::views::take(3),+print)%3B+std::cout+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g151,compilerName:'',compilerOutShown:'1',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B20',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+15.1+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>

<!-- 
#include <algorithm>
#include <iostream>
#include <string>
#include <ranges>
 
int main()
{
    int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    auto print = [](int x){ std::cout << x << ' '; };
    auto isEven = [](int x){ return x % 2 == 0; };

    std::cout << "The first three even elements of ";
    std::ranges::for_each(arr, print);
    std::cout << "are: ";
    // pipe syntax
    std::ranges::for_each(arr | std::views::filter(isEven) | std::views::take(3), print); std::cout << std::endl;
}
-->

A reason this code is performant is that views are lazily evaluated. This means when you write `arr | std::views::filter(isEven) | std::views::take(3), print)`, essentially nothing is done. The result is computed on the go as you iterate over it! Moreover, no space is needed to store any intermediate results. 

When using the pipe syntax for views, the things in between bars are called _range adaptors_ (for example, `filter(isEven)` and `take(3)`); applying range adaptors to views result in views. A complete list of range adaptors provided by the standard library can be found on cppreference: https://en.cppreference.com/w/cpp/ranges.html#Range_adaptors.

# C++ Contracts: A Meaningfully Viable Product & Should I Check for Null Here?

These talks really resonated with me because I am a big fan of contracts in programming! When I program, I think about invariants, contracts, and guarantees... When I use an API, I'd like to know what a piece of code can guarantee if I use it correctly. Good news, in C++26, we get contracts!

Without going into too many details, here's a minimal demo of what contracts in C++26 can achieve, taken from cppreference.

```cpp
#include <limits>
#include <cmath>
float tolerance = 10 * std::numeric_limits<float>::epsilon();

bool isNormalized(std::array<float, 3> vector) {
    return std::abs(std::hypot(vector[0], vector[1], vector[2]) - 1.f) <= tolerance;
}

bool isNormalizable(std::array<float, 3> vector) {
    return std::hypot(vector[0], vector[1], vector[2]) > 0.f;
}

// function contract specifiers
std::array<float, 3> normalize(std::array<float, 3> vector)
    pre(isNormalizable(vector))
    post(output: isNormalized(output))
{
    auto norm = std::hypot(vector[0], vector[1], vector[2]);
    return std::array<float, 3> {vector[0] / norm, vector[1] / norm, vector[2] / norm};
}
```

In addition to function contract specifiers shown above, C++26 also supports contract_assert statements, which are basically the same as C asserts syntatically.

In the snippet, the part enclosed by `pre(...)` is the precondition; only the paramters of the function are in scope for the precondition. The part enclosed by `post(...)` is the postcondition; it names the return value `output` and asserts that it must be a normalized vector. Note how we can specify postconditions on unamed return values this way (we did not have to assign the return value to a variable named `output` and then assert before returning inside the function body.).

The exact semantics of these assertions (i.e whether an assertion is checked, and what happens when it fails, etc) can vary per build, as well as per evaluation of assertion, and are implementation defined. A basic reason is so that different builds can have different behaviors. For example, a debug build could use a semantics that checks the asserts and teriminates on violation, while a release build can simple ignore all asserts. But maybe a more important reason is customizability. C++26 provides the ability to specify your own contract-violation handler, which is a function that takes in an object of type `std::contracts::contract_violation`. This object will contain diagnostic information so that you can decide what to do on a contract violation.

## But what about C asserts?
I think the advantage of contracts is precisely that it is a language feature rather than a macro! This leaves the door open for C++ compilers to use static analysis to optimize code (for example, a compiler can interpret an assert as an "assume"). This is also a step towards "code as proof", where we can have safety guarantees by chaining together pre/post-conditions.

I think the proposals paper for contracts is very well-written and provides further context: [P2900R14](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p2900r14.pdf).
