---
layout: post
title: "Managers vs Controllers"
date: 2013-07-03 15:10
comments: true
categories: iOS, Design Patterns
---
A conversation on twitter last week got me thinking about how I name my classes, particularly the difference between a Manager and a Controller class.

For me, a Manager is the go-to-guy for a particular aspect of functionality or data. For example, a <code>FileManager</code> handles anything related to accessing the file system. A Manager class contains only class methods, or is implemented as a Singleton, as no separate instances are usually required.

``` objective-c
[[MyFileManager sharedManager] fileExistsAtPath:myPath]; // Singleton
```
or
``` objective-c
[MyFileManager fileExistsAtPath:myPath]; // Class method
```

A Controller object co-ordinates a process. For example, a <code>PurchaseController</code> controls the series of actions in purchasing a product. A <code>ViewController</code> co-ordinates a series of actions for interacting with a View.

``` objective-c
MyPurchaseController *purchaseController = [[MyPurchaseController alloc] initWithDelegate:self];
[purchaseController purchaseProduct:productId]; // Begins purchase process
...
[purchaseController cancelPurchase]; // Cancels process
```

Everyone has their own naming preferences, and yours might be different. It's not really the names themselves that's important, but that the naming is consistent throughout the application. Remember, it's all about communicating intent to yourself and others who use your code.