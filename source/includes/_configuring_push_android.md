## Configuring push notifications

Push notifications are a great, unobtrusive way to let your users know that a reply to their message has arrived.

### Step 1. Generate a GCM configuration file for your Android project

<aside class="notice">
Following these steps will enable cloud messaging for your app and create a server API key
</aside>

1. Go to Google's [cloud messaging documentation](https://developers.google.com/cloud-messaging/android/client#get-config)

1. Click on `GET A CONFIGURATION FILE` (you will be prompted to sign in to your Google account if you are not already signed in)

1. Follow the steps to generate your configuration file (for your package name, copy and paste the package used in your application's AndroidManifest.xml file)

1. Enable **Google Cloud Messaging** to your Android project

1. At the end of the page, continue to *Generate configuration files*

1. Take note of the *Server API Key* and the *Sender ID*, we will need those in the second step

1. Download the `google-services.json` file generated by the tool and save the file at the root of your Android application

### Step 2. Configure push notifications in SupportKit

1. Go to [https://app.supportkit.io](https://app.supportkit.io) and log into your account.

2. Go to **Settings** > **Android**

3. Enter your *Server API Key* and *Sender ID* (which was retrieved earlier in Step 1)

### Step 3. Configure Android application to accept push

<aside class="notice">
Be sure to replace `<your_application_package>` with your application package!
</aside>

1. Add the dependency to your project's top-level `build.gradle`:

    ```
    classpath 'com.google.gms:google-services:1.3.0-beta1'
    ```

2. Add the plugin to your app-level `build.gradle`:

    ```
    apply plugin: 'com.google.gms.google-services'
    ```

3. Add the following permissions and uses-permissions to your `AndroidManifest.xml` file (typically before the opening `<application>` tag):

    ```xml
    <permission
        android:name="<your_application_package>.permission.C2D_MESSAGE"
        android:protectionLevel="signature"/>
    
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="<your_application_package>.permission.C2D_MESSAGE"/>
    ```

4. Add the following receiver to your `AndroidManifest.xml` file before the closing `</application>` tag:

    ```xml
    <receiver
            android:name="com.google.android.gms.gcm.GcmReceiver"
            android:exported="true"
            android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
            <category android:name="<your_application_package>"/>
        </intent-filter>
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
        </intent-filter>
    </receiver>
    ```

### Step 4. Test it out!

<aside class="warning">
You can't receive push notifications in the Android simulator, you must use a physical device.
</aside>

1. Kill and restart your app.

2. Launch SupportKit.

3. Send a message.

4. Press the home button or navigate away from the conversation.

5. Reply to the message from your choice of SupportKit integrated service

You'll receive a notification on the phone!
