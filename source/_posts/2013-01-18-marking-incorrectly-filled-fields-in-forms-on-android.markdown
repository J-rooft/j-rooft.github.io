---
comments: true
date: 2013-01-18 11:19:20+00:00
layout: post
title: Marking Incorrectly Filled Fields in Forms on Android
categories: [android, error indicator, form]
---

Filling forms in Android is not as easy as on desktops because of a lack of the hardware keyboard. When the user filled a form incorrectly instead of showing a popup the app can somehow highlight wrong items in the form. Android have convenient way of doing this.

<!-- more -->

Android TextView and all its subclasses (e.g. Buttons, CheckBoxes) have [TextView.setError(CharSequence)](http://developer.android.com/reference/android/widget/TextView.html#setError(java.lang.CharSequence)) method. Use this method when you want to mark a field with incorrect data. Simply call setError() on the view that contains an error.

``` java
editText.setError(getString(R.string.must_be_filled));
chbx.setError(getString(R.string.must_be_checked));
radionBtn.setError(getString(R.string.must_be_checked));
btn.setError(getString(R.string.unknown_error));
```

In the code above I set error to 4 views. Take a look at the following a screenshot.  
{% img center /images/2013/01/18/form-with-default-error-drawables.png 'Form with default error drawables' %}

As you can see on the screenshot above all 4 views have error indicator but only edittext is able to show error text by default. To allow displaying an error message for other views they must set focusableInTouchMode parameter to true. By doing this you allow the view to get focus and when a user taps on it error message will be shown. You can set this parameter to true in xml by using [android:focusableInTouchMode](http://developer.android.com/reference/android/view/View.html#attr_android:focusableInTouchMode) or in code by using [View.setFocusableInTouchMode(boolean)](http://developer.android.com/reference/android/view/View.html#setFocusableInTouchMode(boolean)). I prefer to set it to true only when there is an error and set it to false otherwise. This is because in this mode user needs to make 2 taps to change the checked state or press the button: first tap to focus a view and second tap to interact with it.  
{% img center /images/2013/01/18/form-with-default-error-drawables-focusable.png 'Form with default error drawables focusable' %}

Also Android allows us to change error icon. To set custom icon use [TextView.setError(CharSequence, Drawable)](http://developer.android.com/reference/android/widget/TextView.html#setError(java.lang.CharSequence, android.graphics.drawable.Drawable)) method when setting an error.
Icon sizes must be 20 x 20 dp size. It is 15 x 15 pixels in ldpi mode, 20 x 20 pixels in mdpi, 30 x 30 pixels in hdpi and 40 x 40 in xhdpi. Lets set an error to one of the views using custom error drawable. To do it I modified the code to set custom drawable to checkbox when calling setError() method:

``` java
editText.setError(getString(R.string.must_be_filled));
Drawable customErrorDrawable = getResources().getDrawable(R.drawable.custom_error);
customErrorDrawable.setBounds(0, 0, customErrorDrawable.getIntrinsicWidth(), customErrorDrawable.getIntrinsicHeight());
chbx.setError(getString(R.string.must_be_checked), customErrorDrawable);
radionBtn.setError(getString(R.string.must_be_checked));
btn.setError(getString(R.string.unknown_error));
```

Here I set custom error icon to a checkbox. Note that you need to set bounds to your drawable otherwise it will not be drawn.
Now checkbox has different error indicator:  
{% img center /images/2013/01/18/form-with-custom-error-drawable.png 'Form with Custom Error Drawable' 'Form with Custom Error Drawable' %}

That is how to indicate incorrectly filled fields.
Android project used in this post can be found in my [Github repository](https://github.com/J-rooft/android-forms-with-error-indicators).
