+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/technical-details.jpg"
menu = ""
date = "2017-04-09T13:00:00+02:00"
title = "Technical details"
tags = ["schwifty"]
slug = "technical-details-gs"

+++

*(The cover is copied from http://coub.com/view/7x8n3. If you do not know what the picture has in common with the topic, check out [last weeks blog post]({{<ref "post/13-Get-Schwifty.md" >}}))*

Last week I introduced ['Get Schwifty']({{<ref "post/13-Get-Schwifty.md" >}}) - an app to write, evaluate and run Swift scripts on an iPhone. Today I am going to describe some technical details of the [project](https://www.github.com/BenchR267/Get-Schwifty).

The project consists of three major parts: user interface, compiler and JavaScript runtime environment. Todays blog post is all about the compiler since it is the core feature of the product.

The compiler itself consists of four components which are necessary to compile the given source code from Swift to JavaScript. The components are lexer, parser, generator and AST. I am going to describe each one in more detail in this post. But before starting it; here is an overview how they are arranged in the workflow between the user interface and the JavaScript runtime:

![](/images/posts/flow.png)

The text input is handled by an UITextView which passes the source code as a raw `String` to the lexer. It is the lexer's task to tokenize the input. A token is an object that contains position-, type- and content information about a part of the source code that belongs together. The lexer also distinguishes between a keyword, an identifier and a literal. If you would like to read more about building a lexer, check out [this blog post]({{<ref "post/9-Lexer.md" >}}) I wrote earlier about my [programming language lbd](https://github.com/BenchR267/lbd). In short, this is how a Token looks like in Get Schwifty:

```Swift
struct Location {
    var row, column, len: Int
}

class Token {
    let loc: Location
    let type: TokenType
    let raw: String
}
```

An example input like `let a = 5` would produce the tokens `[.keyword(let), .identifier(a), .equal, .literal(.integer(5))]` *(pseude code)*. This kind of information is called lexical information, that is where the lexer got it's name from. 

The ouput, so the produced array of tokens, is then passed to the parser which adds semantic information to it. Semantic information describes the relationship between tokens. It is defined by the grammar of the language. Swift has a very complex grammar so I decided to only implement a subset of it to be able to get a first prototype up and running.

A grammar in the context of a compiler means the description of which concatenation of tokens results in which higher level construct. The already used example `let a = 5` is a statement. And statements also have different subcategories, in this case it is a let-declaration-statement. A statement could also be an expression *(which produces an output value)* or a control structure like a loop or an if-statement. A program consists of multiple *(or zero)* concatenated statements. This already is a definition of a subset of the grammar. Normally you would define it in some generic form like [Backus–Naur form](https://en.wikipedia.org/wiki/Backus–Naur_form). 

I needed to act quickly when defining the grammar for Schwifty so I used a format between Backus–Naur form and [Xtext]({{<ref "post/3-Exams.md" >}}) and scribbled it on my whiteboard. I do not want to go too deep into detail here because it is actually pretty straight forward. The current feature set of Get Schwifty is also written down on the README file on [Github](https://github.com/BenchR267/Get-Schwifty).

The parser produces the AST *(abstract syntax tree)*, so lets take a look at this first. As the name already suggests, the AST is a [tree structure](https://en.wikipedia.org/wiki/Tree_(data_structure)), that means it has nodes which have child nodes. One of the nodes has to be the root node, the beginning of the parsing structure. In Get Schwifty the root node is a program. As alread said, a program consists of zero, one or more statements. Since a program is not the only node with this structure I created a wrapping one, a scope.

```Swift
public struct Program {
    public var scope: Scope
}

public struct Scope {
    public var statements = [Statement]()
}

public enum Statement {
    case declaration(Declaration)
    case controlStructure(ControlStructure)
    case expression(Expression)
    case assignment(Assignment)
    case returnE(Expression?)
}
```

You can view the whole AST [here](https://github.com/BenchR267/Get-Schwifty/blob/0.3.2%234/GetSchwifty/Transpiler/AST.swift). To create this tree, the parser just tries all possible solutions until one matches. For a program it just needs to parse the scope and if that is done, the parser checks if there is another element left:

```Swift
func parseProgram() throws -> Program {
    let scope = try self.parseScope(list: Parser.standardNamelist)
    if let additional = self.iteratedElement() {
        throw Error.unexpectedString(expected: "EOF", got: additional.raw)
    }
    return Program(scope: scope)
}
```

A scope repetitive tries to parse a statement and adds that to the list until a `}` occurs which indicates the end of a scope. A declaration checks for the current token and decides based on this what it is trying to parse:

```Swift
private func parseDeclaration(namelist: inout [String: IdentifierInformation]) throws -> Declaration {
    let keyword = try self.parseKeyword()
    switch keyword {
    case "var":
        return .variable(try self.parseVariableDecl(namelist: &namelist))
    case "let":
        return .constant(try self.parseLetDecl(namelist: &namelist))
    case "func":
        return .function(try self.parseFunctionDecl(namelist: &namelist))
    default:
        throw Error.unimplemented
    }
}
```

In most common compilers code like this is generated. I also introduced a technique to generate parser code [before]({{<ref "post/3-Exams.md" >}}). But in my opinion it is necessary to understand how a parser works and how one could implement it before generating this important part.

The output of the parser is a completely parsed AST if there were no errors during compile time. The next part in the workflow is the generator which takes the AST as an input and generates JavaScript code out of it. JavaScript does not know the concept of types so they are completely erased during generation time. It is very important that the type checks are done during parsing as well!

The generator generates code for each statement of the program separately which is necessary to allow functions like `sleep`. It takes the part of the AST and generates JavaScript code that describes the intention behind the statement/expression/… The `let a = 5` statement would produce the following JavaScript code: `const a = 5;`. `const` means in JavaScript that the value of this variable does not change. I could also use `var` here since the mutation check is done durig compile time in the parser as well. The generation itself is straight forward and can be checked out [here](https://github.com/BenchR267/Get-Schwifty/blob/0.3.2%234/GetSchwifty/Transpiler/Generator.swift).

That is basically it. The tokens output from the lexer is also used for syntax highlighting, the JavaScript output from the generator is used as the input for the JSContext wrapper which executes the code. Thanks for reading this weeks blog post. If you would like to test Get Schwifty, I have some Testflight places left; just write me on [Twitter](https://twitter.com/benchr).
