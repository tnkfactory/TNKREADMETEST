# Tnk DisplayAd SDK (for iOS)

## Table Of Contents

1. [SDK Get Started](#1)
   * [Test Flight](#1-1)
   * [Publisher ID Settings](#1-2)
   * [COPPA settings](#1-3)
2. [Interstitial Ad](#2)
   * [Create Interstitial Ad Object](#2-1)
   * [Show the Ad](#2-2)
     * [Show immediately after loading](#2-2-1)
     * [Wating for adequate loading time](#2-2-2)
3. [Banner Ad](#3)
4. [Native Ad](#4)
   * [Create Layout](#4-1)
   * [Create Native Object](#4-2)
   * [Show Native Ad](#4-3)
5. [Video Ad](#5)
6. [Use AdListener](#6)

## <a name="1"></a>1. SDK Get Started

### Download SDK

**[[iOS Pub SDK Download v1.07](https://github.com/tnkfactory/ios-pub-sdk/blob/main/sdk/TnkPubSdk.v1.07.zip)]**

### Framework registration

If you unpack the downloaded SDK compressed file, it is TnkPubSDK.xcframework folder is created.
Move that folder to the XCode project folder you want to apply.

Verify that theTnkPubSDK.xcframework exists in XCode -> Target -> General -> Framework, Libraries, and Embedded Content, and change the Embedded setting to Embedded & Sign.

Please refer to the image below.
![drag_framework](https://github.com/tnkfactory/ios-pub-sdk/raw/main/img/drag_framework.png)
![framework_embed](https://github.com/tnkfactory/ios-pub-sdk/raw/main/img/framework_embed.png)


### <a name="1-1"></a>Test Flight

Simply use the code below to launch a test Ad.

> Interstitial Ad
#### Swift
```swift
// ViewController.swift

import UIKit
import TnkPubSdk

class ViewController: UIViewController, TnkAdListener {

    @IBOutlet var adbutton:UIButton!

    @IBAction func didButtonClicked() {
        //TnkUtils.showATTPopup(viewController: self)

        let adItem = TnkInterstitialAdItem(viewController: self,
                                              placementId: "TEST_INTERSTITIAL_V")
        adItem.setListener(self)
        adItem.load()
    }

    func onLoad(adItem:TnkAdItem) {
        adItem.show()
    }
```
#### Objectvie-C
```objective-C
// ViewController.h

#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

@interface ViewController : UIViewController <TnkAdListener>

@property (nonatomic, weak) IBOutlet UIButton *adButton;

- (IBAction)adButtonPressed:(id)sender;
@end

// ViewController.m

- (IBAction)adButtonPressed:(id)sender {
    TnkInterstitialAdItem* adItem = [[TnkInterstitialAdItem alloc]
                                        initWithViewController:self
                                        placementId:@"TEST_INTERSTITIAL_V"];
    [adItem setListener:self];

    [adItem load];
}

- (void)onLoad:(id<TnkAdItem>)adItem {
    [adItem show];
}
```

> Use the Placement ID below on Test Flight to launch a test ad without having to register an account or app.

- TEST_BANNER_100 : Banner Ad (640x100)
- TEST_BANNER_200 : Banner Ad (640x200)
- TEST_INTERSTITIAL_H : Interstitial Ad (1200x627)
- TEST_INTERSTITIAL_V : Interstitial Ad (640x1320)
- TEST_INTERSTITIAL_V_FINISH : Interstitial Ad (2-Button type)
- TEST_FEED : Feed Ad
- TEST_NATIVE : Native Ad
- TEST_REWARD_V : Rewarded Video Ad


### <a name="1-2"></a>Publisher ID Settings

In Test Flight, we could easily conduct the test without having to register an account separately. However, in order to receive the actual Ad, you must first register Inventory on the Tnk Publish Site and add the Publsiher ID to the info.plist file.
Please register the tnk_pub_id by referring to the sample below.

![tnk_pub_id](https://github.com/tnkfactory/ios-pub-sdk/raw/main/img/tnk_pub_id.png)

Once you have registered your Publiher ID, no more ads will appear in the Test Flight code above. You must register Placement according to the advertising type on the Tnk Publish Site and use the name of the registered Placement to display the actual advertisement.

**(In order to proceed with the test using the PLACEMENT_ID of Test Flight, you must proceed without registering the Publisher ID.)**

### <a name="1-3"></a>COPPA Setting

COPPA is the [U.S. Children's Privacy Act and related laws.](https://www.ftc.gov/tips-advice/business-center/privacy-and-security/children's-privacy) Apple requires the app to comply with relevant laws if it serves children under the age of 13. Please set the options below so that age-appropriate ads can be seen.

#### Swift
```Swift
    TnkAdConfiguration.setCOPPA(true)  // ON - For services to children under the age of 13
    TnkAdConfiguration.setCOPPA(false) // Default
```
#### Objective-C
```Objective-C
    [TnkAdConfiguration setCOPPA:YES]; // ON - For services to children under the age of 13
    [TnkAdConfiguration setCOPPA:NO];  // Default
```



## <a name="2"></a>2. Interstitial Ad

### <a name="2-1"></a>Create a Interstitial Ad object

#### Swift

Import the framework and create interstitial Ad object by entering the Placement ID as shown below.

```Swift
import UIKit
import TnkPubSdk
...

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let adItem = TnkInterstitialAdItem(viewController: self,
                                              placementId: "YOUR-PLACEMENT-ID")
    }
}
```
#### Objective-C

Import the framework header file (TnkPubSdk.h) and create interstitial Ad object by entering the Placement ID as shown below.

```java
#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

...

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.

    TnkInterstitialAdItem* adItem = [[TnkInterstitialAdItem alloc]
                                        initWithViewController:self
                                        placementId:@"YOUR-PLACEMENT-ID"];

}
```

### <a name="2-2"></a>Show the Interstitial Ad

#### <a name="2-2-1"></a>Show immediately after loading

Use AdListener to display ads as soon as they are loaded.

```Swift
import UIKit
import TnkPubSdk

class ViewController: UIViewController, TnkAdListener {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let adItem = TnkInterstitialAdItem(viewController: self,
                                              placementId: "YOUR-PLACEMENT-ID")

        adItem.setListener(self)
        adItem.load()
    }

    func onLoad(_ adItem:TnkAdItem) {
        adItem.show()
    }


}
```

```Objective-C
// ViewController.h
#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

@interface ViewController : UIViewController <TnkAdListener>
@end

// ViewController.m
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.

    TnkInterstitialAdItem* adItem = [[TnkInterstitialAdItem alloc]
                                        initWithViewController:self
                                        placementId:@"YOUR-PLACEMENT-ID"];
    [adItem setListener:self];
    [adItem load];
}

- (void)onLoad:(id<TnkAdItem>)adItem {
    [adItem show];
}

@end
```
#### <a name="2-2-2"></a>Wating for adequate loading time.

If you want to load an ad and display it after a certain period of time, you can display it after verifying that the ad has been loaded successfully as shown below.

```Swift
// Create Object
let adItem = TnkInterstitialAdItem(viewController: self, placementId: "YOUR-PLACEMENT-ID")
// Load
adItem.load();

...

// Verify that it is loaded after a certain amount of time before calling the show
// When load and show are called simultaneously, the Ad is not exposed in the Ad not loaded state.

if (adItem.isLoaded()) {
    adItem.show();
}
```

## <a name="3"></a>3. Banner Ad

#### Create Container View

To use banner ads, you must first create a view(Container View) to show banner ads and then set it to TnkBannerView. Create a UIView to display the TnkBannerView in the StoryBoard or Xib file.

![banner_layout](https://github.com/tnkfactory/ios-pub-sdk/raw/main/img/banner_layout.png)

#### Create Banner View and Load Ad

Set the TnkBannerAdView object for displaying banner ads, specify the frame size, and specify the ContainerView you created earlier.
Then call the load() method and show() at the time of onLoad() via AdListener to display banner ads. Banner ads automatically reload every 30 seconds by default.

```Swift
import UIKit
import TnkPubSdk

class ViewController: UIViewController, TnkAdListener {
    @IBOutlet var bannerContainerView:UIView!

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let bannerView = TnkBannerAdView(placementId: "mypage_banner", adListener: self)
        bannerView.frame = CGRect(x: 0, y: 0, width: 320, height: 50)
        bannerView.setContainerView(bannerContainerView)
        bannerView.load()
    }

    func onLoad(_ adItem:TnkAdItem) {
        adItem.show()
    }
}
```

```Objective-C

// ViewController.h
#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

@interface ViewController : UIViewController <TnkAdListener>

@property (nonatomic, weak) IBOutlet UIView *bannerContainerView;

@end

// ViewController.m
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.

    TnkBannerAdView* adView = [[TnkBannerAdView alloc] initWithPlacementId:@"mypage_banner"
                                                                adListener:self];
    adView.frame = CGRectMake(0,0,320,50);
    [adView setContainerView:self.bannerContainerView];

    [adView load];
}

- (void)onLoad:(id<TnkAdItem>)adItem {
    [adItem show];
}

@end
```
## <a name="4"></a>4. Native Ad

#### <a name="4-1"></a>Create Layout

Use the StoryBoard or Xib file to configure the View to display native ads, as shown in the image example below. Native Ads provide main images, icon images, advertisement titles, and Ad descriptions.

![native_layout](https://github.com/tnkfactory/ios-pub-sdk/raw/main/img/native_layout.png)

#### <a name="4-2"></a>Create Native Object and Ad load

Create a TnkNativeAdItem object to get native ads and invoke the load() method. Subsequently, the Ad data required at the time of onLoad() is imported and displayed on the screen.

#### <a name="4-3"></a>Show Native Ad

Native ads are displayed directly on the screen by the developer using advertising assets and text provided by the TnkNativeAdItem object. Please refer to the sample below.

```Swift
import UIKit
import TnkPubSdk

class ViewController: UIViewController, TnkAdListener {
    @IBOutlet var bannerContainerView:UIView!

    @IBOutlet var nativeContainerView:UIView!
    @IBOutlet var nativeImageView:UIImageView!
    @IBOutlet var nativeIconView:UIImageView!
    @IBOutlet var nativeTitleLabel:UILabel!
    @IBOutlet var nativeDescLabel:UILabel!

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let adItem = TnkNativeAdItem(placementId:"event_a_native", adListener: self)
        adItem.load()

    }

    func onLoad(_ adItem:TnkAdItem) {
        if let nativeAdItem = adItem as? TnkNativeAdItem {
            nativeImageView.image = nativeAdItem.getMainImage()
            nativeIconView.image = nativeAdItem.getIconImage()
            nativeTitleLabel.text = nativeAdItem.getTitle()
            nativeDescLabel.text = nativeAdItem.getDescription()

            nativeAdItem.attach(nativeContainerView, clickView:nativeImageView)
        }
    }
}
```

```Objective-C
// ViewController.h
#import <UIKit/UIKit.h>
#import <TnkPubSdk/TnkPubSdk.h>

@interface ViewController : UIViewController <TnkAdListener>

@property (nonatomic, weak) IBOutlet UIView *nativeContainerView;
@property (nonatomic, weak) IBOutlet UIImageView *nativeImageView;
@property (nonatomic, weak) IBOutlet UIImageView *nativeIconView;
@property (nonatomic, weak) IBOutlet UILabel *nativeTitleLabel;
@property (nonatomic, weak) IBOutlet UILabel *nativedescLabel;

@end

// ViewController.m
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.

    TnkNativeAdItem* nativeItem = [[TnkNativeAdItem alloc] initWithPlacementId:@"event_a_native" adListener:self];
    [nativeItem load];
}

- (void)onLoad:(id<TnkAdItem>)adItem {
    TnkNativeAdItem* nativeItem = (TnkNativeAdItem*)adItem;

    self.nativeImageView.image = [nativeItem getMainImage];
    self.nativeIconView.image = [nativeItem getIconImage];
    self.nativeTitleLabel.text = [nativeItem getTitle];
    self.nativedescLabel.text = [nativeItem getDescription];

    [nativeItem attach:self.nativeContainerView clickView:self.nativeImageView];
}

@end
```


## <a name="5"></a>5. Video Ad

To be supported.



## <a name="6"></a>6. Use AdListener

All ads, Interstitial, Banner, Feed type, and Native, can register and use AdListener through setListener().

You only need to override the required methods.

```Swift
@objc public enum AdClose : Int {
    case Simple = 0
    case Auto = 1
    case Exit = 2

    public func description() -> String {
        switch self {
        case .Simple:
            return "AdClose.Simple"
        case .Auto:
            return "AdClose.Auto"
        case .Exit:
            return "AdClose.Exit"
        default:
            return "AdClose.Unknown"
        }
    }
}

@objc public enum AdError : Int {
    case NoError = 0
    case NoAd = -1
    case NoImage = -2
    case Timeout = -3
    case Cancel = -4
    case ShowBeforeLoad = -5
    case NoAdFrame = -6 // not used yet in iOS
    case DupLoad = -7
    case DupShow = -8
    case NoPlacementId = -24
    case NoScreenOrientation = -25
    case NoTestDevice = -28
    case SystemFailure = -99

    public func description() -> String {
        switch self {
        case .NoError:
            return "AdError.NoError"
        case .NoAd:
            return "AdError.NoAd"
        case .NoImage:
            return "AdError.NoImage"
        case .Timeout:
            return "AdError.Timeout"
        case .Cancel:
            return "AdError.Cancel"
        case .ShowBeforeLoad:
            return "AdError.ShowBeforeLoad"
        case .NoAdFrame:
            return "AdError.NoAdFrame"
        case .DupLoad:
            return "AdError.DupLoad"
        case .DupShow:
            return "AdError.DupShow"
        case .NoPlacementId:
            return "AdError.NoPlacementId"
        case .NoScreenOrientation:
            return "AdError.NoScreenOrientation"
        case .NoTestDevice:
            return "AdError.NoTestDevice"
        case .SystemFailure:
            return "AdError.SystemFailure"
        default:
            return "AdError.Unknown"
        }
    }
}

@objc public enum AdVideo : Int {
    case VerifySuccessS2S = 1 // validated by media server
    case VerifySuccessSelf = 0 // media server URL is not set
    case VerifyFailedS2s = -1 // Unable to pay through media server
    case VerifyFailedTimeout = -2 // Timeout when calling media server
    case VerifyFailedNoData = -3 // No ad transmission and exposure history data
    case VerifyFailedTest = -4 // This is a test video ad
    case VerifyFailedError = -9 // other system errors

    public func description() -> String {
        switch self {
        case .VerifySuccessS2S:
            return "AdVideo.VerifySuccessS2S"
        case .VerifySuccessSelf:
            return "AdVideo.VerifySuccessSelf"
        case .VerifyFailedS2s:
            return "AdVideo.VerifyFailedS2s"
        case .VerifyFailedTimeout:
            return "AdVideo.VerifyFailedTimeout"
        case .VerifyFailedNoData:
            return "AdVideo.VerifyFailedNoData"
        case .VerifyFailedTest:
            return "AdVideo.VerifyFailedTest"
        case .VerifyFailedError:
            return "AdVideo.VerifyFailedError"
        default:
            return "AdVideo.UnknownError"
        }
    }
}

@objc public protocol TnkAdListener {

        /**
     * Called when the Ad screen is closed
     * @param adItem AdItem Object to Event Target
     * @param type 0:simple close, 1: auto close, 2:exit
     */
    @objc optional func onClose(_ adItem:TnkAdItem, type:AdClose)

    /**
     * Called when you click on an ad
     * Ad screen does not close
     * @param adItem AdItem Object to Event Target
     */
    @objc optional func onClick(_ adItem:TnkAdItem)

    /**
     * Called when the Ad screen appears.
     * @param adItem AdItem Object to Event Target
     */
    @objc optional func onShow(_ adItem:TnkAdItem)

    /**
     * Called when an error occurs while processing the Ad
     * @param adItem AdItem Object to Event Target
     * @param error AdError
     */
    @objc optional func onError(_ adItem:TnkAdItem, error:AdError)

    /**
     * Called when the ad arrives after the ad load()
     * @param adItem AdItem Object to Event Target
     */
    @objc optional func onLoad(_ adItem:TnkAdItem)

    /**
     * Called When the video Ad ende
     * @param adItem AdItem Object to Event Target
     * @param verifyCode Callback results for video viewing completion.
     */
    @objc optional func onVideoCompletion(_ adItem:TnkAdItem, verifyCode:Int)
}
```
