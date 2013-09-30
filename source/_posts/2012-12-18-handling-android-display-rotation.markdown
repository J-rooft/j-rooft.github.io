---
comments: true
date: 2012-12-18 17:52:39+00:00
layout: post
title: Handling android display rotation
categories: [android, orientation, rotation]
---

When screen orientation changes android recreates current activity. You as a developer are responsible for keeping data stored in this activity. In this post I will tell how to save the data when screen rotates and then recreate it.

<!-- more -->

Android will help you in it. For example, EditText content is restored automatically. But not all views do the same. If the app dynamically changes Button text or maybe TextView text it is easy to restore this text too. Just include to this view declaration in XML the following line:

``` xml
    android:freezesText="true"
```

Text in these views will be restored in `Activity.onResume()` or `Fragment.onViewStateRestored(Bundle)` methods.

But if you want to persist some objects or primitives you have to override `onSaveInstanceState(Bundle)` method. This method receives Bundle as a parameter. To store value in it call one of appropriate put* methods. For example, to store integer value call `putInt(String, Integer)` method and pass key for this value and the value itself.

``` java
    @Override
    protected void onSaveInstanceState(Bundle outState) {
        outState.putInt(KEY_FOR_ID, id);
        
        super.onSaveInstanceState(outState);
    }
```

Storing objects is a little bit more complicated. It requires stored objects to implement `Serializable` or `Parcelable`. If it does then call `putSerializable(String, Serializable)` or `putParcelable(String, Parcelable)` method. When activity or fragment is recreated Bundle object with stored values will be passed to the following methods: `Activity.onCreate(Bundle)`, `Activity.onRestoreInstanceState(Bundle)`, `Fragment.onCreate(Bundle)`, `Fragment.onCreateView(LayoutInflater, ViewGroup, Bundle)`, and `Fragment.onActivityCreated(Bundle)`. To get value from the Bundle call an appropriate get* method and pass key you used to store the value to it, e.g. `Bundle.getInt(String)`.

``` java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // some lines are omitted
                
        if (savedInstanceState != null) {
            // if not null then this activity (fragment) is being recreated
            id = savedInstanceState.getInt(KEY_FOR_ID);
        }
    }
```