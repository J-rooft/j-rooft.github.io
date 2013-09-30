---
comments: true
date: 2013-09-01 19:42:35+00:00
layout: post
title: Parcelable in PendingIntent
categories: [alarm, ClassNotFoundException, parcelable, PengingIntent]
---

Android offers a convenient way to run tasks at specific time via [AlarmManager](http://developer.android.com/reference/android/app/AlarmManager.html). If you want to pass some data to this task you should store it in [Intent](http://developer.android.com/reference/android/content/Intent.html). If you want to pass your own classes then they should implement [Parcelable](http://developer.android.com/reference/android/os/Parcelable.html) interface and persist data in writeToParcel() method. But there is an error message in LogCat when passing your parcelables to PendingIntent:  
`ClassNotFoundException when unmarshalling: YourClass`  
{% img /images/2013/09/01/classnotfoundexception-log.png %}

<!-- more -->

First lets take a look at the code which causes such problems. I created Data class which implements Parcelable interface and has 2 fields. Now I create an instance of this class and fill it with some values.
``` java
Data data = new Data();
data.setInt(1);
data.setStr("a");
[/code]
This data must be stored in PendingIntent for later use in BroadcastReceiver. This is how it can be done:
[code language="java"]
// create Intent with data needed
Intent intent = new Intent(BROADCAST_ACTION);
intent.putExtra(DATA_EXTRA, data);
intent.setExtrasClassLoader(Data.class.getClassLoader());

// create PendingIntent from this intent and set up alarm
PendingIntent pi = PendingIntent.getBroadcast(this, 0, intent, PendingIntent.FLAG_CANCEL_CURRENT);

AlarmManager am = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
am.set(AlarmManager.RTC, System.currentTimeMillis(), pi);
```

After you run this code in system LogCat there will be an error message:
``` text
443-488/system_process E/Parcel: Class not found when unmarshalling: com.nocturnaldev.parcelableinpendingintent.Data, e: java.lang.ClassNotFoundException: com.nocturnaldev.parcelableinpendingintent.Data
443-488/system_process W/Intent: Failure filling in extras
        android.os.BadParcelableException: ClassNotFoundException when unmarshalling: com.nocturnaldev.parcelableinpendingintent.Data
        at android.os.Parcel.readParcelable(Parcel.java:2077)
        at android.os.Parcel.readValue(Parcel.java:1965)
        at android.os.Parcel.readMapInternal(Parcel.java:2226)
        at android.os.Bundle.unparcel(Bundle.java:223)
        at android.os.Bundle.putAll(Bundle.java:303)
        at android.content.Intent.fillIn(Intent.java:6248)
        at com.android.server.am.PendingIntentRecord.sendInner(PendingIntentRecord.java:210)
        at com.android.server.am.PendingIntentRecord.send(PendingIntentRecord.java:192)
        at android.app.PendingIntent.send(PendingIntent.java:677)
        at android.app.PendingIntent.send(PendingIntent.java:631)
        at com.android.server.AlarmManagerService$AlarmThread.run(AlarmManagerService.java:828)
```

[Source code](https://android.googlesource.com/platform/packages/apps/DeskClock/+/master/src/com/android/deskclock/Alarms.java) of android clock has an explanation of why this happens: "The AlarmManager adds extra data to this Intent which causes it to inflate. Since the remote process does not know about the Alarm class, it throws a ClassNotFoundException."

Also there is a [bug report](https://code.google.com/p/android/issues/detail?id=6822) created for this issue.

Fortunately this problem can be overcome by making byte array from your Parcelable object and passing it to PendingIntent.
``` java
// First create Parcel and write your data to it
Parcel parcel = Parcel.obtain();
data.writeToParcel(parcel, 0);
parcel.setDataPosition(0);

// now create Intent and store your parcel in it
Intent intent = new Intent(BROADCAST_ACTION);
intent.putExtra(MARSHALLED_DATA_EXTRA, parcel.marshall());

// create PendingIntent from this intent and set up alarm
PendingIntent pi = PendingIntent.getBroadcast(this, 0, intent, PendingIntent.FLAG_CANCEL_CURRENT);

AlarmManager am = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
am.set(AlarmManager.RTC, System.currentTimeMillis(), pi);
```

Later when your alarm will be fired you can extract your data:
``` java
byte[] byteArrayExtra = intent.getByteArrayExtra(MARSHALLED_DATA_EXTRA);
Parcel parcel = Parcel.obtain();
parcel.unmarshall(byteArrayExtra, 0, byteArrayExtra.length);
parcel.setDataPosition(0);
Data data = Data.CREATOR.createFromParcel(parcel);
```

Now there are no more errors and LogCat clean and neat.

As always source code of sample app is available on [GitHub](https://github.com/J-rooft/Parcelable-In-PendingIntent).

Used materials:
Android Clock [Alarms](https://android.googlesource.com/platform/packages/apps/DeskClock/+/master/src/com/android/deskclock/Alarms.java) and [AlarmReceiver](https://android.googlesource.com/platform/packages/apps/DeskClock/+/master/src/com/android/deskclock/AlarmReceiver.java) classes.
