---
comments: true
date: 2013-04-27 15:30:06+00:00
layout: post
title: Using ViewStub on Android
categories: [android, ViewStub]
---

Sometimes in android we want to display a view in some cases and hide it in others. Instead of adding this view and making it hidden at startup we can use [ViewStub](http://developer.android.com/reference/android/view/ViewStub.html).

<!-- more -->

As said on android developers site: "A ViewStub is an invisible, zero-sized View that can be used to lazily inflate layout resources at runtime". So we can use it to save resources if there is possibility that this view may never be shown. Also ViewStub can be shown later when it needed.

Let's create a simple app that uses ViewStub. It will contain a login screen where a user can enter his credentials or skip this step and start using app without authentication.

A login screen initially will have two buttons to skip login and to enter credentials.  
{% img center /images/2013/04/27/initial-screen.png %}

Once the user presses "Enter credentials" button a new view inflated from ViewStub will appear. This view contains fields to enter login and password. To make login and password fields visible you need to obtain an instance of ViewStub and call [inflate()](http://developer.android.com/reference/android/view/ViewStub.html#inflate()) on it or change its [visibility](http://developer.android.com/reference/android/view/ViewStub.html#setVisibility(int)) to View.VISIBLE.  
{% img center /images/2013/04/27/visible-login-password.png %}

So ViewStub is very easy to use component of android. Also it is very cheap to create.

As always source code is freely available on [GitHub](https://github.com/J-rooft/ViewStub-example).
