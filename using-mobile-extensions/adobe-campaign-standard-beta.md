# Adobe Campaign Standard \(Beta\)

{% hint style="info" %}
**Before** you install or configure the Adobe Campaign Standard beta extension, we recommend that you read the [_Getting Started_](../getting-started/create-a-mobile-property.md) section and in [Configuring a mobile application using Adobe Experience Platform SDKs](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html). 
{% endhint %}

{% hint style="warning" %}
This extension is considered beta functionality and is available only in Launch's [Integration](http://launch-integration.adobe.com) environment. For more information, see [Configuring a mobile application using Adobe Experience Platform SDKs](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html) or contact your beta program manager.
{% endhint %}

## Configure Campaign Standard Extension in Launch

1. In Launch's Integration environment, click the **Extensions** tab.
2. On the **Catalog** tab, locate the **Adobe Campaign Standard** extension and click **Install**.
3. Provide extension settings \(see [Configure Campaign Standard Extension](adobe-campaign-standard-beta.md#configure-campaign-standard-extension)\).
4. Click **Save**.
5. Follow the publishing process to update SDK configuration

### Configure Campaign Standard Extension

![](../.gitbook/assets/campaign-extension-config-v2.png)

#### ACS Endpoints

{% hint style="info" %}
Trying to find your ACS endpoint URLs? Contact your beta manager.
{% endhint %}

Provide endpoint URL\(s\) for your Adobe Campaign Standard instances. You may specify up to three unique endpoints for your development, staging, and production environments. 

{% hint style="warning" %} For this extension, these ACS endpoint URLs should be typed in without the http:// or https://.and cannot end with a forward slash. {% endhint %}

#### pKey

A unique, auto-generated identifier for a mobile app configured in Adobe Campaign Standard. After you've configured this extension in Launch, you will need to configure your Launch mobile property in Adobe Campaign Standard. When the configuration in Campaign is successful, pKeys will be automatically generated, per Campaign Standard instance, and configured in Launch Campaign extension for successful validation.

{% hint style="info" %}
Need help connecting your Launch mobile property with your Campaign Standard mobile app? For more information, see [Configuring a mobile application using Adobe Experience Platform SDKs](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html).
{% endhint %}

#### MCIAS Endpoint

{% hint style="info" %}
Trying to find your MCIAS endpoint URL? Contact your beta manager.
{% endhint %}

Provide your in-app messaging service URL endpoint. The SDK retrieves all in-app messaging rules and definition payloads from this endpoint.

{% hint style="warning" %}
{% hint style="warning" %} For this extension, the MCIAS endpoint URL should be typed in without the http:// or https:// and cannot end with a forward slash. {% endhint %}
{% endhint %}

#### Request Timeout

Time in seconds to wait for a response from the in-app messaging service, before timing out. The SDK default timeout value is 5 seconds.

{% hint style="warning" %} Request Timeout must be a non-zero number. {% endhint %}

## Add Campaign Standard to your app

{% hint style="warning" %}
This beta extension is currently available only for iOS development.
{% endhint %}

{% tabs %}
{% tab title="iOS" %}
{% hint style="warning" %}
This **beta** Campaign Standard extension requires [Mobile Core](mobile-core/) and [Profile](profile/) **beta** extensions. If you are using other versions of Mobile Core and Profile libraries, use the beta version instead, as the instructions below indicate.
{% endhint %}

![](../.gitbook/assets/acs-beta-pods.png)

Add the Campaign Standard, [Mobile Core](mobile-core/), and [Profile](profile/) beta libraries to your project. You'll need to add the following pods to your `Podfile`:

```text
pod 'ACPCoreBeta', '1.0.2beta'
pod 'ACPCampaignBeta', '1.0.2beta'
pod 'ACPUserProfileBeta', '1.0.1beta'
```

or you can manually include the [Mobile Core](https://github.com/Adobe-Marketing-Cloud/acp-sdks/releases/tag/v1.0.2beta-ACPCore), [Campaign Standard](https://github.com/Adobe-Marketing-Cloud/acp-sdks/releases/tag/v1.0.2beta-ACPCampaign), and [Profile](https://github.com/Adobe-Marketing-Cloud/acp-sdks/releases/tag/v1.0.1beta-ACPUserProfile) beta extensions found in Github.

In Xcode, import the Mobile Core beta, Campaign Standard beta, and Profile extensions:

#### Objective-C

```objectivec
#import <ACPCore_iOS/ACPCore_iOS.h>
#import <ACPCampaign_iOS/ACPCampaign_iOS.h>
#import <ACPIdentity_iOS/ACPIdentity_iOS.h>
#import <ACPSignal_iOS/ACPSignal_iOS.h>
#import <ACPLifecycle_iOS/ACPLifecycle_iOS.h>
#import <ACPUserProfile_iOS/ACPUserProfile.h>
```

#### Swift

```swift
import ACPCore_iOS
import ACPCampaign_iOS
import ACPIdentity_iOS
import ACPLifecycle_iOS
import ACPSignal_iOS
import ACPUserProfile_iOS
```
{% endtab %}
{% endtabs %}

### Register Campaign Standard with Mobile Core

{% tabs %}
{% tab title="iOS" %}
In your app's`application:didFinishLaunchingWithOptions:` method, register the Campaign Standard extension:

#### Objective-C <a id="objective-c-1"></a>

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [ACPCampaign registerExtension];
    [ACPIdentity registerExtension];
    [ACPLifecycle registerExtension];
    [ACPSignal registerExtension];
    [ACPUserProfile registerExtension];
  // Override point for customization after application launch.
  return YES;
}
```

#### Swift

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
   ACPCampaign.registerExtension();
   ACPIdentity.registerExtension();
   ACPLifecycle.registerExtension();
   ACPSignal.registerExtension();   
   ACPUserProfile.registerExtension();
  // Override point for customization after application launch.
  return true;
}
```
{% endtab %}
{% endtabs %}

### Set up push messaging

{% hint style="warning" %}
iOS simulators do not support push messaging.
{% endhint %}

{% hint style="info" %}
Need help creating a push notification using Adobe Campaign? For more information, see [Preparing and sending a push notification](https://helpx.adobe.com/campaign/standard/channels/using/preparing-and-sending-a-push-notification.html). 
{% endhint %}

After you follow [Apple's instructions](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1) to get your app ready to handle push notifications, you will need to set the push token using the [`setPushIdentifier`](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/mobile-core/identity/identity-api-reference#set-the-push-identifier) API:

{% tabs %}
{% tab title="iOS" %}
### setPushIdentifier

#### Objective-C

#### Syntax

```objectivec
+ (void) setPushIdentifier: (nullable NSData*) deviceToken;
```

#### Example

```objectivec
- (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  // Set the deviceToken that the APNS has assigned to the device
  [ACPCore setPushIdentifier:deviceToken];
  //...
}
```

#### Swift

```swift
ACPCore.setPushIdentifier(deviceToken)
```
{% endtab %}
{% endtabs %}

If everyting is configured correctly, after installing your app on mobile device, your should see the following debug logs:

a) Campaign rules have been downloaded

#### Example

```text
2019-01-31 18:22:35.980872-0800 CampaignDemoApp[935:156012] [AMSDK DEBUG <com.adobe.module.campaign>]: Successfully downloaded Rules from 'https://mcias-va7.cloud.adobe.io/mcias/mcias.campaign-demo.adobe.com/PR8fdd35ee6cc84aa8bbdea8f92db3f55a/43583282444503123217621782542046274680/rules.zip
```

b) request to demdex has been sent

#### Example

```text
2019-01-31 18:22:35.261676-0800 CampaignDemoApp[935:156015] [AMSDK DEBUG <com.adobe.module.identity>]: Sending request (https://dpm.demdex.net/id?d_rtbd=json&d_ver=2&d_orgid=B1F855165B4C9EA50A495E06@AdobeOrg&d_mid=43583282444503123217621782542046274680&d_blob=j8Odv6LonN4r3an7LhD3WZrU1bUpAkFkkiY1ncBR96t2PTI&dcs_region=9)
```
c) push token

#### Example

```text
2019-01-31 18:22:34.881855-0800 CampaignDemoApp[935:155847] Push Token: c201fc7cc33243800802850ae65856f64f0cebc439c891eee8939682075afe75
```



### Tracking for push and in-app messaging

To set up tracking postbacks for push and in-app messaging and create rules for in-app messaging tracking postbacks and push notifications tracking postbacks, contact your beta program manager. 

For more information, go to [Configuring your application in Launch](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html#ConfiguringyourapplicationinLaunch) and search for _Create rules for push notifications tracking postback_ and _Create rules for In-App tracking postback_ .



## Deleting mobile properties in Launch

{% hint style="danger" %}
Deleting your property in Launch may cause disruption to any recurring push and in-app messaging activities.
{% endhint %}

If you [delete](https://docs.adobelaunch.com/administration/companies-and-properties#delete-a-property) your mobile property in Launch, you might review your mobile property status in Campaign Standard and ensure that the property displays an updated status of **Deleted in Launch**. You may choose to remove the corresponding mobile app in Campaign Standard by clicking the **Remove from ACS** button. For more information, see [Configuring a mobile application using Adobe Experience Platform SDKs](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html).

{% hint style="warning" %}
Deleting your mobile property in Launch does not automatically delete your Campaign Standard mobile app.
{% endhint %}

