+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/get_schwifty.JPG"
menu = ""
date = "2017-04-04T20:00:00+02:00"
title = "Introducing Get Schwifty!"
tags = ["schwifty"]
slug = "get-schwifty"

+++

Each year **Apple** organizes a conference for their **third party** platform developers. The conference is called [**WWDC**](https://developer.apple.com/wwdc/) *(world wide developers conference)* and takes place in **California, USA**. Although the tickets are **extremly expensive** *(1,599$ each)* it is almost **impossible** to get the chance to buy one due to the mass of people who try their luck.

The conference is all about the **software**; Apple uses the opening keynote to introduce the new **major versions** of their platform operating systems, **iOS** and **macOS**. The rest of the week there are a ton of **talks** given by Apple employees plus **labs** where you can talk one to one to the developers who build the **SDKs** and **tools** you use every day.

Attending **WWDC** is propably every iOS/macOS developer's **dream** and so it is mine. Fortunately, Apple has a [special treatment](https://developer.apple.com/wwdc/scholarships/) for pupils, **students** and members of STEM organizations. Last year I send an application for a **scholarship** which would have allowed me to visit the conference for **free**. Unfortunately, I did **not** win the scholarship but my friend [Kilian](https://twitter.com/kiliankoe) did which made me **happy** as well.

![](/images/posts/schwifty.jpg)

Instead of visiting WWDC I had the **great opportunity** to attend [**AltConf**](http://altconf.com) which was taking place in **San Francisco** during the time of WWDC last year. I am incredible **happy** about the chance I got. Being in San Francisco during a time where **thousands** of people around you who **love** the same things as you do is **awesome**. I collected a ton of **invaluable experiences** and met a lot of very interesting people. Some of them I am very proud to call my **friends**.

Although AltConf was really interesting and definitely **worth** the visit it was kind of **sad** to see all the people with badges go into the toward building each morning.

Later in October last year I started my master **studies** which means I am still a **student** and able to apply for a **scholarship** again. Having my experiences from last year in mind it was clear that I needed to try my **luck**. The only problems I had were the **financial load** of buying a plane ticket to the most **expensive** place in the world and staying there for the week. This is still a problem but it is one which I try to figure out if I get the **chance** to.

And that brings me to the original **intend** of this blog post: I am **extremely proud** to present you the project I built as an application for this year's WWDC scholarship. The task was to build some **visually interactive** scene using **Swift Playgrounds**, a part of Xcode and seperate app on iPad. There are literally **infinite** possibilities which makes it really **hard** to get the right idea. When Swift was firstly [announced](https://www.youtube.com/watch?v=MO7Ta0DvEWA) **Chris Lattner** *(the inventor of Swift)* built a SpriteKit scene in Playgrounds. That was very **impressive** and so my first thought was to build a graphical scene as well. But it turns out that graphical three dimensional scenes are not my biggest **strength**.

If you started reading this blog from the [beginning]({{< ref "post/1-A_New_Beginning.md" >}}) you may know that I like to overthink **complex problems** and **learn** new things. You may also have read that I wanted to build my own **compiler** for a new programming language, [**lbd**]({{< ref "post/9-Lexer.md" >}}). Having this in mind I came to the conclusion that the Playground itself is the best **interactive scene** I know. So I started implementing my **own**.

Due to **security** reasons it is not possible to write, compile and run binaries inside iOS apps. But it is possible to evaluate **Javascript** using a framework called JavaScriptCore! That is it!

![](/images/posts/schwifty.gif)

I built a **lexer**, **parser**, **abstract syntax tree** and a Javascript **generator** plus some user interface and a small wrapper around JavaScriptCore. The result is pretty **impressive** *(especially for 2-3 days of work)* and I am very **proud** about it. Today I would like to **open source** the repository to be open for contributions and the possibility for others to learn.

I do not think that this project will stay as a weekend hackathon project but will **grow** in the future. The potential is literally **endless** because it is not possible for me to implement all features that a team of **professionals** at **Apple** creates. And that it is totally okay! The app is not meant to **replace** something; it is just an **addition** to try out the language on the go.

If you would like to **pre-alpha test** the app send me a direct message on [**Twitter**](https://twitter.com/benchr). Feel also free to check out the repository on [**Github**](https://github.com/BenchR267/Get-Schwifty)! Thanks for reading this blog post, I am really **thrilled** about the future! Wish me **luck** for the scholarship lottery, announcement is on **April 21**.

PS: The name is inspired by this great episode of Rick and Morty:

{{< youtube I1188GO4p1E >}}
