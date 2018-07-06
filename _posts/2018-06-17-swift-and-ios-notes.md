---
layout: post
title: 'Swift and iOS App Development Learning Notes (in Progress)'
date: 2018-06-17
cover: 'https://raw.githubusercontent.com/ServiceStack/Assets/master/img/release-notes/swift-logo-banner.jpg'
tags: English Programming iOS Swift
---

### Introduction and Background

After I left my last company, I feel it is the time for me to do something I’d always want to do — build the movie App
I like. Being an iPhone and MacBook user for years,  it is natural for me to study Swift and iOS development. Luckily, I kinda find a book in my office when I left, it was left there, perhaps one year ago to lift up monitors. So it starts, the notes and thoughts about Swift programming.


### Chapter 1 - Cocoa Development Tools

This chapter covers the brief history of Cocoa Tools, and some general concepts regarding Xcode and iOS App development in general such  as distribution, which could be found in Apple’s Developer Website in greater details, plus some really really basic hand’s on practice, and of course a tour around the Xcode.

One bad thing about learning programming from book is that you could not simply copy and paste the example codes into the editor, but it is making me to get used to the IDE. Different IDEs have different code completion style and being familiar with that would tremendously increase your productivity, so I am definitely not complaining that. Plus, it always feels good to write in new languages.

With my current understanding, it seems like that the design of iOS App could be separated into two components, the `interface` and the `connections`, which can be then further divided into `outlets` (and a `outlet collections`) and `actions`

I do want to complain about one thing, the book is outdated, like for 2 years I guess. Thus, the example code is also not correct. However, it is not much a big issue because you could always googled out the changes, plus you get to see the “patch notes” in real time.

Lastly regarding TestFlight, I would probably try HockeyApp first, but that is the late stage, let me see how things go first.


### Chapter 2 - Programming with Swift


The second Chapter covers the language in greater detail. It first introduces the features of Swift in comparison to other programming languages, which includes:

1. safety - strongly typed, usually no null object and of course you have `let` to safeguard your variables, which feels pretty `safe` as a Python engineer previously.
2. modernity - includes modern design features like `Switches`, `Closures` and `Extensions`, though I do not know what they are yet, sounds like something I would like, readability
is really important to me
3. power - talks about the language's relationship and "seamlessly bridged" to Objective-C


The general naming conventions so far seems to follow lower camel case.


#### Variables and Constants

- use as much constants as possible
- variables are allowed not to have values during declaration, but cannot be accessed unless assigned a value
- constants must have values


#### Types

1. `nil` to represent no value
2. `optional variable` is defined to stored both the original data type and a `nil`
3. `unwrap` - since you have the `optional variable` concept, you need to access the real value, instead of type casting directly, swift introduces `!`


Some features for different data structures:

- The tuples in Swift behaves somewhat similar the tuples and dictionaries in Python. but instead of accessing it using string slicing, tuple contents are accessed using `keys`, defined
explicitly or implicitly.

- The arrays usually holds homogeneous data types. This is safer I guess, unlike Python's list, though sometimes you will find a flexible list structure is quite
handy. However, it is possible to mix different types of data in one array. It requires an explicit declaration of type `Any` for the array.

```{swift}
var myArray : [Any] = [1, 2, "3"]
```

- Dictionaries function basically the same as the one in Python, and they are unordered like always.

- Control flows are normal so far, perhaps the only thing that excites me so far is the `if-let` statement, functions as a normal `if` and an special `try` clause

- Switches are really good, I have not been using switches for a very long time because there is simply no switch statement in Python,
and the last time I wrote Java was probably one year ago, and I always forgot about the `break;`. No more `break;` thank you.

- concept of functionLabel

- concept of variable number of arguments

```{swift}
func sumNumbers(numbers: Int...) ...
```

#### Classes and Inheritance

- generally similar to other languages

- no multiple inheritance

- deinitialiser for "memory management"

- computed properties, getter and (optional) setter: adding computational statements in the getter/setter clauses of
properties to save one step (supposedly achievable by declaring a separate method)

- observers: additional execution block before and after setting the value of a property

- lazy property: useful for expensive declaration in initialisation

- protocols: basically interfaces in java, a detailed differences is [here](https://stackoverflow.com/questions/30859334/compare-protocol-in-swift-vs-interface-in-java)

- extensions: similar to inheritance but do not have to know the original source code, plus increase readability

- possible to extend a TYPE to comform a protocol

- implementation of customized operators is also possible ("+")


#### Access Control

- three level access control: public, private plus (default) internal

- internal: application, library/framework level

- read-only access for properties


#### Others

- Generics is similar in Java

- retain cycles are tackled by weak reference


#### Design Patterns

- Model-View-Controller

- Delegation: A class acts as a delegate of another (or many others)

- Notifications


### Chapter 3 - What is an Application

- different conventions in shipping out Apps
- composition of Apps
- the Application Life Cycle are largely similar for iOS Apps and OS X Apps
- except multitasking because of the memory constraints of the mobile devices

- foreground vs background
- foreground vs inactive (phone call example)
- when the application exits (user hit the home button), the app is suspended (not executing code, remained in memory, but stops consuming other resources)
- App in the background could be terminated without notice when memory is not enough, data must be saved before moving into background state
- background fetching and background notifications

- The Application Sandbox





