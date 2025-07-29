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

Background:

* Ranges are abstractions in C++ for iterable sequences, they could be represented as
  * [begin, end) pair of iterators, or
  * a `begin` iterator with a number n for the number of elements, or 
  * a begin iterator with a predicate pred such that pred(p) = true if p is out of range.
  

  
* Views are ways to look at ranges, for example
* filter view

<iframe width="800px" height="400px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:10,positionColumn:2,positionLineNumber:10,selectionStartColumn:2,selectionStartLineNumber:10,startColumn:2,startLineNumber:10),source:'%23include+%3Ciostream%3E%0A//+Type+your+code+here,+or+load+an+example.%0Aint+square(int+num)+%7B%0A++++return+num+*+num%3B%0A%7D%0A%0A%0Aint+main()+%7B%0A++++std::cout+%3C%3C+%22haha%22+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:99.99999999999999,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(),l:'5',n:'0',o:'Executor+x86-64+gcc+15.1+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'0',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>
