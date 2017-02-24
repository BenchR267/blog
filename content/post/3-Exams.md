+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/Exams.jpg"
menu = ""
date = "2017-01-22T23:00:00+02:00"
title = "Exams.."
tags = ["exams", "university", "master", "compiler"]
slug = "exams"

+++

Since I am currently right before **examination period** time at my studies, I'd like to take a moment to talk about something I **learned** during the development of a task for my university. While the task itself was not that **exciting**, I think the overall principle is very nice!

The tool is a **framework** for developing new programming languages! Its name is [**Xtext**](http://www.eclipse.org/Xtext/). While I will propably never use it, I like the **idea** behind it.

When developing a **compiler** or an **interpreter** you need to develop a **lexer** and a **parser** to understand the code you want to compile or run. The lexer is for lexically understanding the code, so its outputs are only **tokens** of the code. It understands that *abc* is an identifier and *for* is a keyword. It also understands that *==* means equal and *5* is a number literal. But it neither understands what the code means nor will it check if it is correct.

The checking of the **syntax** of the code is done by the **parser**. It takes the tokens the lexer produces and checks if those make sense in that order. It checks that **function** calls have the correct number of arguments and that there is a **condition** that produces a *bool* value after the *if* keyword.

The parser is often **hardcoded** and changes in the language mean diving deep into the code of that parser (which is in general very **complex**).

For now I want to describe what **Xtext** does to take the complexity out of the parser to make changes in the language not that complex anymore. Xtext itself is an **own programming language** which makes it possible to describe how a language is structured. It lets you describe that the **condition** needs to follow an *if* **statement** and that a function needs to start with the *fn* keyword.

The framework generates code for parsing and creates an [**AST**](https://en.wikipedia.org/wiki/Abstract_syntax_tree) out of the given code. The **abstract syntax tree** groups the content of the source code in logical groups for better abstraction which is then necessary for machine code generation or the runtime. So a function name, its parameters and their name are logically grouped together.

Here is an example content of a **very basic** xtext file:

```html
Equal:
	Variable '==' Variable
;
Variable:
	identifier = ID | number = INT
;
```

**ID** and **INT** are terminal symbols, so they are predefined. **ID** in that case is the same as `/([A-Za-z_0-9]+)/g`, so any letter, the underline symbol or number, **INT** is just `/([0-9]+)/g`, so only numbers.

The [task](https://github.com/BenchR267/prolog-xtext) was to describe the [**Prolog**](http://www.swi-prolog.org) syntax in a Xtext file and to build an AST and another abstraction out of it. The Xtext file can be viewed [here](https://github.com/BenchR267/prolog-xtext/blob/master/de.htwdd.sf.beleg.s76511.s76575/src/de/htwdd/sf/beleg/Dsl.xtext).

This was just a short **introduction** to the tooling and I really consider to write something similiar so that the language will be much more extensible in the future.

That is all for now, thanks for reading and have a nice week!
