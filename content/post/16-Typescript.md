+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/typescript.jpg"
menu = ""
date = "2017-05-03T11:00:00+01:00"
title = "Typescript"
tags = ["typescript"]
slug = "typescript"

+++

In this blog post I am going to write about a programming language which I have not checked out before. Typescript is a super set of Javascript and is developed by Microsoft. The syntax is exactly the same as Javascript with one very important difference: in comparison to Javascript Typescript knows the concept of types and checks them with a built in type system.

To make type failure messages more useful Typescript is also a compiled language while Javascript is an interpreted language. That means that Typescript files are checked by a program *(the compiler)* which checks for general syntax problems or undefined property access. The biggest advantage of Javascript is at the same time also the biggest problem: its dynamic behaviour. You can create cery rich and cool prototypes in no time but it is hard scalable.

Creating interfaces for modules or functions depends often on rich documentation. Documentation is great and important but it is in this case nothing else than a type system written down in plain english instead of something the computer could understand to help you write your code the right way. Typescript adds this with its type system. Look at this method declaration and usage in Javascript:

```Javascript
function getName(obj) {
	return obj.name
}

let person = {
	name: "Rainer Zufall"
}

let name1 = getName(person)

let number = 5
let name2 = getName(number)
```

This is totally valid Javascript but will set `name2` to `undefined` since the `number` does not have a name property. A compiler with a type system like Typescript checks for this violation and does not compile the code without an error. The same code in Typescript looks like this:

```Typescript
function getName(obj: { name: string }): string {
  return obj.name
}
```

As you see, the compiler shows an error if you try to use the function with a wrong parameter:

![](/images/posts/typescript1.jpeg)

By the way: I use Visual Studio Code for programming, it has very great Typescript-functionality built it and works really great. Since Typescript is a super set of Javascript you can also just write Javascript in your .ts-files and spare the convenience of type information.

Before I continue with the most important features of the type system, here is a short introduction how to install the compiler and use it with other editors:

```Bash
$ # Install typescript via npm/yarn
$ yarn global add typescript
$ # Use the Typescript compiler (tsc) to compile a given .ts file
$ tsc main.ts
```

When compiling a file, the compiler creates the generated Javascript file. Pay attention: the file is also created if the compiler shows an error! You can compile multiple files at once and even specify an output directory for the generated code. But if your project gets bigger than a file I would still recommend to use Visual Studio Code instead.

#### The Type System

To declare a variable with a given type, you use the `let variable: string` syntax. If you than try to assign a value of an expression that does not match the given type, you will get a useful error. I already showed that it is also possible to declare the type by giving an object like syntax with types to enforce special properties: `let human: {name: string, gender: Gender}`. To make this more scalable, it is also possible to define interfaces:

```Typescript
enum Gender {
  male, female
}

interface Person {
  name: string
  gender: Gender
}

let p: Person = {
  name: "Rainer Zufall",
  gender: Gender.male
}
```

A very helpful feature is the autocompletion of Visual Studio Code while defining the actual object. This is something that is not possible in Javascript since objects are so dynamic that they could contain literally anything. In Typescript you can also create classes which implements defined interfaces:

```Typescript
class Student implements Person {
  name: string
  gender: Gender
  discipline: string

  constructor(name: string, gender: Gender, discipline: string) {
    this.name = name
    this.gender = gender
    this.discipline = discipline
  }

  greet() {
    console.log("Hello " + name + "!")
  }
}

let p = new Student("Benjamin Herzog", Gender.male, "Computer science")
p.greet()
```

Another very important feature is the presence of optional parameters. Sometimes when defining an interface you would like to give the implementing types some freedom. Some properties are not necessary but if they are present, they should be handled. That can be done with optional properties:

```Typescript
interface Person {
  name: string
  phoneNumber?: string
}

// valid, because phoneNumber is optional
let p1: Person = {
  name: "Rainer Zufall"
}

let p2: Person = {
  name: "Clair Grube",
  phoneNumber: "+491626194826"
}
```

##### Generics

A strong type system should of course also provide the possibility to use generics. Generics gives the programmer the possibility to write functions that work dynamic with different types without the need of writing multiple functions. The function also does not need to know which specific type it operates it, often only a given property or method is needed. This can be achieved with interfaces. Here is a simple example:

```Typescript
interface Namable {
  name: string
}

function getName<T extends Namable>(obj: T): string {
  return obj.name
}

getName({name: "Ben"})          // valid
getName({age: 25})              // invalid
getName({name: "Ben", age: 25}) // valid
```

Another example shows the strong type system in the dynamic world of Javascript:

```Typescript
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

const obj = {
  name: "Ben",
  age: 25
}

getProperty(obj, "name")	// valid
getProperty(obj, "phone")	// invalid
getProperty(obj, "age")		// valid
```

Since objects in Javascript are like dictionaries/maps in other languages they are very dynamic and their properties can even be accessed through subscripts. In the example above the type system checks that the string *(!!)* that is given to the method as the second parameter is a valid property name of the first parameter. For me as a Swift developer this is totally crazy and really cool!

Now some other advanced type constructs that are not possible in Swift:

Merged types:
```Typescript
const p1 = {
  name: "Ben"
}

const p2 = {
  age: 25
}

function mergedPerson<T, U>(obj1: T, obj2: U): T & U {
  let r = <T & U>{}
  for (let p in obj1) {
    (<any>r)[p] = obj1[p]
  }
  for (let p in obj2) {
    (<any>r)[p] = obj2[p]
  }
  return r
}

let p3 = mergedPerson(p1, p2)
p3.name // "Ben"
p3.age  // 25
```

Union types: (Could in Swift be expressed with enums)
```Typescript
function getSringOrNumber(parameter: number): string | number {
  if (parameter == 42) {
    return "Answer of life"
  }
  return 42
}
```

More string checking:
```Typescript
type HTTPMethod = 'GET' | 'POST' | 'PUT' | 'DELETE'

function request(method: HTTPMethod) {
  // do something
}

request('GET')		// valid
request('UPDATE')	// invalid
```

There are a lot more features, but those were the most important ones. I think Typescript is a really rich language and has a lot of potential, I think the guys at Microsoft are doing a great job! If you would like to know more about the language, check out the [official website](https://www.typescriptlang.org/index.html).

Thanks for reading this blog post! I know it came a bit late and I struggle a bit with the weekly blog posts. I just do not want to write a blog post just for the purpose of writing. The main focus is on learning new things. Writing a blog post about Typescript forces me to get to know more about the topic, which is really great. It is just hard to read and try that much stuff for cool topics beside my studies, jobs and other projects. I have another update regarding by programming language lbd which I did not touch for a while. I will write a post about it tomorrow. See you there!
