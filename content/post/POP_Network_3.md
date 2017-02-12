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

In the [last part]({{< ref "post/POP_Network_2.md" >}}) I defined the interface of the **Client** and it is **Authenticator**. Together with the definition of the endpoints ([first part]({{< ref "post/POP_Network_2.md" >}})) it is now possible to take a deeper look in the actual network request. But before doing that I need to clarify one important part: the **mapping of the response**. Mapping JSON (or other transport formats) is a different topic, especially in Swift. Just a side note:

<center>{{< tweet 827486597650198529 >}}</center>

I totally aggree [Cédric](https://twitter.com/0xced) on this point even I love overthinking technical tasks like parsing JSON! And since JSON parsing is (unfortunately) a [**really big thing**](https://github.com/search?utf8=✓&q=swift+json) in iOS/Swift, I do not want to create something fancy in this project.

But I need some protocol for **response** types to make it possible to map them from a raw format like **JSON** to a real Swift object. This protocol is as simple as possible:

```swift
public protocol JSONObject {
    init?(json: Any)
}
```


