+++
draft = false
author = "Benjamin Herzog"
share = true
comments = true
image = "images/react-native.jpg"
menu = ""
date = "2017-04-04T18:00:00+02:00"
title = "React Native Example"
tags = ["react"]
slug = "rn-example"

+++

In this post I am going to describe what **steps** are necessary to create an app in **React Native**. The example I want to build is the same one I created during my **Cocoaheads** talk. If you would like to see this **in action** or get more information check out my last post [here]({{< ref "post/11-About-React-Native.md" >}}).

### Installing dependencies

You may already know this: in Javascript it is **all** about **dependencies**. The language itself is pretty easy and does not have a lot **functionality**. In other environments you have rich **SDKs** *(like iOS/Android SDK, .Net, …)* in Javascript this is mostly **not** the case. Since you do not want to reinvent the wheel with every project it is common to use different **libraries** for different tasks.

I am not sure if this is a **good** or a **bad** thing. In general using dependencies makes you depending on those, that is where the name is coming from. But it is still good to see that the community created solutions for **everything** and it is very easy to build on top of that.

To install the dependencies you need to create this example app I recommend using the package managers [**brew**](https://brew.sh) and [**yarn**](https://yarnpkg.com/en/). **yarn** is a replacement for the **node package manager** ([npm](https://www.npmjs.com)) built by Facebook. It is not needed, you can also use npm. Just translate my given **yarn** commands to npm commands (mostly replace `add` by `install`).

```Bash
# install node, watchman and yarn via brew
$ brew install node watchman yarn

# install the command line tool to create a pre-configured React Native app
$ yarn global add create-react-native-app
```

It is not needed to create a React Native app via the command line interface; you could instead also **configure** all the files by your own. To create an app execute the following command:

```Bash
$ create-react-native-app AwesomeProject
$ cd AwesomeProject # change directory to the created one
```

The creacted file structure should look similiar to this:

```
.
|-- App.js
|-- App.test.js
|-- README.md
|-- app.json
|-- node_modules
|   `-- …
|-- package.json
`-- yarn.lock
```

Inside the `package.json` there are a few defined **commands** you can use via `yarn`. `start` starts the **development server** *(useful when the app is already compiled and installed in the simulator)*. `android` and `ios` compiles the respective platform **native app** and opens an iOS simulator or Android emulator. As it is native code, it **takes some time** to finish but it is not necessary to do this **often** or even twice. `test` starts **jest** to execute tests written for the app code.

Another one is `eject` which changes the structure of the **whole** directory. Currently the setup is built on top of [Expo](https://expo.io) which makes it possible to create React Native apps written on your computer and run them **immediately** in your devices via the Expo native app. This is very cool since you don't even need to have the **SDKs** *(Xcode/Android Studio)* installed. The **drawback** is that you do not even have a dedicated native project so it is not possible to upload the app to the **App/Play Store** for example. It is also not possible to create **native extensions** for your app.

`eject` fixes this by re-structuring your app. After running 

```Bash
$ yarn eject
```

your file structure should look like this:

```
.
|-- App.js
|-- App.test.js
|-- README.md
|-- android
|   |-- app
|   |   |-- BUCK
|   |   |-- build.gradle
|   |   |-- …
|-- app.json
|-- index.android.js
|-- index.ios.js
|-- ios
|   |-- AwesomeProject
|   |   |-- AppDelegate.h
|   |   |-- …
|-- node_modules
|   `-- …
|-- package.json
`-- yarn.lock
```

It is recommended to delete the `node_modules` folder and run `yarn install` to refresh the installation of the dependencies. Be aware that this operation is **permanent** and can not be revoked!

### Building the app

Since this blog post is only about **getting started** I would like to create a very simple project showing **recipes** for a hard coded ingredients list. The data is coming from [**Receipt Puppy**](http://www.recipepuppy.com) and will be requested via `http://www.recipepuppy.com/api/?i=eggs,bread`, so our ingredients are **eggs** and **bread**.

Since we would like to use `FlatList` we need to use some **unstable** versions of react and react-native. Replace the dependencies in the package.json file with this:

```
"dependencies": {
	"react": "16.0.0-alpha.6",
	"react-native": "0.44.0-rc.0"
}
```

After `yarn install` we are ready to start. In the app we open the **App.js** and replace the `class App` with the following code:

```Javascript
export default class App extends React.Component {

  constructor(props) {
    super(props)
    this.state = {
      data: []
    };
  }

  componentWillMount() {
    fetch('http://www.recipepuppy.com/api/?i=eggs,bread')
      .then(response => response.json())
      .then(json => this.setState({data: json.results.map((e, i) => {
        return {
          title: e.title,
          key: i + Date.now()
        }
      })}));
  }

  render() {
    return (
      <View style={{flex: 1}}>
        <FlatList style={{height: '100%', width: '100%'}}
        data={this.state.data}
        renderItem={item => <Cell title={item.item.title} />}
        />
      </View>
    );
  }
}
```

As you see, wee set the **state** to an initial value in the **constructor** of the class. In the `componentWillMount` lifecycle method we execute the **http request** and reload the state with the manipulated data *(we only want to see the title)*. For rendering the items we pass in a function which returns a `Cell`. `Cell` is another **component** we define in this file:

```Javascript
class Cell extends React.Component {
  render() {
    return (
      <View>
        <Text>{this.props.title}</Text>
      </View>
    );
  }
}
```

To keep things **simple** we only display the **title** of the meal with any more user interface.

You can now **build the app** and run it by executing `yarn ios` in the **Terminal**. You will realize that it is **not working**. After all React-Native is a wrapper over UIKit; so every http request is implemented using `URLRequest`. Apple introduced earlier that iOS apps only allow https calls by default. The webservice we are using is *(unfortunately)* not available via **https**, so we need to define an **exception** in the `Info.plist`. Open the `Info.plist` file by typing `open ios/AwesomeProject/Info.plist` in the Terminal. You should add an entry **'Allow Arbitrary Loads'** to the **'App Transport Security Settings'** entry with a value of **'YES'**.

After that change the app needs to be **re-compiled** by executing `yarn ios`. Then you should see a screen similiar to this:

![](/images/posts/react-native-example.png)

Yes, it is **not pretty** but already **functional**! Using other components like `Image` or `TextInput` could improve this also. Feel free to **add additionally functionality** and improvements in the **design**!
