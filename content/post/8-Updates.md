+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/starting-the-challenge.jpg"
menu = ""
date = "2017-02-24T20:00:00+02:00"
title = "Starting the challenge"
tags = ["compiler"]
slug = "starting-the-challenge"

+++

As mentioned in the [last blog post]({{< ref "post/7-I_am_done.md" >}}) I am finally able to start my [**challenge**]({{< ref "post/1-A_New_Beginning.md" >}}) for this year. In an [earlier blog post]({{< ref "post/2-Some_Thoughts.md" >}}) I thought about some **key points** that need to be decided before I can finally start. Here is the list with their **short answers**, the long answer is written below:

- In which main **language** will the tools be written?
	- Go
- Will the language be **compiled** or **interpreted**?
	- compiled and transpiled, starting with a transpiler
- What is the **name** of the language?
	- Currently it is lbd (learning by doing), but I am still open for proposals
- What exact **features** will the language support?
	- that depends on the evolution over time
- Will it be **open source**?
	- Yes, it is! ([Github](https://github.com/BenchR267/lbd))

Why did I choose **Go** over **Swift**? This decision is based on multiple thoughts: 

First of all, I know Swift quite well and I work with it **all day**. I totally **love** this language and I am very happy to use it that often. I also think that it would **fit** quite well for a **compiler toolchain**. But by choosing **Go** I get a very **powerful** standard library and extremly powerful **tools** (which had more time to evolve than Swifts). I also get the ability to learn more about the Go programming language. I am familiar with the **syntax** and with some **patterns** but it is always best to create something to learn a new language.

In Go it is also very **easy** to produce binaries for different operating systems. That is not something that **disqualifies** Swift but it is much easier to do in Go and is very useful. Beside that Go does not expect any **source breaking** changes in contrast to Swift. So I do not have to worry about **migrating** to a new version of the language.

![me](/images/gopherme.png)
<center>
*This is me as a Gopher. Create yours at [gopherize.me](https://gopherize.me).*
</center>

Why did I choose to **compile/transpile** the code?

Before answering this question here is a *short* definition of transpiling taken from [Wikipedia](https://en.wikipedia.org/wiki/Source-to-source_compiler):

> A […] transpiler is a type of compiler that takes the source code of a program **written in one programming language** as its input and **produces** the equivalent source code in **another programming language**.

This is especially useful if you want to test your lexer and parser by generating code in a forgiving and simple language like [**Javascript**](https://www.javascript.com). That is the reason why I want to start with that: to get results as **fast** as possible. In comparison to that the compiler generates low level code like **assembler** or **machine code**. I am going to use [**LLVM**](http://llvm.org) as the backend for the compiler. LLVM defines an **interface** to run low level machine instructions on multiple processors.

To be honest there is not that much to say about the **name**. I had some great ideas, mostly [colors](https://en.wikipedia.org/wiki/Lists_of_colors), [gemstones](https://en.wikipedia.org/wiki/User:Miwasatoshi/List_of_gemstones_and_ornamental_stones), [gods](http://www.godchecker.com) or places on earth like [beaches](https://en.wikipedia.org/wiki/List_of_beaches) or [mountains](https://en.wikipedia.org/wiki/List_of_highest_mountains_on_Earth). All of them are **already taken**. But since this language should not **compete** with others it does not matter whether the name is taken already. *Apple hijacked another languages name with [Swift](http://swift-lang.org/main/).* But I actually like the shortcut **lbd** for **learning by doing**. It says exactly why this project even exists and what I want to **achieve** with it. The only thing that bothers be is the similarity to [**lldb**](https://lldb.llvm.org).

The **features** will depend on the language evolving over **time**. I am going to start with a very simple [**MVP**](https://en.wikipedia.org/wiki/Minimum_viable_product) and add more features later. Of course, as I already mentioned [before]({{< ref "post/2-Some_Thoughts.md" >}}), you can follow my progress on [**Github**](https://github.com/BenchR267/lbd). I am open to **feedback** as well. You are also free to fork the project and build on it on your own.

That is all I wanted to say for now. Next week I am going to describe the lexer I already [built](https://github.com/BenchR267/lbd/tree/master/lexer).
