+++
comments = true
date = "2017-01-15T10:00:00+02:00"
title = "Some Thoughts"
draft = false
share = true
tags = ["blog", "decisions", "compiler"]
slug = "thoughts"
author = "Benjamin Herzog"
image = "images/Some_Thoughts.jpg"

+++

This week I want to share some of my thoughts about my [**new programming language**]({{< ref "post/A_New_Beginning.md" >}}). Or to be a bit more specific: about some technical details for building the toolchain.
There are a lot of things that need to be decided which are also very important for the **planning process**. Here is an overview over some of them:

- Will the language be **compiled** or **interpreted**?
- What exact **features** will the language support?
- In which main **language** will the tools be written?
- What is the **name** of the language?
- Will it be **open source**?

Beforehand: yes, it will of course be **open source**! I will not only host the code on [Github](https://www.github.com), but also develop the whole project there. I will create a repository for it as soon as I come up with a name. 

That takes me to the next question: what will be the **name of the language**? I think this is a really hard one and I honestly don't want to decide this yet. If you have any idea, please [let me know](https://twitter.com/benchr)! I'm a bit uncreative about those kind of things… Looking at other languages it could be just an uppercased character ([C](http://groups.engin.umd.umich.edu/CIS/course.des/cis400/c/c.html), [F](http://fsharp.org), [R](https://www.r-project.org)), some shiny, fast words ([Swift](https://swift.org), [Go](https://golang.org), [Ruby](https://www.ruby-lang.org/en/)), a shortcut for some technical description ([PHP](http://php.net), [SQL](https://en.wikipedia.org/wiki/SQL)) or just a random name ([Java](https://www.java.com/en/), [Python](https://www.python.org)).

Will the language be **compiled** or **interpreted**? I'm glad I don't have to decide this by now. There are a lot of other things that need to get worked out. But in the end in my point of view there are **two possible answers** for this: either the compiler will generate code that can be run in [llvm](http://llvm.org) or I will write an interpreter for running the code directly. I will decide this when the lexer and parser are both ready.

The **features** of the language will also be decided later. This is actually something I will decide depending on the progress I'm making over time. I'd like to get started with a [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) which supports only the necessary parts of a language (variables, literals, control structures, functions) and add the rest later.

Which **language** will I use for the toolchain? I also have two answers for that and just need to make a decision. It would be very obvious to use **Swift** since I'm very familiar with the language and know how to structure things for working properly. So this is the first option I'm considering. The second one is another language I'm very interested in which brings also some advantages for a toolchain with it: **Go**. Go is very fast, can create binaries for different architectures very easily and has a great concurrency model. 

I definitely have a lot more **experience** in Swift than in Go, so using Go would force me to **learn** more about the usage of the language. I'm not sure if this is **good** or if it costs more time and risked the chance of achieving my goal in general. Apart from that learning something new is exactly what I want to achieve. I will decide that later as well.

Thanks for reading this week's blog post! Since the **exams** are getting closer every week I'll use next week's post to write about something I learned in university which could be a very interesting addition for the parser. So far, write you in a week!
