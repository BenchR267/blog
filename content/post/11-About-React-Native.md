+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/react-native.jpg"
menu = ""
date = "2017-03-21T11:00:00+02:00"
title = "About React Native"
tags = ["react"]
slug = "about-rn"

+++

In this blog post I would like to talk about [React Native](https://facebook.github.io/react-native/). Before starting: this evening I am going to talk about this topic at the [Cococaheads Dresden](http://meetu.ps/36GS6z) meetup. If you are more the visual type and you are able to understand German check out the video of the talk [here](https://www.youtube.com/watch?v=L4SEep6Immk).

> React Native - Learn one, write anywhere: Build mobile apps with React.

This is the official description of the framework and you will get what it means during this post. I would like to cover some questions to get everything important covered.

## What is it?

React Native is Reactjs for native apps. Reactjs is a framework for writing high performant web frontend applications. It has a really clean UI architecture built on top of the components concept. A component in this context is a view with a defined interface. Those components display a given state and depending on changes in the state the UI is changing as well, so the UI is completely state driven.

React Native now lets you use this web framework to write native apps in Javascript. I am not talking about a rendered web view but real native apps. That gives the developer the possiblity to write fast apps with a shared codebase for a hand full of operating systems.

## What does it do?

First of all: it gives you a very fast iteration cycle during the development. Since the code is compiled down to a bundle and then evaluated at runtime, you literally save your changes and see the UI changing immediately. If you are used to recompile a big iOS project written in Objective-C/Swift only to see a color or number change, you know what a big advantage this is.

The next thing is the already mentioned shared code base. Do not get me wrong on this: you can not reuse the whole project *(at least if it is already a bit complex)*. But you can reuse your models, business- and network logic between your mobile apps, your website, your desktop apps and even your backend!

## Why should I use it?

> Any application that *can* be written in JavaScript, *will* eventually be written in JavaScript. 
- [Atwood's Law](https://blog.codinghorror.com/the-principle-of-least-power/)

React Native is available for [iOS](https://github.com/facebook/react-native), [Android](https://github.com/facebook/react-native), [Web](https://github.com/facebook/react), [macOS](https://github.com/ptmt/react-native-macos), [Ubuntu](https://github.com/CanonicalLtd/react-native/blob/ubuntu/README-ubuntu.md) and even for [Windows](https://github.com/Microsoft/react-native-windows)! Since it is an abstraction layer over all the mentioned platforms you do not need to struggle with platform specific APIs. But if your app should do more than the standard stuff *( like displaying a list and some detail informations)* you might need build a native bridge for the needed platform.

## How to set it up?

The fastest way to get started is using the [Expo](https://expo.io)-App on your phone and the [Sketch-Expo-Service](https://sketch.expo.io) on your computer. After scanning the QR code with your phone you can edit the Javascript file on your computer and without the need to save the file any changes in the code results in changes on your phone.

While this is great for fast prototyping or mob programming I would recommend to set up a project on your computer. You have two options to choose from. Either you continue to use your phone as executing device or you boot up a simulator (or emulator). For both you first need to initialize a project with the [create-react-native-app](http://facebook.github.io/react-native/blog/2017/03/13/introducing-create-react-native-app.html). I recommend using yarn as a package manager, but npm is also fine. After that initialize the project with an awesome name, change into the created directory and start the server:

```
$ yarn add global create-react-native-app
$ create-react-native-app AwesomeProject
$ cd AwesomeProject
$ yarn [start|ios|android]
```

`npm start` starts the dev server, `npm ios` and `npm android` start a simulator or emulator instance of the given operating system and a development server in the background. But I do not want to dive deeper into an example project in this blog post; I would rather like to answer the last three questions:

## What technologies are being used?

React Native is build in Typescript which is transpiled down to Javascript. That brings the big advantage of defined typed interfaces so you do not need to struggle with untyped Javascript interfaces *(which is especially hard when coming from a static typed language like Swift)*. When programming your apps you can use any language that transpiles down to Javascript. Recommended is using ES6 through Babel or Typescript.

The development server that serves your bundled app code is powered by Nodejs. Another tool that is running with your server is watchmen which is responsible for watching changes on your files to rebuild the bundle when needed. The app is also able diff changes in the bundle to reload your app without invalidating the state. That is extremely powerful if you are working with data that is loaded from the internet.

As a layout engine React (Native) uses Stylesheets powered by [flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) which is very powerful and can be compared to `UIStackView`. If you would like to integrate React Native in your existing project it is recommended to use Cocoapods to integrate the React framework *(and its subspecs)*. The Pod is part of the node module dependency and I am going to write another blog post about this topic but there is also a great tutorial [here](http://facebook.github.io/react-native/docs/integration-with-existing-apps.html).

## What are the major advantages / drawbacks when using React Native?

advantages|drawbacks
|---|---|
|development on speed|big dependency (> 550 node modules)|
|faster editor|no 'IDE'|
|lightning fast apps|for complex stuff native bridging needed|
|Javascript|Swift > Javascript|
|flexbox / css||
|components||
|state driven design||

I think all points are self-explaining. There is another drawback thats mentioned in some [blog posts](http://react-etc.net/entry/your-license-to-use-react-js-can-be-revoked-if-you-compete-with-facebook) about the license of using React. This is a really important topic and [Ash Furrow](https://ashfurrow.com) started a really interesting discussion on [Github](https://github.com/facebook/react/issues/7293) about this. The original problem is that Facebook could theoretically reject the license in using React (Native) if you compete with their product. [Here is a great FAQ](https://code.facebook.com/pages/850928938376556) Facebook created for this topic. tl;dr: this is not a problem you should ever face.

## What means 'state driven design'?

In this blog I did not wrote any actual code yet, so it might be a bit hard to follow. Imagine you have an app that fetches some user information and displays the users name in a label. This is what an app doing that might look like:


```
class User extends Component {
	render() {
		return (<Text>{this.props.user.name}\</Text>);
	}
}
export default class App extends Component {
	constructor(props)	{
		super(props);
		this.state = {
			user: null
		}
	}
	componentWillMount() {
		this.fetchUser().then((user) => this.setState({user}));
	}
	fetchUser() {
		return fetch('https://myservice.net/user')
			.then((response) => response.json())
	}
	render() {
		if (this.state.user == null) {
			return (<Text>Loading...</Text>);
		} else {
			return (<User {...this.state.user}>);
		}
	}
}
```

This app uses an `User` component to display the only information available for a user: the name. For fetching the information from a server it is using promises which are 'built in' in ES6 Javascript. The exciting thing is that the render method is automatically called when the state changes *(via setState())*. For lists this is particularly great because the framework will generate a diff and only update the rows that need to be updated after the state change.

When updating the state you don't need to set a whole object. The system will just override the given properties and keeps the rest untouched.

In the next blog post I am going to build a simple example project using React Native and describe this operation more explicitly. Thanks for reading this weeks blog post and I hope to see some of you this evening at Cocoaheads!
