---
layout: post
title: NSNotifications with MCNotificationCenter
description: "Using MCNotificationCenter to manage notifications and observers in iOS."
tags: [NSNotification, notification, observer, MCNotificationCenter, open-source]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

## Notifications in iOS

NSNotificationCenter and NSNotifications are a key part of any iOS app. Notifications allow for communication between different classes and NSNotificationCenter provides the central location for these interactions. [Read more here][1]

While this system works great most of the time there are a couple ways of running into problems while registering observers and sending notifications.

## Who's watching?

Recently I was working on an app where I was adding and removing observers on lots of classes that were being alloc'd and dealloc'd repeatedly and quickly. On top of that these classes had components (AVPlayers mostly) that were using KVO as well. 

This presents a tricky situation becuase when an observer is added it must also be removed. If an observerving class is deallocated before an observer is removed and the notification is received by that observer the app will crash.

That's okay so we just have to remove our observers before the class is deallocated. But what if we already removed the observer and we try to remove it again? This causes a crash as well.
Sure so we can just check to see if we're registered as an observer before removing the class, right? Unfortunately, NSNotificationCenter doesn't provide a way to do that.

## Handle the handlers

So I wrote `MCNotificationCenter` to fix these things. 

* MCNotification center keeps references to all registered observers
* Calls to removeObserver first check if the observer is actually registered
* Check if a class is an observer, an observer for an object, name etc. 
* Get all observers for a class
* Send a notification only if there is an observing class

## KVO

KVO was also a little clunky in that it requires an override to the observing method and lots of logic to handle it. 

I wanted to be able to register key value observers with a block, so MCNotificationCenter can do that as well. There is also a convenience class in the library `NSObject+EasyKVO` that provides methods for adding and removing an observer with just a keypath and block. Cool!


## Check it out

The code is completely open source and available on Github: [`MCNotificationCenter` on Github][2]. There's also some documentation there.

And you can grab the library with cocoapods:

```sh
$ pod 'MCNotificationCenter'
```

[1]: http://nshipster.com/nsnotification-and-nsnotificationcenter/
[2]: https://github.com/maxcampolo/MCNotificationCenter
