# Narratiive SDK integration example

This is an example project for [Narratiive Android SDK](https://github.com/covalercom/narratiive-analytics-androidsdk) integration.

This example shows how to add Narratiive SDK to your Android app to measure traffic to named screens.


# Requirements

- [Android Studio](https://developer.android.com/sdk/index.html)


# Set up your project

1. Update your project's *AndroidManifest.xml* file to include the `INTERNET` and `ACCESS_NETWORK_STATE` permissions ( [728b469](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/728b469f9628844e3f514fc13c118a837ee8b9bc) ):

	```
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	          package="com.example.analytics">
	
	  <uses-permission android:name="android.permission.INTERNET"/>
	  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	
	  <application android:name="AnalyticsApplication">
	    ...
	  </application>
	</manifest>
	
	```

1. Narratiive SDK can optionally use [Advertising ID](https://support.google.com/googleplay/android-developer/answer/6048248?hl=en) to identify devices. This requires including the `APPLICATION_ID` in your project's *AndroidManifest.xml* file ( [ed0fc68e](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/ed0fc68e8523ccce5f994518e5702928c4960032) ). 


	```
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	          package="com.example.analytics">
	  ...		
	  <application android:name="AnalyticsApplication">
	    ...
	    
	    <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="ca-app-pub-0000000000000000~0000000000"/>            
	  </application>
	</manifest>
	
	```
	
	You can [find your application ID](https://support.google.com/admob/answer/7356431?hl=en) in your Admod account. If you don't use [Advertising ID](https://support.google.com/googleplay/android-developer/answer/6048248?hl=en) in your project, you can instead use a blank ID of `ca-app-pub-0000000000000000~0000000000`.


1. Add the JitPack repository to your preject level `build.gradle` file. Add it in your root build.gradle at the end of repositories ( [461c268](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/461c2688e580d2876ec738e6e23d2a6f722f83c5) ):

	```
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
	```
	
1. Add the following dependency to `app/build.gradle` ( [37b415d](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/37b415d5158f4e8713b5e9d1adc17baa7900fc23) ):

	```
	dependencies {
	   ...
	   implementation 'com.github.covalercom:narratiive-analytics-androidsdk:0.2.1'
	}
	```
	
	Replace `0.2.1` above with the latest SDK version number. You can check the [SDK homepage](https://github.com/covalercom/narratiive-analytics-androidsdk/releases) for the latest production versions.
	
	
# Create narratiive.xml	

Create the file `app/src/res/values/narratiive.xml` with the following content ( [4ac1504](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/4ac1504aad199912a8a992bc7c4812fa6d6e0065) ):

```
<resources>
    <string name="narratiive_host" translatable="false">${YOUR_HOST}</string>
    <string name="narratiive_hostkey" translatable="false">${YOUR_HOST_KEY}</string>
    <string name="narratiive_use_aaid" translatable="false">1</string>
</resources>
```

Replace `${YOUR_HOST}` and `@{YOUR_HOST_KEY}` with the `host` and `hostKey` values provided by the Narratiive support team.

If `narratiive_use_aaid` is given a value of "1", the Android Advertising ID will be used as the device identifier when measuring screen traffics. If Android Advertising ID is disabled on the device, or `narratiive_use_aaid` is given a value rather than "1", screen traffics will be measured with Narratiive device identifiers.

# Add screen tracking

1. Initialize the Narratiive SDK.


	Before measuring any screens, have your app initialize the Narratiive SDK by calling `NarratiiveSDK.init()` which initializes the SDK. This needs to be done only once, ideally at app launch.
	Here's an example of how to call the init() method in the Main Activity ( [221dc0a](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/221dc0a3771657504853460c47c146aec4f086e1) ):
	
	```
	class MainActivity : AppCompatActivity() {

	    override fun onCreate(savedInstanceState: Bundle?) {
	        super.onCreate(savedInstanceState)
	        ...
	        NarratiiveSDK.init(this)
	    }
	}
	```
	
	For debugging purpose, you can pass on a second parameter to the function ( e.g. `NarratiiveSDK.init(this, true)`). NarratiiveSDK will output debugging info to Logcat. For example:
	
	```
	 D/NarratiiveSDK: Init completed
	 D/NarratiiveSDK: Send started
	 D/NarratiiveSDK: Creating hit with path: /home
	 D/NarratiiveSDK: Send completed
	 D/NarratiiveSDK: Saving token: v1:RNSqGav+7GTdsFTDawCl9iNY8YLOyIn/T6h462cg7O84SS...
	 D/NarratiiveSDK: Hit created with new token: v1:RNSqGav+7GTdsFTDawCl9iNY8YLOyI...
	```	

2. Send screen events.

	To send screen events, call `NarratiiveSDK.send(screenName)`. Override the appropriate method, such as onResume for an Activity or onPageSelected for a ViewPager to send events when the screen changes ( [030be9b](https://github.com/covalercom/narratiive-analytics-androidsdk-demo/commit/030be9bd858ae0a6f19855db05f5358252c9d0e6) ).

	```
	...
    override fun onResume() {
        super.onResume()
        NarratiiveSDK.send("/dashboard")
    }
    
    ```
	
	The `screenName` parameter is used to identify the screen. It is analogous to the path of a URL and shares the same format rules as URL paths. The leading slash ‘/’ is optional. e.g. `/news/international`
