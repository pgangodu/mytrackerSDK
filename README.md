# myTracker Flutter SDK

![Platforms][platforms-svg]
[![License][license-svg]][license-link]

* [General info](#general-info)
* [Initialization](#initialization)
     * [Available-to-configure options](#available-to-configure-options)
         * [MyTrackerConfig](#mytrackerconfig)
         * [MyTrackerParams](#mytrackerparams)
     * [Turn on/off debug mode](#turn on/off debug mode)
* [Events](#events)

## General information

myTracker is a multi-platform analytics and attribution system based on Mail.ru Group technologies.

### Minimum requirements

####Android
* Android api level 14 (Android 4.0)
* Permission `android.permission.INTERNET`
* Permission `android.permission.ACCESS_NETWORK_STATE`

Required dependencies that will be added automatically:
* [Google Play Services](https://developers.google.com/android/guides/setup) (module com.google.android.gms:play-services-ads-identifier)
* [Google Play Install Referrer](https://developer.android.com/google/play/installreferrer) (module com.android.installreferrer:installreferrer)

#### iOS
* Minimum supported iOS version is 9.0
* xCode version 12.4 or higher

In case it is necessary to obtain information about IDFA / IDFV on iOS 14+, then permission from the user is required.
For a solution, it is possible to use [App Tracking Transparency Plugin](https://pub.dev/packages/app_tracking_transparency). Or implement the permission request yourself.

### Location data

If you need to collect location data, then adding permissions to access the location and requesting these permissions is also required.
To solve this problem, you can use the [Location Permission Plugin](https://pub.dev/packages/location_permissions). Or implement the permission request yourself.

### Apps for Huawei App Store
If you are building an application for [Huawei App Store](https://appstore.huawei.com/), then the library must be included in the project
[Huawei Media Services](https://developer.huawei.com/consumer/en/service/hms/catalog/pps_document.html?page=hmssdk_huaweipps_devprepare_download_SDK)
(module com.huawei.hms:hms-ads-identifier), so that myTracker can get
[OAID](https://developer.huawei.com/consumer/en/service/hms/catalog/pps_document.html?page=hmssdk_huaweipps_introduction).

## Connection

Download the SDK, add it to your project in some `PATH_TO_SDK` path, and specify it as a dependency in your application's `pubspec.yaml`:
```yaml
dependencies:
   ...
   mytracker_sdk:
     path: PATH_TO_SDK
   ...
```

## Initialization

When initializing the tracker, you must specify your appId.
If necessary, before initialization, you can perform additional configuration of the tracker and parameters.
Application activity (launches, sessions) is tracked automatically.

```dart
// If necessary, set the parameters
MyTrackerParams trackerParams = await MyTracker.getTrackerParams();
MyTrackerParams trackerConfig = await MyTracker.getTrackerConfig();

// ...
// Setting tracker parameters
// ...

// Initialize the instance
await MyTracker.initTracker(SDK_KEY);
```


### Parameters available for setting

Tracker configuration can be done through an instance of the `MyTrackerConfig` class, accessible through the `MyTracker.getTrackerConfig()` method.
Tracker parameters can be configured via an instance of the `MyTrackerParams` class, which is available via the `MyTracker.getTrackerParams()` method.

####MyTrackerConfig
An instance of this class is responsible for the tracker configuration and provides the following methods.

```dart
Future<MyTrackerConfig> setTrackingLaunchEnabled(boolean trackingLaunchEnabled)
```
Application launch tracking. The default is true.

```dart
Future<MyTrackerConfig> setLaunchTimeout(int seconds)
```
Interval in seconds during which a new launch will not be counted and the session will not be terminated when the application is minimized.
The default is 30 seconds. You can set the value in the range of 30-7200 seconds.

```dart
Future<MyTrackerConfig> setBufferingPeriod(int seconds)
```
Interval in seconds during which events will be accumulated on the device before being sent to the server.
The default is 900 seconds. You can set the value in the range of 1-86400 seconds.

```dart
Future<MyTrackerConfig> setForcingPeriod(int seconds)
```
Interval in seconds after installing or updating an application during which events will be immediately sent to the server.
The default is 0 seconds (immediate send disabled). You can set the value in the range 0-432000 seconds (5 days).

```dart
Future<MyTrackerConfig> setAutotrackingPurchaseEnabled(boolean autotrackingPurchaseEnabled)
```
Automatic tracking of in-app purchases.
The default is true.

```dart
Future<MyTrackerConfig> setTrackingLocationEnabled(boolean trackingLocationEnabled)
```
Location tracking.
The default is true.

```dart
Future<MyTrackerConfig> setRegion(MyTrackerRegion region)
```
Setting the region of the data receiving server.
Available values:
* MyTrackerRegion.EU - Europe.
* MyTrackerRegion.RU - RF.

```dart
Future<MyTrackerConfig> setProxyHost(@Nullable String proxyHost)
```
Setting the proxy host of the data receiving server.

#### MyTrackerParams
An instance of this class is intended for setting user parameters.

User parameters can be set at any time during the application's runtime.

```dart
future setUserInfo() async
{
     MyTrackerParams trackerParams = await MyTracker.getTrackerParams();
     
     // Set the floor
     await trackerParams.setAge(22);
     // Set the age
     await trackerParams.setGender(MyTrackerGender.FEMALE);
     // Set id
     trackerParams.setCustomUserIds(["user_id_0", "user_id_1"]);
     //Set email addresses
     trackerParams.setEmails(["address1@example.com", "address2@example.com"]);
     // Set phone numbers
     trackerParams.setPhones(["84953332211", "84953332212", "84953332213"]);
}
```

### Enable/disable debug mode
Enabling/disabling the debugging mode is done through the static methods of the MyTracker class.

```dart
Future setDebugMode(boolean debugMode)
```
Enable/disable debug mode.
The default is false.

## Event tracking
Events can be sent via static methods of the MyTracker class.
The following methods are available for tracking different types of events:

```dart
Future trackLoginEvent(String userId, Map<String, String>? eventParams)
```
Login event.
The required parameter userId specifies the user ID.
The optional eventParams parameter allows you to set arbitrary key-value parameters for the event.
The maximum length of the key and value is 255 characters.

```dart
Future trackRegistrationEvent(String userId, Map<String, String>? eventParams)
```
Registration event.
The required parameter userId specifies the user ID.
The optional eventParams parameter allows you to set arbitrary key-value parameters for the event.
The maximum length of the key and value is 255 characters.

```dart
Future trackEvent(String name, Map<String, String>? eventParams)
```
An arbitrary event with the given name.
The optional eventParams parameter allows you to set arbitrary key-value parameters for the event.
The maximum length of the key and value is 255 characters.
For example:
```dart
MyTracker.trackEvent("name", {"key_0": "value_0", "key_1": "value_1"});
```

```dart
Future flush()
```
Force sending of all events and resetting the send timer.

[license-svg]: https://img.shields.io/badge/license-LGPL-lightgrey.svg
[license-link]: https://github.com/myTrackerSDK/mytracker-flutter/blob/master/LICENSE
[platforms-svg]: https://img.shields.io/badge/platform-Flutter-lightgrey.svg
