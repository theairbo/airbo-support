# Adding SupportKit to your app

There are a few ways you can include the SupportKit plugin on your Android project.

The SupportKit library comes in both the AAR and JAR packages. The AAR package is the preferred one but if you are not using Android Studio, you will need to install the JAR. The current supported `compiledSdkVersion` must be at least **21**.

## Android Studio with Gradle
<span class="badge">[![Bintray](https://api.bintray.com/packages/supportkitorg/maven/supportkit/images/download.svg)](https://bintray.com/supportkitorg/maven/supportkit/_latestVersion)</span>

The SupportKit library is hosted on [Bintray](https://bintray.com/supportkitorg/maven/supportkit) and is available on [JCenter](http://jcenter.bintray.com/io/supportkit/).

Setup JCenter as a repository.

```
repositories {
    jcenter()
}
```

Add the dependencies right into your `build.gradle` file.

```
compile 'com.google.android.gms:play-services-gcm:7.5.0'
compile 'com.google.code.gson:gson:2.3.1'
compile 'com.squareup.okhttp:okhttp:2.4.0'
compile 'io.supportkit:core:latest.release'
compile 'io.supportkit:ui:latest.release'
```

Sync the Gradle project then add the necessary code to [initialize SupportKit in your app](#initialize-supportkit-in-your-app).

<aside class="notice">
SupportKit depends on a few external libraries that many applications already feature. For this reason, it does not bundle those dependencies inside the AAR or JAR packages.
</aside>

## Eclipse, Netbeans and the ant build system

### Core and UI modules

On Eclipse, we need to retrieve our `eclipse` package to add SupportKit and our dependencies to your project. Also, we will need to manually add permissions and services to the manifest.

* Download the `supportkit-eclipse-x.x.x.zip` file from [Bintray](https://bintray.com/supportkitorg/maven/supportkit). The package is located in `Files/ui/x.x.x/supportkit-eclipse-x.x.x.zip`.
* Extract the zip and copy the `appcompat` and `supportkit` folders to **the root of your project**.

<span class="half-width-img">![eclipse import](images/eclipse_import.png)</span>

* In Eclipse, import both folders in your workspace as `Existing Android Code`. After this step you should have two new projects added to your workspace named `android-support-v7-appcompat` and `ConversationActivity`.

* Expand the `android-support-v7-appcompat\libs` folder. Then on **every** library, right click on them and `Build Path` > `Add to Build Path`.

<span class="half-width-img">![eclipse libraries](images/eclipse_libraries.png)</span>

* Go to the properties of your project (i.e. `right-click` > `Properties`). Then under the Android item, add a reference to the SupportKit's `ConversationActivity`.

* Add these permissions in **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE"/>
```

* Add these services in **AndroidManifest.xml**:

```xml
<service android:name="io.supportkit.core.service.SupportKitService"/>
<service android:name="io.supportkit.core.GcmInstanceIDListenerService" android:exported="false">
    <intent-filter>
        <action android:name="com.google.android.gms.iid.InstanceID"/>
    </intent-filter>
</service>
<service android:name="io.supportkit.core.GcmRegistrationIntentService" android:exported="false"/>
<service android:name="io.supportkit.core.GcmService" android:exported="false">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
    </intent-filter>
</service>
<receiver
        android:name="io.supportkit.ui.notification.NotificationReceiver"
        android:exported="false">
    <intent-filter>
        <action android:name="io.supportkit.NOTIFICATION"/>
    </intent-filter>
</receiver>
```

* Add this activity in **AndroidManifest.xml**:

```xml
<activity android:name="io.supportkit.ui.ConversationActivity" android:theme="@style/Theme.SupportKit" />
```

* Follow [these instructions to install the Google Play Services](https://developers.google.com/android/guides/setup) to your project.

Clean and build your project then add the necessary code to [initialize SupportKit in your app](#initialize-supportkit-in-your-app).

### Core module only

If you are planning on building your own UI and only [use our API](http://docs.supportkit.io/api/android/io/supportkit/core/package-summary.html), you can integrate the core module only.

* In your project directory, create a subdirectory called **libs** if it does not already exist
* Copy **SupportKit-core.jar** into the **libs** directory
* Add these permissions in **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE"/>
```
* Add these services in **AndroidManifest.xml**:

```xml
<service android:name="io.supportkit.core.service.SupportKitService"/>
<service android:name="io.supportkit.core.GcmInstanceIDListenerService" android:exported="false">
    <intent-filter>
        <action android:name="com.google.android.gms.iid.InstanceID"/>
    </intent-filter>
</service>
<service android:name="io.supportkit.core.GcmRegistrationIntentService" android:exported="false"/>
<service android:name="io.supportkit.core.GcmService" android:exported="false">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
    </intent-filter>
</service>
```

* Download and copy each dependency JAR into the **libs** subdirectory:
 * [Google-Gson](https://code.google.com/p/google-gson/)
 * [OkHttp and Okio](https://square.github.io/okhttp/)

* Follow [these instructions to install the Google Play Services](https://developers.google.com/android/guides/setup) to your project.

#### Troubleshooting

If you get the following error when running your application

```
[2015-07-23 09:33:42 - Dex Loader] Unable to execute dex: Multiple dex files define Lio/supportkit/ui/BuildConfig;
[2015-07-23 09:33:42 - MainActivity] Conversion to Dalvik format failed: Unable to execute dex: Multiple dex files define Lio/supportkit/ui/BuildConfig;
```
The fix is:

* Eclipse project properties > `Java build path` > `Order and export`
* Uncheck Android private libraries.

See [http://stackoverflow.com/a/26856255/213272](http://stackoverflow.com/a/26856255/213272).

## Initialize SupportKit in your app

After following the steps above, your app is setup for working with the SupportKit SDK. Before your code can invoke its functionality, you'll have to initialize the library using your app's token.

This token is free and uniquely identifies your app and links it to the SupportKit backend that does the heavy lifting necessary to bridge the gap between you and your users.

You can find your token by [logging into SupportKit](https://app.supportkit.io) and copying it from the settings page as shown below.

![App Token on Overview Page](/images/apptoken.png)

Once you've located your token, use the code below to initialize SupportKit.

Add the following line of code to your `onCreate` method on your [Application](http://developer.android.com/reference/android/app/Application.html) class:

```java
SupportKit.init(this, "YOUR_APP_TOKEN");
```
<aside class="notice">
    Make sure to replace `YOUR_APP_TOKEN` with your app token.
</aside>

If you don't have an `Application` class, we recommend that you create one to make sure SupportKit is always initialized properly. If you don't have one, you can copy the following and save it to your application package.

```java
package your.package;

import android.app.Application;
import io.supportkit.core.SupportKit;

public class YourApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        SupportKit.init(this, "YOUR_APP_TOKEN");
    }
}
```

You also need to **declare** your newly created `Application` class in the `<application>` tag in your `AndroidManifest`.

```xml
<application
    android:name="your.package.YourApplication">
    ...
</application>
```

<aside class="notice">
    Remember to replace `your.package`, `YourApplication`, `YOUR_APP_TOKEN` by the appropriate names and your app token.
</aside>

### Displaying the SupportKit User Interface

Once you've initialized SupportKit, you're ready to try it out. 

Find a suitable place in your app's interface to invoke SupportKit and use the code below to display the SupportKit user interface. You can bring up SupportKit whenever you think that your user will need access to help or a communication channel to contact you.

```java
ConversationActivity.show(this);
```

You should also take the time to [configure the push notifications setup](#configuring-push-notifications).
