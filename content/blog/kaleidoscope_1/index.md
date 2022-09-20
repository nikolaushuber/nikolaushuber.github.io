--- 
author: Nikolaus Huber
title: Kaleidoscope Compiler 1 - Setup 
date: 2022-07-21 
abstract: Installing the tools needed to build a small compiler with OCaml and LLVM.  
thumbnail: "drill.jpg"
sidebar: false 
categories: ["Compiler"]
keywords: ["compiler", "llvm", "ocaml", "kaleidoscope"]
draft: true 
---

# Introduction 

Compilers are interesting pieces of software. They are so essential, that most of us 
use terms like programming language and compiler as synonyms, even though they 
are quite different things. 

In this series of posts I would like to walk you through the steps of building a small 
compiler. Our compiler will not be the most advanced, the language it will compiler is 
rather small and non-expressive, however it is possible to write non-trivial programs in it. 

When one wants to start working on a compiler the question of which language to use inevitably arises. 
Most compilers today are written in the langue that they themselves compiler (through a process called 
bootstrapping). 

For our language, we will use OCaml. OCaml is a functional language which is used a lot for tools 
that evolve around programming languages, such as different analysis frameworks (examples ...). 
It is also a good language for implementing compilers themselves. 

Typically, a compiler is divided into two or three major phases:

- The front end
- The middle end 
- The back end 

The front end deals with understanding the program code we write. It must be able to 
take the textfile we use to write down our code and convert it into a datastructure that 
the compiler can then further transform into executable code. 

The middle end is usually used for optimizations. Here we transfer the datastructure we got 
from the frontend in order to make the code better (for an arbitrary definition of the word better :P) 

The back end is responsible for transforming the (possibly optimized) code into something that can be
executed by the computer. There there are two different approaches. Either we transform our code into 
some form of assembly language (i.e. something that is very close already to the machine code that can be 
executed by the computer), or we translate it into instructions for a virtual machine (i.e. a kind of made up 
CPU that is itself emulated through a program that is usually called interpreter). 

Examples for languages which are usually transformed into assembly are C and C++. The later group 
consists of langauges such as Python and Java. While both have their advantages and disadvantages, I will 
not go into detail here about these. For our language, we will use the first approach. 

The reason for dividing up a compiler into different phases (or ends) is twofold. First, it simplifies and structures 
the code nicely. Additionally, it allows for reusuing parts of the compiler. Imagine you want to later on add another 
programming language that your compiler can understand and translate. If you can succeed in transforming the input 
text into the same type of datastructure you used to communicate between the front end and the middle end in your original
compiler, you can then just reuse the middle end and back end! 

This is, where LLVM comes into the picture. LLVM is a framework for implementing compilers. It gives us a socalled intermediate 
representation (IR), which you can think of as the communication data type between the front end and the middle end. As long as 
we can translate our programming language into the LLVM IR, we can then use the middle end and backends provided by LLVM. 

This is another reason why I chose OCaml as the implementation language for our compiler. The LLVM project currently keeps 
four official APIs around, one for C/C++, one for GO, one for Python, and one for OCaml. 

This tutorial is not intended to teach you everything there is to know about compiler engineering. Moreover, 
it is not an indroduction to or even a demonstrations of best practices in software design. I will go over 
most topics quite quickly, so if you have any questions along the way please consult your preferred compiler engineering book :) 

Without further ado, lets start by getting all the tools installed that we need. 

# Installing required tools 

The first thing we need is the OCaml compiler. The best way of installing OCaml is to use OCaml's package manager opam.
Opam allows us not only to install different versions of OCaml, but also to have complete toolchains (i.e. compiler + packages) for 
individual projects. If you know Python think of opam like a combination of pyenv and virtualenv. 

To start, install opam as outlined here ... . 
Then we can create a local switch (i.e. a toolchain specific to the project we are interested in):

```bash 
mkdir kaleidoscope 
cd kaleidoscope 
opam switch create . ocaml-base-compiler.4.14.0
opam install dune utop menhir 
```

