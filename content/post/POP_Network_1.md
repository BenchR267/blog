+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/Exams.jpg"
menu = ""
date = "2017-02-03T15:00:00+02:00"
title = "Part I - Writing a protocol oriented network abstraction"
tags = ["swift", "pop", "code"]
slug = "pop-network-1"

+++

Todays topic is very **technical**. So if you are not interested in programming (or especially in Swift programming), this post might not be for you.

This is the first of multiple (propably 3) blog posts in the future. Before starting with my plan and the implementation details I would like to describe how that topic came up to me.

In summer 2014, right after the announcement of Swift, I created a [YouTube Channel](https://www.youtube.com/swiftde). It was a huge success here in Germany and a lot of viewers started watching the videos. Later, I produced a [video course](https://www.udemy.com/ios-8-und-swift-der-einstieg-in-grosartige-app-entwicklung/?couponCode=BLOG_1) about Swift. It was a huge success as well. Then, a year ago, I created a [Slack-Team](http://slack.swiftde.net) for all the ambitious, motivated novice Swift engineers. It was a great success! There are over 630 registered people and they aren't beginners anymore. We came up with a lot of great ideas. One of those were a challenge like competition in which every participant builds the same app with reduced functionality concentrated on code quality and architecture. The outcome was presented in a [live video stream](https://www.youtube.com/watch?v=EXxYGszFOSY) on YouTube and the [code](https://github.com/swiftde/swift-client-challenge/tree/master/01%20Github-Client) was uploaded to Github.

One of the main parts about this challenge in my eyes was the general over engineering which is just not possible in daily business. It was a manageable feature set: Build a Github-Client which is able to search for Users, show their repositories and open the README-file of the selected one. There was one rule: don't use any third party dependency. This was a downer in my opinion but it also forced me and the others to rethink very basic stuff. Since my implementation got very complex, I wasn't able to describe it completely in the live stream. But the topic is so interesting that I thought I could write about it. So here it is: writing a protocol oriented network abstraction in Swift!

The code I'm showing here is not complete, but will be made available on Github when the last post of this series got online. The implementation of the challenge solution was made up of three parts: the network abstraction, the mapping logic and the UI wrapper for UITableView. I rewrote the network abstraction to be as protocol oriented as it makes sense. Protocol oriented programming feels very naturally to Swift, since the standard library is built that way. It's also not a complex paradigm and quite easy to understand. The aim is to make parts of the code as reusable as possible (if it makes sense to reuse this code). Since Swift classes can only inherit from one superclass it also brings a lot of freedom and new patterns to use protocols since all Swift types can 'inherit' from multiple protocols. And protocols can bring their one implementation via extensions which is extremly powerful!

Don't worry: this post isn't just the introduction, we will start with the code now and the next part will be online in the next days as well.

The framework should be used for one [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) service, so the interface should be a RestClient. The client itself is just a plain class, because adding a protocol here wouldn't make any sense, the user should use this class. Using a struct or enum would also make no sense since the client will be used from the whole app and should only be referenced instead of copied.

```Swift
public class Client { 
	public var baseUrl: URL
	init(baseUrl: String) {
		self.baseUrl = URL(string: baseUrl)!
	}
}
```

A client is initialized with a base url which is also changeable for later requests. The force unwrapping in the initializer is definitely a code smell but urls for webservices often don't change and are hard coded, so a crash is actually intended. In production environment we might have a better describing error message.

Each call to the webservice is defined as an endpoint. An endpoint defines the path to the method that's invoked and provides all the necessary informations. Aim is that the user of that endpoint can't configure anything wrong. The requests are good old HTTP 1.1 requests so the most important possible configurations are: http method, url parameters, post parameters, header values. But not every http method accepts post parameters, so that should be enforced be Swifts type system. And this is possible by using protocols:

```Swift
public protocol URLParameterType {
    var urlParameter: [String: String] { get }
}
public enum PostBody {
    case None, String(String), Data(Data), JSON(Any), URL([String: String])
}
public protocol PostBodyType {
    var postBody: PostBody { get }
}
public protocol Endpoint: URLParameterType {
    associatedtype ResponseType: JSONObject
    var path: String { get }
    var header: [String: String] { get }
}
public protocol GET: Endpoint {}
public protocol POST: Endpoint, PostBodyType {}
public protocol PUT: Endpoint, PostBodyType {}
public protocol DELETE: Endpoint, PostBodyType {}
```

I will only implement the http methods get, post, put, delete for now but you get the idea. Something that can have any url parameters is described by the protocol URLParameterType. Something that can has a post body is defined by the PostBodyType protocol. Endpoint is the general protocol for an endpoint which should not be used directly. GET, POST, PUT and DELETE are specific protocols that define which parameters they accept. With that implementation it's easy for the client to enforce http methods for defined endpoints. 

At the end I would like to test the whole implementation with the [Github-API](https://developer.github.com/v3), so here is a sample implementation of the endpoint which describes the get requests for fetching the informations about all repositories of the logged in user.

```Swift
struct Repositories: GET {
    typealias ResponseType = JSONArray<Repository>
    let path = "user/repos"
    let urlParameter = ["visibility": "public"]
}
```

Ignore the ResponseType of endpoint for now, this is just necessary to get strongly typed responses from the client after a successful request.

In the next blog post I will build on top of those declarations the client and response structures. Thanks for reading so far, see you in a few days!
