---
comments: true
date: 2012-11-12 20:30:05+00:00
layout: post
title: How to use LocalBroadcastManager in Android
categories: [android, broadcast, broadcast receiver]
---

Broadcast messages are widely used in android, for example when you want to know current buttery level or scan for wifi networks you have to set up a broadcast receiver. There are two ways to set up broadcast receiver. You can declare them in the manifest file (AndroidManifest.xml) or you can register receiver during runtime and unregister it when it is no longer needed. The main difference is that receiver declared in manifest will receive messages even when the app is not running.

<!-- more -->

To register a receiver we need to specify what actions (events) we will be listening to. Every receiver can listen to one or more actions.

When we send a broadcast a receiver gets an instance of Intent class. Using this intent we can know what kind of action happens (in case we listen to more than one action). Also this intent has convenient key-value storage called Bundle.

Here is a simple example of how to register receiver programmatically and send a broadcast with some text to it. First you need create and register a receiver:

``` java
    // an action to listen
    public static final String MESSAGE_SENT_ACTION = "com.nocturnaldev.MESSAGE_RECEIVED_ACTION";
    // key to extract a text from bundle
    public static final String MESSAGE_EXTRA = "com.nocturnaldev.MESSAGE_EXTRA";
    
    // make receiver a member variable of a class to be able to unregister it later
    private BroadcastReceiver receiver;
    
    // ...
    
    // in onResume() initialize and register this receiver
    @Override
    protected void onResume() {
        super.onResume();
        
        receiver = new BroadcastReceiver() {
             
            @Override
            public void onReceive(Context context, Intent intent) {
                Bundle bundle = intent.getExtras();
                String message = bundle.getString(MESSAGE_EXTRA);

                // do something useful with message
            }
        };
        
        // here we specify MESSAGE_SENT_ACTION as an action 
        registerReceiver(receiver, new IntentFilter(MESSAGE_SENT_ACTION));
    }
```

Now you will be notified when broadcast with aforementioned action will be sent:

``` java
    Intent broadcastIntent = new Intent();
    broadcastIntent.setAction(MainActivity.MESSAGE_SENT_ACTION);
    broadcastIntent.putExtra(MainActivity.MESSAGE_EXTRA, "some text");
    sendBroadcast(broadcastIntent);
```    

When receiver is no longer needed simply call `Context.unregisterReceiver`:

``` java
    @Override
    protected void onPause() {
        super.onPause();
         
        if (receiver != null) {
            unregisterReceiver(receiver);
            receiver = null;
        }
    }
```

But this code has some important points. According to [documentation](http://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html) other applications will be able to listen to your broadcasts and send broadcasts to your app. Also sending broadcasts through global broadcast system is not so efficient as sending them through local one. That is why in support package v4 Google introduced LocalBroadcastManager.

If you send and receive broadcasts only inside your app you may start using it. It's not so hard. Instead of using activity's context (as well as service's, application context and so on) you have to obtain an instance of LocalBroadcastManager and use it to register/unregister receiver as well as send broadcasts. Let's see how to modify the previous program to use LocalBroadcastManager. We only need to change 3 lines of code.

Register a receiver:

``` java
    LocalBroadcastManager.getInstance(this).registerReceiver(receiver, new IntentFilter(MESSAGE_SENT_ACTION));
```

Send a broadcast:

``` java
    LocalBroadcastManager.getInstance(this).sendBroadcast(broadcastIntent);
```

Unregister a receiver:

``` java
    LocalBroadcastManager.getInstance(this).unregisterReceiver(receiver);
```

After this changes your app will be using system more effectively.

Full source code of examples in this article can be found on github:  
1. [BroadcastReceiver-Tutorial](https://github.com/J-rooft/BroadcastReceiver-Tutorial)  
2. [LocalBroadcastManager-Example](https://github.com/J-rooft/LocalBroadcastManager-Example)  
The apps on github starting a background service after tapping on a button. This service notifies our activity when it starts and finishes (after 3 seconds) via broadcasts.
