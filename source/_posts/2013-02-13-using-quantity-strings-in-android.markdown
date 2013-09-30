---
comments: true
date: 2013-02-13 21:35:36+00:00
layout: post
title: Using Quantity Strings in Android
categories: [android, plurals, quantity strings]
---

There are a lot of languages in the world. Different languages have different rules. For example, in English we say 1 item, but 0 items, 2 items and so on. Here we have 2 categories. But other languages may have different number of categories. For example, Latvian language have 3 categories. In this post I'll show how to handle such situations in Android. Also I'll create a demo project to demonstrate usage of plurals.

<!-- more -->

Android internally uses language plural rules as in this [table](http://unicode.org/repos/cldr-tmp/trunk/diff/supplemental/language_plural_rules.html). According to it tags are used to represent categories. For English language there are 2 tags: `one` and `other`.
To show how plurals work I will create a sample application.
Android plurals stored in string resources as regular strings. In `res/values/stings.xml` I added the following lines:

``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- ... -->
    
    <plurals name="itemsSelected">
        <item quantity="one">1 item selected</item>
        <item quantity="other">%d items selected</item>
    </plurals>

</resources>
```

This plurlas can be received via [getResources().getQuantityString (int id, int quantity, Object... formatArgs)](http://developer.android.com/reference/android/content/res/Resources.html#getQuantityString(int, int, java.lang.Object...)) method. So to get the right string I call this method:

``` java
private String getSelectedItemsString() {
    int count = getListView().getCheckedItemCount();
    return getResources().getQuantityString(R.plurals.itemsSelected, count, count);
}
```

Notice that I passed `count` twice. This because first parameter is used by android to find the right string and the last parameter will replace `%d` in the second string.
Now if I run the app and select some lines I got the following:  
{% img center /images/2013/02/13/english-plurals.png 'Selected items' 'Selected items' %}

To add plurals for other languages I created `strings.xml` file in `res/values-cs` folder which is used for Czech language. According to the [table](http://unicode.org/repos/cldr-tmp/trunk/diff/supplemental/language_plural_rules.html) Czech language has 3 categories, so `strings.xml` must have the following lines:

``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    
    <plurals name="itemsSelected">
        <item quantity="one">1 položka vybrána</item>
        <item quantity="few">%d vybrané položky</item>
        <item quantity="other">%d vybraných položek</item>
    </plurals>

</resources>
```

Now when device language switched to Czech I got the following:  
<div align="center">{% img /images/2013/02/13/1-czech.png 'Selected items' 'Czech 1 selected' %} {% img /images/2013/02/13/2-czech.png 'Selected items' 'Czech 2 selected' %} {% img /images/2013/02/13/5-czech.png 'Selected items' 'Czech 5 selected' %}</div>
<p>
Quantity strings make it easier to handle rules of different languages. For example in Russian language there are 4 categories. First category is for 1, 21, 31, ... Second category is for 2-4, 22-24, 32-34 and so on. With plurals it's much more easier to translate your apps to different languages.
Full source code for the sample app can be found in my [Github repository](https://github.com/J-rooft/Quantity-Strings).
