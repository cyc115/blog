---
title: "Mobile cross platform development with react-native"
date: 2017-11-12T18:30:54-06:00
draft: true
tags: ["react-native", "mobile"]
cover: http://angular.github.io/react-native-renderer/assets/react.png
---



Lately I've been working on a small mobile demo written in react-native, here are some of the things I've learned along the way. 

Since React-native is such a young project, the toolchain support for the editors are not so great compare to other more established technologies such as Golang or java. Even nuclide, the official editor from facebook, only have basic syntax highting support. There was no editor-linter integration and the crash debug messages are difficult to read. 

![react native](http://angular.github.io/react-native-renderer/assets/react.png)

## Running on IOS simulator

Getting react-native to run in IOS simulator is pretty simple and strait forward. 

You first create a project using `create-react-native-app`. Write your app, then eventually when you are ready to deploy, run `yarn run eject`. 

After that, you will have to run the build server with `yarn start` and build and push your app to the emulator with `yarn run ios` 

Et Voila, a simulator boots up and your app start running. 


## Running on IOS device

Running on device is not as stress free as running in a simulator. I've had multiple problems along the way. 
Here are some that you might face when deploying your IOS app on device for the first time:

### main.jsbundle not created
after `eject`ing from the `create-react-native-app` project, you will have an `ios` and `android` folder set up for you. Look inside your `ios/` folder to see if a file named `main.jsbundle` is there. If not, then go to up one directory and run the following script to build a bundle.

``` bash
react-native bundle --platform ios \
        --dev true \
        --entry-file index.js \
        --bundle-output ios/main.jsbundle
```

### Specify the signing key for your app

Don't forget to obtain an apple developer account and set it up for your xcode project.

![xcode setup](/img/2017-11-11/react1.png)

### App crashes after splash screen

This is pretty difficult to debug since there are no error messages. What happens it that building and running your app from xcode works only the firt time. If you kill the app and then run it from the main screen it will hang (in some cases your phone stops responding) and after a while the app crashes. 

This is because your react native app is built in debug mode and is trying to fetch the `main.jsbundle` file from the build server (on port 8081) instead of using the file that was buildin. 

If the build server is not running, then the unresponsive network thread hangs until timeout. 

To solve this, go to `xcode menu => Product => Scheme => Edit Scheme` and set Build configuration to `Release`

![select build configuration](/img/2017-11-11/react2.png)


## Conclusion 

React-native is pretty cool dispite its immaturaity. Using it requires some up front effort to learn and set up the toolchain (editor, linter, xcode integration, etc) but it is well worth trying if you are familiar with React or if you are creating an application that is largely OS independent. Using react-native allows someone (like myself) who is not familar with Swift and IOS development to quickly create a working POC. 
 