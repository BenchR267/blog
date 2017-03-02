+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/mvp-lexer.jpg"
menu = ""
date = "2017-03-03T20:00:00+02:00"
title = "Writing a lexer"
tags = ["compiler"]
slug = "mvp-lexer"

+++

If you have read [**last weeks blog post**]({{< ref "post/8-Updates.md" >}}) you might also have checked out the [repository of my **compiler** on Github](https://github.com/BenchR267/lbd). After deciding the programming language to use and the name for the repository I started creating the first fundamental files. One of them is the **lexer**. Before diving too deep into the topic here is a definition of **lexical analysis** from [Wikipedia](https://en.wikipedia.org/wiki/Lexical_analysis):

> In computer science, lexical analysis is the process of converting a sequence of characters […] into a sequence of tokens […].

For the compiler it is essential to **understand** the code that is written to be able to support the software engineer and to create executable files. Understanding is something we normally associate with **human beings** and not with computer programs. In this context understanding means to create an **abstraction** that the computer is able to process. I am going to describe this abstraction in a later blog post since it is not important for the lexer. But it is important to know that this abstraction is powered by the [**parser**](https://en.wikipedia.org/wiki/Parsing).

> Parsing […] is the process of analysing a string of symbols, either in natural language or in computer languages, conforming to the rules of a formal grammar.

A parser is taking a bunch of tokens and transforms them to an abstraction, called [**AST**](https://en.wikipedia.org/wiki/Abstract_syntax_tree), by a given set of rules, called **grammer**. The parser of [lbd](https://github.com/BenchR267/lbd) is also going to be the topic of a later blog post. But since tokens are the input of the parser we now have the connection back to our lexer: the lexer is **providing those tokens** to be processed by the parser.

The lexer is the first component that gets in touch with the **raw input** of the user. This input is passed to the lexer as raw **bytes** and gets outputted as **tokens** which describe what a given set of the input represents. Since the tokens length is often not the same length as a bytes length it is the task of the lexer to **cluster** the bytes together in **lexical groups**. One lexical group will later describe one word in the **alphabet** that is used in the grammer of the parser.

Before showing you the possible tokens that the **MVP** can handle, this is a sample part of code written in lbd:

```
add = (a int, b int) -> int {
    return a + b
}

mul = (x int, y int) -> int {
    return x * y
}

a = 5
b = 4
c = add(a, b)
d = mul(a, b)
```

As you can see, the feature set is **intentionally small** to see results as fast as possible. It is only possible to create variables which can be of type **function** or **int**.

A `Token` is defined by its `Position` in the stream, its type and its raw representation in the source code:

```Go
type Token struct {
	Pos  Position
	Type Type
	Raw  string
}

type Position struct {
	Line   int
	Column int
	Len    int
}
```

Both types are defined in a sub-package of the `lexer` package called `token`. `token.Type` is defined as an **enum** which is described in Go as a set of constants of type `int` incremented by the keyword `iota`. The list of all possible values for `token.Type` can be viewed [**here**](https://github.com/BenchR267/lbd/blob/master/lexer/token/token.go#L24).

Since the **lexer** transforms the raw input to values of type `token.Token` I defined the type like this:

```Go
type Lexer struct {
	NextToken chan token.Token

	input  <-chan rune
	curPos token.Position
	buffer tokenizer
}
```

If you are not familiar with Go, the **capitalized fields** in structs are **exported**. That means they can be accessed from **outside** of the package. Also important: `rune` is a built in [Go type](https://blog.golang.org/strings) that describes an [unicode](https://en.wikipedia.org/wiki/Unicode) character. So the interface of `lexer.Lexer` only offers a read only channel of `token.Token`. The lexer is initialized with a Go typical `NewLexer` function which takes a read only **input channel** of runes. A channel is a **stream** of values which is used for safe communication between go routines.

```Go
func NewLexer(inputStream <-chan rune) *Lexer
```

After initializing the caller needs to `Start()` the instance which will **spawn a new go routine** which reads from the input channel. Receiving from a channel is a **blocking operation** until another go routine starts **sending** on that channel. To get started I created two functions to create the needed channels with **ease**: (the implementation can be found [here](https://github.com/BenchR267/lbd/blob/master/lexer/helper.go))

```Go
func StreamFromString(input string) <-chan rune
func StreamFromFile(filename string) (stream <-chan rune, err error)
func CombineStreams(streams ...<-chan rune) <-chan rune
```

When all runes from the input channel are processed and transformed into tokens, the lexer stops the whole process by **closing** the `NextToken` channel. 

The real work of **building tokens** out of the runes is done by an internal type `lexer.tokenizer`. The tokenizer is some kind of **buffer** for the lexer. It holds a slice of runes until appending a new rune does not belong to the current buffered ones. That is important for tokens that need **more than one** rune to be finalized (`==`, `!=`, `return`).

Working with **channels** and **go routines** in Go is **really great**. It supports with a lot of **power** and brings also a lot of **performance** and **security** in communication between different parts of the app.

So far, thanks for reading. Next week I am going to write about the [**REPL**](https://en.wikipedia.org/wiki/Read–eval–print_loop) which is very handy to get instant feedback on written code.
