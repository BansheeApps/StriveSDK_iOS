![Alt Text](http://imgur.com/0LBJMTL.gif)

# StriveSDK: Augmented Reality Face Filters SDK for iOS

StriveSDK for iOS makes it dead simple to add live effects to your app’s camera experience. With one line of code, you’ll help users look instantly more photogenic, creative, and comfortable with the camera.


In this repo, you'll find:
- Strive.framework: use this to bring augmented reality face filters to your app.
- DEMO_APP: A demo project that showcases how to use this app.
- TUTORIAL_APP: A tutorial project you can use to follow along the integration instructions below.

## Get an SDK Key

You'll need one of these, get one here: [http://www.strivesdk.com](http://www.strivesdk.com). 

:rotating_light: :rotating_light: We'll only be offering *free SDK keys* for the next 24 hours :rotating_light: :rotating_light:


## Guide

Follow along with your own project, or use the Tutorial project we've setup (more info below).

## Installation

1. Clone this repo to download the framework file, alongside demo and tutorial Xcode projects:

```bash
git clone https://github.com/GetLightBite/StriveSDK_iOS.git
open StriveSDK_ios
```
- You'll find our framework at: `demo_app/Strive.framework`
- You can find the tutorial project at `tutorial_app/StriveCameraTutorial.xcodeproj`. Open it with Xcode, and let's get going!


2. Drag `Strive.framework` into your project. Make sure "Copy items if needed" is checked. It may take ~10 seconds for the framework to appear in the project directory.
![Alt Text](http://imgur.com/RvQLqJf.gif)


3. In  Project Settings, go to `General tab > Embedded Binaries`. Click the `+` icon and add in Strive.framework.  Do the same in `Linked Frameworks and Libraries` if it's not there automatically.
![Alt Text](http://i.imgur.com/90EojmL.gif)


4. Go to the `Build Settings tab`, search for `bitcode`, and set `Enable Bitcode` to `NO`.
![Alt Text](http://imgur.com/CNdcDIc.gif)


## Initialization

Locate your app delegate and initialize the framework in your *application:didFinishLaunchingWithOptions* method. 

You'll need an SDK Key, you can get one here: [https://www.strivesdk.com](https://www.strivesdk.com).

> In the tutorial app, this would be at the top of AppDelegate.m

**Objective-C**
```objective-c
@import Strive; // add this at line 11 in tutorial app

...

- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // ...other initialization code

    NSString *striveKey = @"INSERT_KEY_HERE"; // add these lines at line 29 in tutorial app
    [StriveInstance setupWithKey:striveKey];
    [[StriveInstance shared] prepare]; // optional, speeds up filter setup later

    return YES;
}
```

**Swift**
```swift
import Strive

...

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    // ...other initialization code

    let striveKey = "INSERT_KEY_HERE" // add these lines at line 29 in tutorial app
    StriveInsance.setupWithKEy(striveKey)
    StriveInstance.shared.prepare()

    return true
}
```

## Integration

Go to the controller where you configured your camera settings.

> In the tutorial app, this would be SessionHandler.m. I've included line numbers in the comments of the sample code below

:rotating_light: :rotating_light: These instructions should work if you're using AVFoundation or any library that's a light wrapper around it (like OpenTok's camera implementation). If not, shoot me a note at (seb x strivesdk x com) and I'll be happy to help. :rotating_light: :rotating_light:

a) Import Strive and create a StriveInstance property

**Objective-C**
```objective-c
@import Strive; // line 11 in tutorial app


@interface YourCameraClass ()

// other properties

@property (nonatomic) StriveInstance *strive; // add this at line 25 in tutorial app


@end

```

b) Initialize your class' StriveInstance property. This should be done as early in this object's lifecycle (ie. viewDidLoad for UIViewController subclasses, or in the init method for NSObject subclasses).

```objective-c

- (id)init
{
    self = [super init];
    if (self) {
        // ... initializing other things
        self.strive = [StriveInstance shared]; // // add this at line 43 in tutorial app
    }
    return self;
}


```

c) Configure the camera to use the 32BGRA pixel format, portraid orientation, and mirroring

```objective-c
- (void)yourMethodWhereYouConfigureCamera

    // ...

    AVCaptureVideoDataOutput *videoDataOutput = [[AVCaptureVideoDataOutput alloc] init]; // line 67 in tutorial app
    NSDictionary *outputSettings = @{ (id)kCVPixelBufferPixelFormatTypeKey : [NSNumber numberWithInteger:kCVPixelFormatType_32BGRA]}; // !!! make sure to use 32BGRA for the pixel format
    videoDataOutput.videoSettings = outputSettings; 

    if ([_captureSession canAddOutput:videoDataOutput]) {
        [_captureSession addOutput:videoDataOutput];
        AVCaptureConnection *cnx = [videoDataOutput connectionWithMediaType:AVMediaTypeVideo];
        cnx.videoOrientation = AVCaptureVideoOrientationPortrait; // !!! make sure to use Portrait mode
        cnx.videoMirrored = YES; // !!! mirroring on front camera is recommended
    }

    // ...
}

```

d) Start passing frames to Strive to see augmented reality in action! Specify which filter you'd like to see, and provide a callback function that accepts the newly processed frames.

```objective-c

- (void)captureOutput:(AVCaptureOutput *)captureOutput // line 110 in tutorial app
didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer
       fromConnection:(AVCaptureConnection *)connection
{
    STVFilter s = STVFilterButterfly; // don't worry, we have more effects ;)
    // If you're following the tutorial, the next line lets users pick the filter
    // STVFilter s = self.selectedIndex;
    [self.strive applyFilter:s
                 sampleBuffer:sampleBuffer
                   completion:^(CMSampleBufferRef sampleBuffer) {
                       [self.layer enqueueSampleBuffer:sampleBuffer];
                   }];
}

```

## Celebrate

Build and Run the app, open the bottle, and celebrate!


