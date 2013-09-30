---
comments: true
date: 2013-05-28 09:47:52+00:00
layout: post
title: Using spannable strings in android
categories: [android, spannable, string, text]
---

Users want to use beautiful and well-designed apps. Appearance of a text in TextView in android can be changed by using html page or creating spannable string. This post tells about the latter. For example, we can modify any part of the string as shown on this screenshot:  
{% img center /images/2013/05/28/colored.png %}

<!-- more -->

If you want to change text appearance dynamically then spans are more preferred then html pages. Spannable strings are easy to create and use. We must simply create and assign a span to a part of a string. I'll create an app demonstrating it. Initially on the screen there is a TextView with three buttons. First two buttons change text appearance by using spans and the third button clears any previously created spans.  
{% img center /images/2013/05/28/initial.png %}

In this app I use only two spans: [ForegroundColorSpan](http://developer.android.com/reference/android/text/style/ForegroundColorSpan.html) to make red text and [UnderlineSpan](http://developer.android.com/reference/android/text/style/UnderlineSpan.html) to underline text. And of course there are a way more spans you can use. Let me describe some of them:

  * [RelativeSizeSpan](http://developer.android.com/reference/android/text/style/RelativeSizeSpan.html) to change text size  
  * [StrikethroughSpan](http://developer.android.com/reference/android/text/style/StrikethroughSpan.html) to strike through the text
  * [URLSpan](http://developer.android.com/reference/android/text/style/URLSpan.html) to make URLs in text
  * [ClickableSpan](http://developer.android.com/reference/android/text/style/ClickableSpan.html) to make text clickable (onClick method will be called)
  * [StyleSpan](http://developer.android.com/reference/android/text/style/StyleSpan.html) to make text bold and/or italic
  * etc.

Other spans can be known by looking at subclasses of [ParcelableSpan](http://developer.android.com/reference/android/text/ParcelableSpan.html).

ClickableSpan and URLSpan require movement method to be set:
``` java
textView.setMovementMethod(LinkMovementMethod.getInstance());
```

Spannable text is created by assigning spans to spannable string. So first we must create a string with text that is able to apply spans. Let's use [SpannableString](http://developer.android.com/reference/android/text/SpannableString.html) for it:
``` java
String text = "Words in this sentence can be colored in red or underlined.";
SpannableString spannable = new SpannableString(text);
```

Now when we have spannable string we can assign spans to it by calling [Spannable.setSpan(Object span, int start, int end, int flags)](http://developer.android.com/reference/android/text/Spannable.html#setSpan(java.lang.Object, int, int, int)) method. First parameter in this method is simply one of spans described earlier. Second and third parameters specify boundaries of span. The last parameter is flag to specify how text should behave when spannable string changes. It is not used for static text and we can set it to 0.

To remove previously added span call [Spannable.removeSpan(Object span)](http://developer.android.com/reference/android/text/Spannable.html#removeSpan(java.lang.Object)) on it.

Once spannable string is ready we can assign it to TextView. You must tell TextView that string you created is spannable string:
``` java
textView.setText(spannableText, BufferType.SPANNABLE);
```

Spannable strings are simple and easy to use feature that helps you to make your app nicer.

As always app source code is available on [GitHub](https://github.com/J-rooft/Spannable-Strings).
