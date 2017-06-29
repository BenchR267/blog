+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/autoclosure.jpg"
menu = ""
date = "2017-06-27T19:00:00+01:00"
title = "Replacing @autoclosure in Swift"
tags = ["swift"]
slug = "autoclosure"

+++

A short preamble before the actual topic: yes, I let the blog slide. I am pretty sure that not that many people are reading my posts and those few are not mad reading a bit less but I have to admit that I failed the challenge I started beginning this year. It is not about that it is hard writing blog posts every week (what it is), it is more about writing posts weekly just to have them written and published. I would love to write more posts on the blog but I would like to write them when I have a great topic I would like to write about. So in the future there are no regulary posts on this blog but hopefully some better ones. Now, let's start with this posts topic.

Before I start with this: I really love Swift! It is the best language in my eyes that is currently available. But I also hate a lot of stuff about the language design that I would like to help getting it fixed.

One of those things is the way `@autoclosure` is implemented. But let me first explain what it is and why it is needed. Imagine you have a function that takes a boolean value and a string. If the boolean is false it prints the string to the console, otherwise it does nothing. This function actually exists and is called assert, it also stops the execution of the program.

```Swift
func assert(_ condition: Bool, _ message: String) {…}
```

The problem with this definition is, that the message might be heavily computed because we need to have a lot of information if the condition fails. Since Swift is strictly evaluated *(every computation is done when the execution pointer reaches the line)* this would mean the heavy computation is also done when the condition is true, so it would be useless since the string is not needed.

A way to fix this is to use a closure that produces the value instead of the value itself:

```Swift
func assert(_ condition: Bool, _ message: () -> String) {
    guard !condition else { return }
    print(message())
}
```

This would only produce the value when it is needed. Since message is now a function instead of a String, we need now to call the function to get its value.

On the call side this changes a lot:

```Swift
assert(something > 0, { return "something is \(something) 😱" })
```

This is not great and that is where `@autoclosure` comes into play. It wraps the given value automatically in a closure so that you do not call the function differently even if it is now using lazy evaluation. *(lazy evaluation is the name for evaluating an expression only when and if you need the value)*

I worked a bit with Scala recently and saw that they have a similar functionality in the language but I like the implementation much better. Here is the same function from above in Scala:

```Scala
def assert(c: Boolean, msg: => String)
```

A normal closure declaration in Scala is pretty similar:

```Scala
def function(f: String => String)
```

So in the end they only omit the parameters in the declaration. Parameters are also not possible for @autoclosure because the value needs to be produced without any if it is not written in a closure.

I really like this syntax and would love to see it in Swift as well! What do you think about it? The proposed solution would be to replace @autoclosure with this:

```Swift
func assert(_ c: Bool, _ message: -> String)
```
