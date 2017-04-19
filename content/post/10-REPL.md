+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/mvp-repl.jpg"
menu = ""
date = "2017-03-18T14:00:00+02:00"
title = "Fast feedback using a REPL"
tags = ["compiler"]
slug = "first-repl"

+++

In this blog post I would like to write about the REPL which I built to get a *fast* feedback cycle for my [lexer]({{< ref "post/9-Lexer.md" >}}). Before going into the topic, here is a short description of what REPL stands for and what its purpose is. REPL stands for Read-Evaluate-Print-Loop. So in the end it is an endless loop which is waiting for input from the user to evaluate it and print the outcome afterwards.

Mostly every programming language is shipped with a REPL to try out language specific features. [lbd](https://www.github.com/BenchR267/lbd) will also have a REPL to try out its features.

The word 'evaluate' is broadly defined in this context. When the language is usable it will mean that you are able to define variables and functions and execute them to get results back. At the moment this evaluation process is a lot less complex since the parser and all the following stuff is not written yet.

In this implementation the REPL only accepts lines of lbd-code and prints out the tokenized version of this line produced by the [lexer]({{< ref "post/9-Lexer.md" >}}). This is of course nothing useful when the compiler has all needed functionality but it is quite helpful when testing the lexer, especially when new features are added. So I think about adding a flag which starts the REPL in different modes, one of them would be the lexer only mode to test tokenizing. If tokens are not produced correctly it could cause some serious bugs in the compiler that are quite hard to catch.

So, lets get into the code! ```repl``` is a new package inside the ```lbd``` package (like ```lexer```). It has only one function which starts the loop, ```repl.Start()```. First, I wanted to create a new lexer each time a line is produced and that would technically be totally fine since the lexer does not need to know any context like previously defined variables. But later, when the parser is added, I need that context, so I wanted to create some workflow that allows it to reuse my only instance of ```lexer.Lexer```.

Since the lexer is working with a rune channel, I needed to create this one first. But the lexer is initialized with this channel and that makes it really hard to reuse the instance. To fix this, I [changed the interface](https://github.com/BenchR267/lbd/commit/0c061cd3d7926b194c34f826b540d862f56fcdd0) of the lexer to take the input channel as an argument of the ```Start``` function instead of ```New```. This makes it possible to create one ```Lexer```, one ```bufio.Scanner``` and create just the channels inside the loop.

```Go
lex := lexer.New()
scanner := bufio.NewScanner(reader)
```

An endless loop in Go is created with the only loop that exists: ```for {}```. Without any condition or iteration this does the same as a ```while``` loop in other languages. Inside the loop the REPL prints its prefix (```lbd $ ```), waits for input and evaluates this.

To read the input from the user I use a ```bufio.Scanner``` instance. It is initialized with a global defined ```io.Reader``` instance which is by default set to ```os.Stdin```. This is a great work around to mock input when unit testing this method. If I would just use ```os.Stdin``` without setting it to this variable, I would hardly be able to write unit tests. The other very important part that needs to be mocked is the ```io.Writer``` where the output is written to. This is by default also set to the obvious one, ```os.Stdout```.

If you are not familiar with those two interfaces in Go: they are the most powerful ones. Go is a truly interface oriented language since interfaces are used for everything to make code more generic usable *(especially due to the lack of [generics itself](https://github.com/golang/proposal/blob/master/design/15292-generics.md))*. Interfaces are especially powerful when they define only one or two methods. ```io.Reader``` for example only defines one method:

```Go
type Reader interface {
	Read(p []byte) (n int, err error)
}
```

Since interface conformance is implicit every type that implements this method is automatically an ```io.Reader``` and can therefor be used as a parameter to every method that accepts an ```io.Reader```. In the test case I am just using ```strings.NewReader()``` to create input for the REPL and ```bytes.Buffer``` to create a buffer for the output.

When the text is scanned from the reader, the REPL sends the given runes to the lexer and waits for its evaluation. The sending of each individual rune is done by the already introduced helper function ```StreamFromString``` from the lexer package.

If you [remember]({{< ref "post/9-Lexer.md" >}}) the [implementation](https://github.com/BenchR267/lbd/blob/c2923d5b3c47f0bfab7a7af25ce5576b71182f99/lexer/lexer.go#L45) of lexers ```Start()``` method, you know that it is creating its own go routine, so the call itself is not blocking.

The lexer is closing its channel after evaluating all runes from the input stream. So to print every evaluated token I am just using a for-range-loop on the channel:

```Go
for t := range lex.NextToken {
	fmt.Fprintln(writer, t)
}
```

Using the REPL looks like this currently:

```Bash
lbd $ a = 5
{{0 0 1} Identifier a}
{{0 2 1} Assign =}
{{0 4 1} Integer 5}

lbd $ b = a == 3
{{0 5 1} Identifier b}
{{0 7 1} Assign =}
{{0 9 1} Identifier a}
{{0 11 2} Equal ==}
{{0 14 1} Integer 3}
```

The session is ended by pressing ```ctrl``` + ```D``` *(^D)* which produces an ```io.EOF``` (end of file) error which then results in a false return value of the ```scanner.Scan()``` call.

To see the whole implementation check out the source file [here](https://github.com/BenchR267/lbd/blob/d5ba30b34ca9b03ad6f6b152cf5fe3cdb2a0e249/repl/repl.go). Thanks for reading this post, the next one is going to be published in the next days since this one is *a bit* delayed.
