# Tnkfactory DisplayAd SDK (for Android)

## Index

1. [Installation](#1)
   * [Test Flight](#1-1)
   * [Publisher ID Settings](#1-2)
   * [COPPA settings](#1-3)
2. [Interstitial Ads](#2)
   * [Create Interstitial Ad Object](#2-1)
   * [Show the Ad](#2-2)
     * [Immediate exposure after ad load](#2-2-1)
     * [Ad is exposed after a certain amount of time after loading](#2-2-2)
     * [Expose after activity change](#2-2-3)
   * [How to use the '2-Button' frame ad at the end of the app.](#2-3)
   * [Class AdManager for Interstitial Ad Management.](#2-4)
     * [Load the ad](#2-4-1)
     * [Show the ad](#2-4-2)
     * [AdManager allows for ad load and show at the same time.](#2-4-3)
3. [Banner Ads](#3)
   * [XML View Insertion](#3-1)
     * [Create Banner View](#3-1-1)
     * [Load](#3-1-2)
   * [Dynamic View Generation](#3-2)
     * [Create Parent Layout](#3-2-1)
     * [Load](#3-2-2)
4. [Feed Ads](#4)
   * [XML View Insertion](#4-1)
     * [Create Feed View](#4-1-1)
     * [Load](#4-1-2)
   * [Dynamic View Generation](#4-2)
     * [Create Parent Layout](#4-2-1)
     * [Load](#4-2-2)
5. [Native Ads](#5)
   * [Create Layout](#5-1)
   * [Create Native Object](#5-2)
   * [Show Native Ad](#5-3)
     * [Show immediately after loading](#5-3-1)
     * [Wating for adequate loading time](#5-3-2)
6. [Video Ads](#6)
   * [Confirm Earned Rewards](#6-1)
7. [Use AdListener](#7)

## <a name="1"></a>1. Installation
TNK SDK is deployed on Maven Central.
Add maven repository to build.gradle of the top level (project).


```gradle
repositories {
    mavenCentral()
}
```


```gradle
dependencies {
    implementation 'com.tnkfactory:pub:7.21.2'
}
```

AndroidManifest configurations (Permissions Settings)


```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Wifi permission is essential when applying Video Ad.

```xml
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
```

If you are using proguard, following classes included in TNK SDK must not be obfuscated

```proguard
 -keep class com.tnkfactory.** { *;}
```

### <a name="1-1"></a>Test Flight

Show the Test Ads.

```diff
- Caution 1 : In the test state, the advertisement list appears normally only when the equipment being tested is registered as a development equipment.
```
링크 : [Regist Test Equipment](https://github.com/tnkfactory/android-sdk/blob/master/reg_test_device.md)

```diff
- Caution 2 : It is recommended that you expose the front advertisement to the splash screen when starting the app.
```
Refer to the Google Policy [Link](https://support.google.com/googleplay/android-developer/answer/12253906?hl=en)

We recommend that you load the ad in the intro, show the ad, and then  go to the main screen.

[Refer to the sample code](https://github.com/tnkfactory/android-sdk/blob/master/sample_app/app/src/main/java/com/tnkfactory/pub/sample/IntroActivity.java)



> SDK import

```java
import com.tnkfactory.ad.*;
```

> Interstitial Ad

```java
InterstitialAdItem adItem = new InterstitialAdItem(this,"TEST_INTERSTITIAL_V", new AdListener() {
        @Override
        public void onLoad(AdItem adItem) {
            adItem.show();
        }
    });

adItem.load();
```
> Banner Ad

```xml
<com.tnkfactory.ad.BannerAdView
    android:id="@+id/banner_ad_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_alignParentBottom="true"
    android:background="#ffffffff"
    app:placement_id="TEST_BANNER_100"/>
```

```java
BannerAdView bannerAdView = findViewById(R.id.banner_ad_view);
bannerAdView.load();
```
>
c

Use the Placement ID below on Test Flight to launch a test ad without having to register an account or app.

- TEST_BANNER_100 : Banner Ad (640x100)
- TEST_BANNER_200 : Banner Ad (640x200)
- TEST_INTERSTITIAL_H : Interstitial Ad (1200x627)
- TEST_INTERSTITIAL_V : Interstitial Ad (640x1320)
- TEST_INTERSTITIAL_V_FINISH : Interstitial Ad (2-Button type)
- TEST_FEED : Feed Ad
- TEST_NATIVE : Native Ad
- TEST_REWARD_V : Rewarded Video Ad

### <a name="1-2"></a>Publisher ID Settings

In Test Flight, we could easily conduct the test without having to register an account separately. However, in order to receive the actual Ad, you must first register Inventory on the Tnk Publish Site and add the Publsiher ID to the AndroidManifest.xml file.
Please register the Publsiher ID by referring to the sample below.

```xml
<application
   ...
   >
      ...

        <meta-data android:name="tnk_pub_id" android:value="YOUR-INVENTORY-ID-HERE" />

      ...
</application>
```

Once you have registered your Publiher ID, no more ads will appear in the Test Flight code above. You must register Placement according to the advertising type on the Tnk Publish Site and use the name of the registered Placement to display the actual advertisement.

**(In order to proceed with the test using the PLACEMENT_ID of Test Flight, you must proceed without registering the Publisher ID.)**

### <a name="1-3"></a>COPPA Setting

COPPA is the [U.S. Children's Privacy Act and related laws.](https://www.ftc.gov/tips-advice/business-center/privacy-and-security/children's-privacy) Google requires the app to comply with relevant laws if it serves children under the age of 13. Please set the options below so that age-appropriate ads can be seen.
```java
AdConfiguration.setCOPPA(this, true); // ON - For services to children under the age of 13
AdConfiguration.setCOPPA(this, false); // OFF - Default
```



## <a name="2"></a>2. Interstitial Ad

### <a name="2-1"></a>Create a Interstitial Ad object

import tnk class
```java
import com.tnkfactory.ad.*;
```

Enter a Placement ID to create a Ad object.
```java
@Override
public void onCreate(Bundle savedInstanceState) {
  ...

    InterstitialAdItem interstitialAdItem = new InterstitialAdItem(this, "YOUR-PlACEMENT-ID");

  ...
}
```

### <a name="2-2"></a>Show the Interstitial Ad

#### <a name="2-2-1"></a>Show immediately after loading

Use AdListener to display ads as soon as they are loaded.

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
      ...

        // Create object
        InterstitialAdItem interstitialAdItem = new InterstitialAdItem(this,"YOUR-PlACEMENT-ID");
      	// Use AdListener to expose when ads are loaded
        interstitialAdItem.setListener(new AdListener() {
            @Override
            public void onLoad(AdItem adItem) {
                adItem.show();
            }
        });

      	// Load
        interstitialAdItem.load();

      ...
    }
}
```

####<a name="2-2-2"></a> Wating for adequate loading time.

If you want to load an ad and display it after a certain period of time, you can display it after verifying that the ad has been loaded successfully as shown below.

```java
// Create object
InterstitialAdItem interstitialAdItem = new InterstitialAdItem(this,"YOUR-PlACEMENT-ID");
// Load
interstitialAdItem.load();

...

// Verify that it is loaded after a certain amount of time before calling the show
// When load and show are called simultaneously, the Ad is not exposed in the Ad not loaded state.
if (interstitialAdItem.isLoaded()) {
    interstitialAdItem.show();
}
```

#### <a name="2-2-3"></a>Show after activity change.

```java
...

// InteractiveAdItem, If an activity other than the one you created changes, as shown below
// When you call show(), you need to replace the context to expose the advertisement on the current screen.
interstitialAdItem.show(this);
```

### <a name="2-3"></a> How to use the '2-Button' frame ad at the end of the app.

Use the '2-Button' frame to naturally insert a front-page pop-up ad when the app is closed.

First of all, you can set the frame of the placement to 2-Button frame on the TNK Publisher Site and implement the shutdown process such as onBackPressed as follows.

```java
public class TestActivity extends AppCompatActivity {

    // Declare the ad item variable to output the end popup
    CloseAdItem closeAdItem;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Item create
        closeAdItem = new CloseAdItem(this, "placement-id");
        // Load
        closeAdItem.load();

    }

    @Override
    public void onBackPressed() {
        //super.onBackPressed();
	// If an ad is called before it loads or if the ad fails to load, an ad-free exit dialog is printed.
        closeAdItem.show();
    }
}
```

If detailed event processing is required, the Listener can be registered and implemented as follows:

```java
    closeAdItem.setListener(new CloseAdItem.CloseAdListener() {

      // Typical Exit pop-up output if the Exit pop-up advertisement fails
// An onError is called if the ad is called before it loads or if the ad fails to load.
        @Override
        public void onError() {
            new AlertDialog.Builder(TestActivity.this)
                    .setMessage("Exit?")
                    .setNegativeButton("cancel", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {

                        }
                    })
                    .setPositiveButton("Exit", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
				finish();
                        }
                    })
                    .show();
        }
        // Process when the OK button is pressed in the exit pop-up
        @Override
        public void onConfirm() {
            finish();
        }

        // Process when the user presses the Cancel button in the Exit pop-up
        @Override
        public void onCancel() {
            super.onCancel();
        }

        // If a user clicks on an ad, it will be implemented when tracking events using Google Analytics.
        @Override
        public void onClickAd(AdItem adItem) {
            super.onClickAd(adItem);
        }

        // Called when an advertising pop-up is output.
        @Override
        public void onShow(AdItem adItem) {
            super.onShow(adItem);
        }

    });

```

### <a name="2-4"></a>Class AdManager for Interstitial ad Management.

To use a Interstitial ad in the Publisher SDK, you must create an Interactive AdItem, load the ad, and then run an ad exposure when the loading is complete. At this time, it is necessary to maintain the InteractiveAdItem object until the advertisement is exposed, but if the activity transition occurs, it may be difficult to deliver the object.

AdManager class is implemented to facilitate the retention of Interstitial AdItem objects in these situations and to allow Ad exposure to desired activities. AdManager is implemented in a single tone pattern that allows you to load ads in Activity 1 and then expose ads in Activity 2.

#### <a name="2-4-1"></a>Load Interstitial Ad

loadInterstitialAd() allows you to run a Interstitial Ad load.

```java
// Without AdListener
AdManager.getInstance().loadInterstitialAd(this, "YOUR-PlACEMENT-ID");

// When using AdListener
AdManager.getInstance().loadInterstitialAd(this, "YOUR-PlACEMENT-ID", new AdListener() {

    @Override
    public void onLoad(AdItem adItem) {
      ...
    }
});
```

#### <a name="2-4-2"></a>Show Interstitial Ad

```java
// Without AdListener
AdManager.getInstance().showInterstitialAd(this, "YOUR-PlACEMENT-ID");

// When using AdListener
// Used to replace the AdListener that was registered during loadInterstateAd()
AdManager.getInstance().showInterstitialAd(this, "YOUR-PlACEMENT-ID",new AdListener() {

    @Override
    public void onShow(AdItem adItem) {
      ...
    }
});
```

#### <a name="2-4-3"></a>AdManager allows for ad load and show at the same time.

When using InterstitialAdItem, after loading(), must be called with the Ad loaded to expose the
Ad, but AdManager can call InterstitialAdItem() as shown below and at the same time call showInterstitialAd(). When both load and exposure are invoked at the same time, the exposure is automatically invoked at the end of the Ad loading to show the Ad.

```
AdManager.getInstance().loadInterstitialAd(this, "YOUR-PlACEMENT-ID");
AdManager.getInstance().showInterstitialAd(this, "YOUR-PlACEMENT-ID");
```

## <a name="3"></a>3. Banner Ad

There are two ways to use banner ads: XML View Insertion and View Dynamic Creation.

### <a name="3-1"></a>XML View Insertion Method

#### <a name="3-1-1"></a>Create Banner View

Create a banner view within the layout XML as follows:

Enter the Placement ID.

```xml
<RelativeLayout
   ...
   >
      ...

        <com.tnkfactory.ad.BannerAdView
            android:id="@+id/banner_ad_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:placement_id="YOUR-PLACEMENT-ID" />

      ...
</RelativeLayout>
```

#### <a name="3-1-2"></a>Load Banner Ad

Please import SDK class.
```java
import com.tnkfactory.ad.*;
```

Load the ad into the banner view inserted in XML as follows:

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
    	...

        BannerAdView bannerAdView = findViewById(R.id.banner_ad_view);

    		// Load
        bannerAdView.load();

    	...
    }
}
```

### <a name="3-2"></a>Dynamic view generation

#### <a name="3-2-1"></a>Create Parent Layout.

Create a parent layout to place the banner view within the layout XML as shown below.

```xml
<RelativeLayout
   ...
   >
      ...

        <RelativeLayout
            android:id="@+id/banner_ad_layout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

      ...
</RelativeLayout>
```

#### <a name="3-2-2"></a>Load Banner Ad

Please import SDK class.
```java
import com.tnkfactory.ad.*;
```

Enter the Placement ID below to create a banner view and load the banner ad.

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
      ...

        RelativeLayout bannerAdLayout = findViewById(R.id.banner_ad_layout);

      	// Creating Banner View Object
        BannerAdView bannerAdView = new BannerAdView(this, "YOUR-PLACEMENT-ID");

      	// Insert Banner View into Parent Layout
        bannerAdLayout.addView(bannerAdView);

      	// Load Banner Ad
        bannerAdView.load();

      ...
    }
}
```

## <a name="4"></a>4. Feed Ad

There are two ways to use feed-type ad: xml and view dynamic generation.

### <a name="4-1"></a>XML View Insertion Method

#### <a name="4-1-1"></a>Create Feed View

Put the feed view in the layout xml as shown below.

Enter the Placement ID.

```xml
<RelativeLayout
   ...
   >
      ...

        <com.tnkfactory.ad.FeedAdView
            android:id="@+id/feed_ad_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:placement_id="YOUR-PLACEMENT-ID"/>

      ...
</RelativeLayout>
```

#### <a name="4-1-2"></a>Load Feed Ad

Please import SDK class.
```java
import com.tnkfactory.ad.*;
```

Load the ad into the feed view inserted in xml as follows:

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        FeedAdView feedAdView = findViewById(R.id.feed_ad_view);

        // Load Feed Ad
        feedAdView.load();
    }
}
```

### <a name="4-2"></a>Dynamic view generation

#### <a name="4-2-1"></a>Create Parent Layout

Create a parent layout that will populate the feed view within the layout xml as shown below.

```xml
<RelativeLayout
   ...
   >
      ...

        <RelativeLayout
            android:id="@+id/feed_ad_layout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

      ...
</RelativeLayout>
```

#### <a name="4-2-2"></a>Load Feed Ad

Please import SDK class.
```java
import com.tnkfactory.ad.*;
```

Create a feed view by entering the Placement ID as below and load the banner ad.

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
      ...

        RelativeLayout feedAdLayout = findViewById(R.id.feed_ad_layout);

        // Creating a Feed View Object
        FeedAdView feedAdView = new FeedAdView(this, "YOUR-PLACEMENT-ID");

        // Insert a feed view into the parent
        feedAdLayout.addView(feedAdView);

        // Load feed Ad
        feedAdView.load();

      ...
    }
}
```



## <a name="5"></a>5. Native Ad

### <a name="5-1"></a>Create Layout

Create a layout (native_ad_item.xml) to display native ads.

The layout below is an example and you can create the structure you want when you actually use it.

```xml
<RelativeLayout
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
	android:padding="6dp"
	android:background="#DDDDDD">

	<RelativeLayout
		android:id="@+id/native_ad_image_layout"
		android:layout_width="match_parent"
		android:layout_height="wrap_content">

		<FrameLayout
			android:id="@+id/native_ad_content"
			android:layout_width="match_parent"
			android:layout_height="wrap_content"/>

		<ImageView
			android:id="@+id/native_ad_watermark_container"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_alignParentRight="true"/>
	</RelativeLayout>

	<RelativeLayout
		android:layout_width="match_parent"
		android:layout_height="wrap_content"
		android:layout_marginTop="10dp"
		android:layout_below="@+id/native_ad_image_layout">

		<ImageView
			android:id="@+id/native_ad_icon"
			android:layout_width="72dp"
			android:layout_height="72dp"
			android:layout_alignParentTop="true"
			android:layout_alignParentLeft="true"
			android:padding="4dp"
			android:scaleType="fitXY"/>
		<TextView
			android:id="@+id/native_ad_title"
			android:layout_width="match_parent"
			android:layout_height="wrap_content"
			android:layout_toRightOf="@id/native_ad_icon"
			android:layout_alignParentTop="true"
			android:layout_marginTop="3dp"
			android:layout_marginLeft="8dp"
			android:gravity="center_vertical"
			android:textColor="#ff020202"
			android:textSize="17sp"/>
		<TextView
			android:id="@+id/native_ad_desc"
			android:layout_width="match_parent"
			android:layout_height="wrap_content"
			android:layout_toRightOf="@id/native_ad_icon"
			android:layout_below="@id/native_ad_title"
			android:layout_marginLeft="8dp"
			android:layout_marginTop="8dp"
			android:gravity="center_vertical"
			android:textColor="#ff179dce"
			android:textSize="13sp"/>
	</RelativeLayout>
</RelativeLayout>
```

### <a name="5-2"></a>Creating a Native Object

Please import SDK class.
```java
import com.tnkfactory.ad.*;
```

```java
@Override
public void onCreate(Bundle savedInstanceState) {
  ...

    NativeAdItem nativeAdItem = new NativeAdItem(this, "YOUR-PlACEMENT-ID");

  ...
}
```

### <a name="5-3"></a>Show Native Ad

#### <a name="5-3-1"></a>Show immediately after loading

Use AdListener to display ads immediately when native ads are loaded.

```java
public class MainActivity extends AppCompatActivity {
  ...

    @Override
    protected void onCreate(Bundle savedInstanceState) {
      ...

        NativeAdItem nativeAdItem = new NativeAdItem(this, "YOUR-PlACEMENT-ID");

        // Expose when native ads are loaded using AdListener
        nativeAdItem.setListener(new AdListener() {

            @Override
            public void onLoad(AdItem adItem) {
                // Show native ad
                showNativeAd((NativeAdItem) adItem);
            }
        });

        // Load native ad
        nativeAdItem.load();

      ...
    }

  ...

    // Show Native ad
    private void showNativeAd(NativeAdItem nativeAdItem) {

        if (nativeAdItem != null & nativeAdItem.isLoaded()) {

            // Initialize the container into which the native ad will be inserted
            ViewGroup adContainer = findViewById(R.id.native_ad_container);
            adContainer.removeAllViews();

            // Insert Native Item Layout into Container
            ViewGroup view = (ViewGroup) View.inflate(this, R.layout.native_ad_item, adContainer);

            // Creating a Native Binder Object
            // Required view ID to display main content in constructor
            NativeViewBinder binder = new NativeViewBinder(R.id.native_ad_content);

            // Native binder setting
            binder.iconId(R.id.native_ad_icon)
                    .titleId(R.id.native_ad_title)
                    .textId(R.id.native_ad_desc)
                    .watermarkIconId(R.id.native_ad_watermark_container)
                    .addClickView(R.id.native_ad_content);

            // Native Ad Exposure
            nativeAdItem.attach(view, binder);
        }
    }

  ...
}
```

#### <a name="5-3-2"></a>Wating for adequate loading time.

If you want to load an ad and display it after a certain period of time, you can display it after verifying that the ad has been loaded successfully as shown below.

```java
// Create Ad object
NativeAdItem nativeAdItem = new NativeAdItem(this,"YOUR-PlACEMENT-ID");
// Load native Ad
nativeAdItem.load();

...

// Verify that the Ad is loaded after a certain amount of time before calling the show
// When load and show are called simultaneously, the front Ad is not exposed in the Ad not loaded state.
if (nativeAdItem.isLoaded()) {

    // Initialize the container into which the native ad will be inserted
    ViewGroup adContainer = findViewById(R.id.native_ad_container);
    adContainer.removeAllViews();

    // Insert Native Item Layout into Container
 		ViewGroup view = (ViewGroup) View.inflate(this, R.layout.native_ad_item, adContainer);

    // Creating a Native Binder Object
    // Required view ID to display main content in constructor
    NativeViewBinder binder = new NativeViewBinder(R.id.native_ad_content);

    // Native binder setting
    binder.iconId(R.id.native_ad_icon)
            .titleId(R.id.native_ad_title)
            .textId(R.id.native_ad_desc)
            .watermarkIconId(R.id.native_ad_watermark_container)
            .addClickView(R.id.native_ad_content);

    // Native Ad Exposure
    nativeAdItem.attach(view, binder);
}
```

## <a name="6"></a>6. Video Ad

Video Ad is the same as Interstitial Ad, so when you create a Placement ID, you can set up video ad in TNK Publisher Site and use the [전면 광고 가이드](#2-전면-광고-interstitial-ad).

### <a name="6-1"></a> Check whether reward video ads are accumulated.

For Rewarded video ad, you can check your accumulation using AdListener after the playback is complete.

```java
interstitialAdItem.setListener(new AdListener() {
  ...

    /**
     * Called when the video Ad has finished playing.
     * @param adItem 광고 아이템
     * @param verifyCode 적립 여부
     */
    @Override
    public void onVideoCompletion(AdItem adItem, int verifyCode) {
        super.onVideoCompletion(adItem, verifyCode);

        if (verifyCode >= VIDEO_VERIFY_SUCCESS_SELF) {
            // Accrual success
        } else {
            // fail
        }
    }

  ...
});
```



## <a name="7"></a>7. Use AdListener

All ads, Interstitial, Banner, Feed type, and Native, can register and use AdListener through setListener().

You only need to override the required methods.

```java
public abstract class AdListener {

    public static int CLOSE_SIMPLE = 0; // Don't click. Just close.
    public static int CLOSE_AUTO = 1; // Close after the auto-close time has passed
    public static int CLOSE_EXIT = 2; // Close with exit button (2-button)

    // video completion verification code
    public static int VIDEO_VERIFY_SUCCESS_S2S = 1; // validated by media server
    public static int VIDEO_VERIFY_SUCCESS_SELF = 0; // media server URL is not set
    Public static int VIDEO_VERIFY_FAILED_S2S = -1; // Unable to pay through media server
    public static int VIDEO_VERIFY_FAILED_TIMEOUT = -2; // Timeout when calling media server
    public static int VIDEO_VERIFY_FAILED_NO_DATA = -3; // No ad transmission and exposure history data
    public static int VIDEO_VERIFY_FAILED_TEST_VIDEO = -4; // This is a test video ad
    public static int VIDEO_VERIFY_FAILED_ERROR = -9; // other system errors

    /**
     * Called when the Ad screen is closed
     * @param adItem AdItem Object to Event Target
     * @param type 0:simple close, 1: auto close, 2:exit
     */
    public void onClose(AdItem adItem, int type) {

    }

    /**
     * Called when you click on an ad
     * Ad screen does not close
     * @param adItem AdItem Object to Event Target
     */
    public void onClick(AdItem adItem) {

    }

    /**
     * Called when the Ad screen appears.
     * @param adItem
     */
    public void onShow(AdItem adItem) {

    }

    /**
     * Called when an error occurs while processing the Ad
     * @param adItem
     * @param error AdError
     */
    public void onError(AdItem adItem, AdError error) {

    }

    /**
     * Called when the ad arrives after the ad load()
     * @param adItem
     */
    public void onLoad(AdItem adItem) {

    }

    /**
     * Called When the video Ad ende
     * @param adItem
     * @param verifyCode Callback results for video viewing completion.
     */
    public void onVideoCompletion(AdItem adItem, int verifyCode) {

    }
}
```
