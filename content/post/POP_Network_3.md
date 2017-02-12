+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/pop-network-1.jpg"
menu = ""
date = "2017-02-12T18:00:00+02:00"
title = "Part III - Writing a protocol oriented network abstraction in Swift"
tags = ["swift", "pop", "code"]
slug = "pop-network-3"

+++

All Parts: [**I**]({{< ref "post/POP_Network_1.md" >}}) [**II**]({{< ref "post/POP_Network_2.md" >}}) [**III**]({{< ref "post/POP_Network_3.md" >}})

[Last week]({{< ref "post/POP_Network_2.md" >}}) I defined the interface of the **Client** and its **Authenticator**. Together with the definition of the endpoints ([first part]({{< ref "post/POP_Network_2.md" >}})) it is now possible to take a deeper look in the actual network request. But before doing that I need to clarify one important part: the **mapping of the response**. Mapping JSON (or other transport formats) is a different topic, especially in Swift. Just a side note:

<center>{{< tweet 827486597650198529 >}}</center>

I totally aggree [Cédric](https://twitter.com/0xced) on this point even I love overthinking technical tasks like parsing JSON! And since JSON parsing is (unfortunately) a [**really big thing**](https://github.com/search?utf8=✓&q=swift+json) in iOS/Swift, I do not want to create something fancy in this project.

But I need some protocol for **response** types to make it possible to map them from a raw format like **JSON** to a real Swift object. This protocol is as simple as possible:

```swift
public protocol JSONObject {
    init?(json: Any)
}
```

Using `Any` here totally **leverages Swifts strong type system**, but it is necessary because JSON objects can be arrays or dictionaries in Swift. It would be possible to create an **empty protocol** just for forcing the range of types that are usable, but that would not bring any more security to the code and would instead **decrease readability**.

Speaking of Arrays, it would be **great** to have the possibility to write something like:

```swift
extension Array: JSONObject where Element: JSONObject { ... }
```

Unfortunately this is currently not possible but it is great to see that it is [already planned](https://github.com/apple/swift-evolution/blob/master/proposals/0143-conditional-conformances.md). To bypass this I created a wrapper type that can be used instead:

```swift
public struct JSONArray<Element: JSONObject>: JSONObject {
    let array: [Element]
    public init?(json: Any) {
        guard let e = json as? [Any] else {
            return nil
        }
        self.array = e.flatMap(Element.init)
    }
}
```

When defining an **endpoint** that receives an **array** as response, you just specify `JSONArray` with the `Element` type as the `ResponseType`:

```swift
struct MessagesEndpoint: GET {
    typealias ResponseType = JSONArray<Message>
    let path = "messages"
}
```

The specific type needs to implement `init?(json: Any)` on its own, so there is no way aroung casting the `Any` instance to a dictionary and **extracting all the values** for the **needed types** out of it. This is **not that pretty** but as I said in a real project you could use a **third party** library like [ObjectMapper](https://github.com/Hearst-DD/ObjectMapper) for that task.

So, how does the request itself look like? Of course I use [**URLSession**](https://developer.apple.com/reference/foundation/urlsession) for the network request. Every specific method from the `Client` interface forwards its call to a more **generic one**. This is for example the implementation of the get method:

```swift
public func get<E: GET>(_ endpoint: E, completion: @escaping Completion<E.ResponseType>) {
    self.start(endpoint: endpoint, method: .get, completion: completion)
}
```

In `start` the first thing to do is creating the url out of the **saved base url** and the given url parameters. I created two **encoders** for encoding url parameters and post body parameters. They have a **common interface** so I created a protocol for that task:

```swift
public protocol ParameterEncoder {
    associatedtype From
    associatedtype To
    static func encode(from: From) -> To
}
```

The actual implementation of the encoders can be found [here](https://github.com/BenchR267/Resty/blob/master/Sources/ParameterEncoder.swift).

After the url parameters the **headers** are set on the request. This is just a loop over the given parameters and setting them on the request. The **http body** is more interesting:

```swift
// POST BODY
if let post = endpoint as? PostBodyType, 
   let data = PostParameterEncoder.encode(from: post.postBody) {
    request.setValue("\(data.count)", forHTTPHeaderField: "Content-Length")
    request.httpBody = data
}
```

First of all I need to be sure the given endpoint has a **post body**. It is very **convenient** to have a **protocol** for that because now I only need to check whether or not the endpoint **conforms** to the protocol or not. If it does, I try to encode it and set the resulting data on the request.

Now the request itself is **completely configured**. The only thing that is missing is the **authentication data**. As described in the [last post]({{< ref "post/POP_Network_2.md" >}}) this is done by a given `Authenticator`. That authenticator is saved as a property on **initialization time** of the client. The authenticator gets the configured request and **manipulates** it so that it is authenticated with the web service.
```swift
// Authentication
self.authenticator.authenticate(request: &request)
```

Notice the **`&` character** that indicates that we are passing a *'pointer'* in there. *(For clarification: this is no a traditional pointer, it is a so called inout variable in Swift.)* The request is now **ready** to be send to the web service. The next interesting part is in the completion handler of `URLSession`:

```swift
do {
    let json = try JSONSerialization.jsonObject(with: data, options: [])
    let obj = E.ResponseType(json: json)
    completion(Response(res: obj, error: nil))
} catch {
    completion(Response(res: nil, error: error))
}
```

`JSONSerialization` is Apples `Foundation` framework for parsing JSON data. It will **throw an error** if the given data is not correctly formatted json and return an `Any` value if everything worked out. This resulting json value is then passed to the `ResultType` of the `Endpoint` to create a **Swift type** out of it.

**Finally** here is a working example to fetch all your repositories on Github:

```swift
let githubToken = "xxx"

let githubAuthenticator = SimpleAuthenticator {
    $0.setValue("token \(githubToken)", forHTTPHeaderField: "Authorization")
}

struct Repository: JSONObject {
    let desc: String
    let name: String
    init?(json: Any) {
        guard let j = json as? [String: Any] else {
            return nil
        }
        guard
            let d = j["description"] as? String,
            let n = j["full_name"] as? String
        else {
            return nil
        }
        self.desc = d
        self.name = n
    }
}

struct RepositoryEndpoint: GET {
    typealias ResponseType = JSONArray<Repository>
    let path = "/user/repos"
    var urlParameter: [String : String] {
        return ["visibility": "public"]
    }
}


let client = Client(baseUrl: "https://api.github.com", authenticator: githubAuthenticator)

client.get(RepositoryEndpoint()) { response in
    response.res?.array // -> is now an array of all requested repositories
}
```

**Thanks** for reading my first short blog series, it was really **fun** writing it! The whole project can be found on [Github](https://github.com/BenchR267/Resty) and is **free to use and enhance**!
