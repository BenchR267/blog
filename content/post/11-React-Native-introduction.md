+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/react-native.jpg"
menu = ""
date = "2017-03-21T11:00:00+02:00"
title = "Introduction to React Native"
tags = ["react"]
slug = "rn-introduction"

+++

In this blog post I would like to talk about [**React Native**](https://facebook.github.io/react-native/). Before starting: this evening I am going to talk about this topic at the [Cococaheads Dresden](http://meetu.ps/36GS6z) meetup. If you are more the visual type check out the video I will post here when it is finished processing.

> React Native - Learn one, write anywhere: Build mobile apps with React.

This is the official description of the framework and you will get what it means during this post. I would like to cover some questions to get everything important covered.

## What is it?

React Native is Reactjs for native apps. Reactjs is a framework for writing high performant web frontend applications. It has a really clean UI architecture built on top of the components concept. A component in this context is a view with a defined interface. Those components display a given state and depending on changes in the state the UI is changing as well, so the UI is completely state driven.

React Native now lets you use this web framework to write native apps in Javascript. I am not talking about a rendered web view but real native apps. That gives the developer the possiblity to write fast apps with a shared codebase for a hand full of operating systems.

## What does it do?

First of all: it gives you a very fast iteration cycle during the development. Since the code is compiled down to a bundle and then evaluated at runtime, you literally save your changes and see the UI changing immediately. If you are used to recompile a big iOS project written in Objective-C/Swift only to see a color or number change, you know what a big advantage this is.

The next thing is the already mentioned shared code base. Do not get me wrong on this: you can not reuse the whole project (at least if it is already a bit complex). But you can reuse your models, business- and network logic between your mobile apps, your website, your desktop apps and even your backend!

## Why should I use it?

> Any application that *can* be written in JavaScript, *will* eventually be written in JavaScript. 
- [Atwood's Law](https://blog.codinghorror.com/the-principle-of-least-power/)

React Native is available for [iOS](https://github.com/facebook/react-native), [Android](https://github.com/facebook/react-native), [Web](https://github.com/facebook/react), [macOS](https://github.com/ptmt/react-native-macos), [Ubuntu](https://github.com/CanonicalLtd/react-native/blob/ubuntu/README-ubuntu.md) and even for [Windows](https://github.com/Microsoft/react-native-windows)! Since it is an abstraction layer over all the mentioned platforms you do not need to struggle with platform specific APIs. But if your app should do more than the standard stuff (display a list and some detail informations) you might need build a native bridge for the needed platform.

## How to set it up?

The fastest way to get started is using the [Expo](https://expo.io)-App on your phone and the [Sketch-Expo-Service](https://sketch.expo.io) on your computer. After scanning the QR code with your phone you can edit the javascript file on your computer and without changing the app changes on your phone.
