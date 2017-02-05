+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/pop-network-1.jpg"
menu = ""
date = "2017-02-05T08:00:00+02:00"
title = "Part II - Writing a protocol oriented network abstraction in Swift"
tags = ["swift", "pop", "code"]
slug = "pop-network-2"

+++

In the [**first part**]({{< ref "post/POP_Network_1.md" >}}) I built the **foundation** for a protocol oriented network abstraction written in Swift. The main focus for the component lies on **safety**, **code quality** and a very **clean** [**API**](https://en.wikipedia.org/wiki/Application_programming_interface).

Safety is mostly about the **rules** you define how the component can be used. In the best scenario the user **can't even compile** the program if the component is wrong configured. Of course, this is the **perfect case** which is just not reachable. But by defining different **protocols** for different http methods, it is possible to distinguish between them on the level of Swift's **type system** instead of comparing Strings.

Code quality is a very generic term and I bet that everyone will think different about this. In my eyes code quality is defined by **readability** and the structure of the code. A **rule of thumb** is that code is written in 10% of the productive time but it is read in **90%** of it. That means you are fighting yourself when writing '*spaghetti code*'. Related to the rest component building code quality means to structure the code that well a user could **fix a bug** by himself.

API design is a **subset** of code quality since it defines how the interface of the component is built. Even while there should be documentation it should not be necessary to read it to be able to use the code. It is also bound to **safety** in this case because the API should be strongly typed though **generics** since the concrete types are not even defined in this module.

So, how does the API of the client look like?

```Swift
public class Client {
    public enum Errors : Error {
        case invalidUrl(String)
        case invalidResponse(Data?, Error?)
        case requestFailed(Int, Error?)
    }

    public init(baseUrl: String, authenticator: Authenticator, timeout: TimeInterval = default)

    public typealias Completion<R : JSONObject> = (Response<R>) -> Void
    public func get<E : GET>(_ endpoint: E, completion: @escaping Completion<E.ResponseType>)

    public func post<E : POST>(_ endpoint: E, completion: @escaping Completion<E.ResponseType>)

    public func put<E : PUT>(_ endpoint: E, completion: @escaping Completion<E.ResponseType>)

    public func delete<E : DELETE>(_ endpoint: E, completion: @escaping Completion<E.ResponseType>)
}
```

First of all, there are some Errors defined via a **nested enum**. Thats very convenient since they are bound to the namespace `Client`. There is one initializer which takes a baseUrl, an **authenticator** which I will describe in a moment and a timeout in seconds. Those three values are also stored in properties which might also be part of the interface but I removed them from this view for **clarity**.

The rest of the API are methods for each possible **http method**. Since every method that starts a request takes a **generic endpoint** it forces via the **type system** that only endpoints can be used that support the given http method.

To make this more clear, think about this arbitrary endpoint:

```Swift
struct MessageEndpoint: POST, DELETE {
    typealias ResponseType = Message
    let path = "message"
}
```

This endpoint describes the message service of our random backend. It is possible to **POST** (add) a new message or to **DELETE** an existing one. This is realized with the **multiple inheritance** via protocols. And since `MessageEndpoint` does not conform to `GET`, it's not possible to use it with the `get` method of `Client`. That's **safety enforced by the compiler**.

In the next blog post I will tell more about the **associated type** in the `Endpoint` definition. But before that I would like to quickly describe the very basic authentication implementation:

```Swift
public protocol Authenticator {
    func authenticate(request: inout URLRequest)
}
```

An `Authenticator` is a type that is able to **authenticate** a given request. It **doesn't matter** for the client how this is done, it's just important that the correct **header** values are set. So before sending the actual request, the client gives it's authenticator the chance to authenticate the request. Here is a **very basic implementation** of an authenticator:

```Swift
public struct SimpleAuthenticator: Authenticator {
    let authenticationBlock: (inout URLRequest) -> Void
    
    public func authenticate(request: inout URLRequest) {
        self.authenticationBlock(&request)
    }
}
```

This simple struct wraps a **closure** which is called to authenticate a given request. For the Github API this is enough since we are using **app tokens** to authenticate us with the server. So when using the `SimpleAuthenticator` the code would look like this:

```Swift
let authenticator = SimpleAuthenticator { req in
	req.setValue("token \(githubToken)", forHTTPHeaderField: "Authorization")
}
```

But since the `Authenticator` is declared as a **protocol** it's also possible to implement very **complex authentication algorithms**. The authenticator gets the complete request with all information which he could use to calculate and manipulate.

Thanks for reading and see you next week!
