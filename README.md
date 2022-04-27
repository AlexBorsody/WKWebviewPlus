

Note:
> PWA Designer is an enhanced implementation of WKWebView. The project includes customization of the UI, push notifications, authentication with iPhone biometric sensors and more.

## **Contents**

* [Requirements](#requirements)
* [Installation](#installation)
* [Usage](#usage)

## <a name="requirements"></a> Requirements

* iOS 13.0+ / Mac OS X 11+
* Xcode 13.3+

## <a name="installation"></a> Installation

---

## Usage

### Quick Start

Note:
> The project is built using MVP architecture (Model - View - Presenter).

To successfully launch the project, you need to connect the [SnapKit](https://github.com/SnapKit/SnapKit#manually) library to the project

> The library is already in the project and connected via Swift Package Manager. If you plan to use another package manager (Carthage or Cocoa Pods) you need to remove the library from the project and re-attach it using your package manager.

If you plan to use Firebase to receive push notifications, analytics and crash tracking, you need to create a project on the [Firebase](https://firebase.google.com) resource and connect the appropriate [library](https://github.com/firebase/firebase-ios-sdk).

> Firebase is already connected to the project and uses the following additions:
>
>* FirebaseAnalytics
>* Crashlytics
>* FirebaseMessaging
>
>If you need to use other add-ons, you must add them yourself.
>
>When connecting to Firebase, you must replace the ***GoogleService-Info*** file with the one provided to you when creating the project on the Firebase site. Without this file, you will get an error when starting the application, if you do not manually disable the use of Firebase. You will read about this below
>
>We recommend that you place the file as follows in the project.
>
>/ios-pwa/Support Files/GoogleService-Info.plist




### Theme

### Preloader

There is a customizable loading screen animation/preloader that is seen on the startup screen.
To change it just go to /Support Files where you will find a preloader file in  w:200 x h:200 gif format.

This preloader will also show on the first page load before, in the case your web preloader animation does not display (we recommend using a web preloader that fires on click before each page load).

If you just want to change the display of the animation you just need to replace the gif file with your own.
The file should be with a transparent background, as well as with the adjusted animation speed and all the necessary styles.

> To change the path of the preloader navigate to modules/preloader and open the PreloaderViewController file.

> On line 45 of the code you can find a method to adjust the size of setupLayout ()
We set the initial value as a square that occupies 20% of the width of the screen where the animation starts.

```
private func setupLayout() {
   view.addSubview(animationLoadingImageView)
        
   let size = UIScreen.main.bounds.width * 0.2
        
   animationLoadingImageView.snp.makeConstraints({
      $0.centerX.centerY.equalToSuperview()
      $0.width.height.equalTo(size)
    })
}

// let size = UIScreen.main.bounds.width * 0.2
// let size = CGSize(width: 400, height: 400)
```

### Colors

We use color constants to customize the theme of the application. You can find them at /Support Files/Themes.xcassets
Since iOS 13, Apple has added a new constant type of AccentColor.

To set this color, you need to follow the path above, select AccentColor and use the color palette to select the color you want.

If you want to add other colors to the Storyboard system you need to call the context menu on the color bar and select "New Color Set."

> Example color name `c-controller-background`.


WARNING!!! In order to start using colors in the project code, you need to perform the following steps.

Go to the /Utils/Extensions/Extension+UIColor.swift file

```
extension UIColor {
    struct Pallete {
        static let accentColor = UIColor(named: "AccentColor")!
        static let cBlack = UIColor(named: "c_black")!
        
        struct View {
            static let cLaunchBackground = UIColor(named: "c_launch_background")!
            
            static let cControllerBackground = UIColor(named: "c_controller_background")!
            static let cWrapperBackground = UIColor(named: "c_wrapper_background")!
        }
        
        // static let <colorName> = UIColor(named: "<added color name in library>")!
    }
}

// colorName - the name you will use in the code may be the same as the one you added in the library

// added color name in library - the name of the color that you added in the library, be sure to check the correctness of the entered name, otherwise when using the program will crash

```

> In order to use the added color in the code, you need to add the following color call code.

```
// webView.backgroundColor = UIColor.Pallete.View.cWrapperBackground
// or
// UIColor.Pallete.cWrapperBackground
```

### Manage resources

If you need to change or add new resources to the project - pictures or any other media materials, you can add it to the catalog by /Support Files/Assets.xcassets

> In this directory, you can change the application icon by adding the appropriate application icons with the necessary dimensions to the AppIcon element.

Once you have added a picture to the directory, you can click on it and view all the settings on the right.

> If you are adding a svg, pdf format image, we recommend changing the option render us to -> Template Image
> Then add an option Scales -> Single Scale
> 
> Then heck the box Preserve Vector Data 
> This will allow you to use the image at any resolution, similar to a vector image.

Now to use the previously added photo you need to create a photo object and place it in UIImageView

```
let imageView = UIImageView()
let image = UIImage(named: "<your image name>")
// imageView.contentMode = .scaleAspectFit
// imageView.contentMode = .scaleAspectFill
```
> The scaleAspectFill option will stretch the image to fit.
> The scaleAspectFit option will contain the image to fit.


### Style for status bar

Whenever you change your URL, there is a chance that you will need to change the status bar style depending on the color of your web page.

If you go to /Modules/Wrapper/WrapperViewController.swift you will find at the beginning a method that updates the status bar style and we can do this for specific URLs.

```
override var preferredStatusBarStyle: UIStatusBarStyle {
   return statusBarStyle()
}
```

Once the transition to another page is complete, we call a method that updates the bar status style.

```
func handleURL() {  
   
   setNeedsStatusBarAppearanceUpdate()
   
   // ...
}
```

This method will cause a forced update, which in turn will refer to the statusBarStyle() method to get the update.

```
private func statusBarStyle() -> UIStatusBarStyle {
   guard let currentLoadedUrl = presenter.currentLoadedUrl else { return .default }
   guard let url = URL(string: currentLoadedUrl) else { return .default }
        
   let components = url.pathComponents
        
   let exPaths = [
       "dashboard"
   ]
        
   if components.contains(where: exPaths.contains) {
       return .lightContent
   } else {
      return .darkContent
   	}
}

// let exPaths = [
//    	"dashboard",
//		"panel",
//		"and more path for light status bar style"
// ]

```

You need to clearly define the URL path by following which style the bar status will change to light. To do this, you need to add the last component of the path to the array.

>For example:
>https://pwa.com/panel/dashboard
>
>The system will get the panel and dashboard components from theURLurl and check if one of the components is in the array, in this example the status bar will be light in color `lightContent`.

### Other

The project has problems with adjusting the orientation of the interface. To solve this, we recommend using the following solution

**Follow steps:**

1. Choose your target.

2. Select Build Settings.

3. Select All and Combined options.

4. Search orientation keywords.

5. Notice Supported Interface Orientations(iPhone), click the value on the right.

6. Delete the orientations you don't want.


### Supporting Associated Domains

If you need to combine your application with your web resource you need to use an Associative Domain.

The project includes settings for this.

1. Go to project target select ios-pwa and press Signing and Capabilities in the settings panel.

2. Find the field in the list Associated Domains click plus or add and indicate your resource by example.

```
webcredentials:{google.com}
```

The file shouldn't have an extension and should have a name like apple-app-site-association.

The file will need to be placed at the root of your resource.

```
https://<yourdomain.com>/apple-app-site-association
```

The file will contain the following JSON.

```
{ 
	"webcredentials": { 
		"apps": ["XXXXXXXXXX.com.designer.pwa"] 
	} 
}
```

>XXXXXXXXXX - Your team ID.
>
>com.designer.pwa - Your app bundle ID.

You can find your team ID here [Apple Developer](https://developer.apple.com)

More details can be found [here](https://developer.apple.com/documentation/xcode/supporting-associated-domains)

### Biometrics

You can activate this functionality in /Support Files/Configuration.plist -> isBiometricsUsage (set 1 or true)

Once activated, you will be given the opportunity to use biometric sensors when loading a web page.

Biometric authentication has several modes of operation. The first is the beginning when the user receives a permission request to use biometric data, if the user has not granted the permission then he will be asked each time to enter the access code to his phone.

If the user has granted the permission, the system will automatically call the screen blur and then display the authentication request window using one of the sensors of the device. If the user successfully passes, the system will delete the blur and show the user the main page that was loaded.

If the user is unable to pass the authentication, he will be shown a window with an error message and the button to repeat again.

By default, the authentication request will be executed each time the user opens the application, as well as each time the application moves from the background to the active state.

>Please note that the call of the authentication window is controlled by the iOS system to avoid overload, the system will call the window if necessary after calling the window from the code.

If you need to exclude pages where you call authentication you need to go to /Modules/WrapperPresenter.swift and find the variable that is responsible for the exception.

```
var biometricExceptionMarkers: [String] = ["user/login", "user/password"]
```

We want to exclude /user/login and user/password from biometric authentication because we only want biometric to show when the user is logged in, this way when the user opens the app after closing they can authenticate with biometric and not need to enter a password each time.

```
var biometricExceptionMarkers: [String] = ["user/panel?userId=1", "user/password-reset?like=2&point=10"]
```

>For a more detailed study of authentication, you can read the file /Utils/Services/BiometricIDAuthService.swift, which describes all possible errors and methods for working with Face ID and Touch ID.


### Project structure
```
/ios-pwa
	/Utils
		/Architecture
		/Services
		/Extensions
	/Modules
	/Storyboards
	/Support Files
```
We recommend saving all auxiliary files in the / Utils directory.
In this directory you will find the **AppContext** file. Use this file to declare lone objects that you can use in the future from anywhere in the program, for example...

```
let configurationService = ConfigurationService.shared

let configurationService = AppContext.shared.configurationService
```

The /Architecture directory contains the architecture files. **Deleting or modifying these files is at your own risk.**

We recommend placing files of services that provide functionality that can be called from anywhere in your project in the /Services directory.

We recommend placing files placing files that extend the capabilities of your code in the /Extensions.

The /Modules directory contains all the files of your modules, for easy reading, all the files are located in the directories that compose the module into a single directory.  In this directory you will find the ViewFactory system file (**deleting or modifying this file you do so at your own risk**). To create any module of the file program, you must use the code for example

 ```
 func createLoadingScreen(viewModel: PreloaderViewModel) -> PreloaderViewController {
 	let controller = PreloaderViewController()
        
   	let presenter = PreloaderPresenter(viewModel: viewModel)
   	bind(view: controller, presenter: presenter)
   	return controller
 }
 
 let viewModel = PreloaderViewModel(title: nil)
 let controller = ViewFactory.shared.createLoadingScreen(viewModel: viewModel)
 ```

We do not use Storyboard files to create the interface, the whole interface is built manually. If you want to configure the interface differently you need to do it yourself. To do this, in the /Storyboards directory, you will find the Root.storyboard file (this is an empty canvas). To call a file from Storyboard you need to use the extension that is located in the project and execute the following code when initializing the module in ViewFactory

Note:
> Before you can make the call, you need to set your controller ID in Storyboard.

```
func createLoadingScreen(viewModel: PreloaderViewModel) -> PreloaderViewController {
	let storyboard = UIStoryboard(storyboard: .root)
   	let controller = storyboard.instantiateViewController(withIdentifier: "PreloaderViewController") as! PreloaderViewController
        
   	let presenter = PreloaderPresenter(viewModel: viewModel)
   	bind(view: controller, presenter: presenter)
   	return controller
 }
 
 let viewModel = PreloaderViewModel(title: nil)
 let controller = ViewFactory.shared.createLoadingScreen(viewModel: viewModel)
```

>In order for the Storyboard ID to appear as an enumeration element when calling UIStoryboard (storyboard: .root), you need to add it to the Utils/Extension+Storyboard.swift file

```
enum Storyboard: String {
	case root
        
   	var filename: String {
   		return rawValue.capitalizingFirstLetter()
   	}
}
```

In the /Support Files directory, we store all auxiliary files, such as .plist files and resource files. There you can add any files you plan to use in the project

### App Configuration

The basic settings are set in the file Support Files/Configuration.plist.

>This file contains settings for two possible build schemes (Debug/Release).
>
>The configuration file automatically determines the active project schema and retrieves the corresponding nal values.
>
>To change the configuration you need to follow the settings of the project scheme.

These 3 settings are available

1. resourseURL (String) - Sets the value for the start_url

2. testResourseURL (String) - If you need to use a test link you can install it here.

3. isFirebaseUsage (Bool) - If you use Firebase you must set the value here to true, otherwise the library will not be called.

> The main file that manages the configuration is Utils/Services/ConfigurationService.swift
> 
> Custom keys need to be added to ConfigurationService

```
enum Key: String {
	case resourseURLKey = "resourseURL"
   	case testResourseURLKey = "testResourseURL"
        
   	case isFirebaseUsageKey = "isFirebaseUsage"
}
```

>You must use the following code to call any value.
>
>Also, for a full call, you need to specify the type of variable explicitly.
>
>We can expand the service file and call exactly the value we need.

```
let isFirebaseUsage: Bool = AppContext.shared.configurationService.isFirebaseUsage

let initialURL: URL = AppContext.shared.configurationService.getResourseURL()
```

```
extension ConfigurationService {
    
    var isFirebaseUsage: Bool {
        
    let isFirebaseConected: Bool = getActiveVariableValue(forKey: .isFirebaseUsageKey)
        
        if isFirebaseConected {
            guard let _ = Bundle.main.path(forResource: "GoogleService-Info", ofType: "plist") else {
                fatalError("GoogleService-Info is missing. Please register Firebase project and add file to project. Go to https://firebase.google.com")
            }
            return isFirebaseConected
        } else {
            return isFirebaseConected
        }
    }
}
```

Use this for a normal call.

```
let isFirebaseConected: Bool = AppContext.shared.configurationService.getActiveVariableValue(forKey: .isFirebaseUsageKey)
```

### App Start

The program is launched and the initial window is installed in the file AppDelegate.swift

For convenience, an extension is added for this file.

In the extension, it is recommended that you make all the necessary calls that are required when starting the program.  An extension file is used to test Firebase. 

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
	configureFirebase()
	
	return true
}
```

```
import UIKit

#if canImport(Firebase)
import Firebase
#endif

extension AppDelegate {
    
    fileprivate var configurationManager: ConfigurationService {
        return AppContext.shared.configurationService
    }
    
    func configureFirebase() {
        // Configure Firebase if (isFirebaseUsage == true/1)
        if configurationManager.isFirebaseUsage {
            FirebaseApp.configure()
        }
    }
}
```

To install the initial window, you need to make sure that the following code is executed.

```
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
                
        window = UIWindow(frame: UIScreen.main.bounds)
        
        // Use ViewFactory.shared to get the controller object
        window?.rootViewController = ViewFactory.shared.createWrapperScreen()
        window?.makeKeyAndVisible()
        
        return true
    }
}
```

```
window?.rootViewController = ViewFactory.shared.createWrapperScreen()
```

ViewFactory create a controller for us and passes it as the main one for the window.

```
func createWrapperScreen() -> WrapperViewController {
	let controller = WrapperViewController()
        
   	let initialURL: URL = AppContext.shared.configurationService.getResourseURL()
        
   	let viewModel = WrapperViewModel(title: nil, initialURL: initialURL.absoluteString)
   	let presenter = WrapperPresenter(viewModel: viewModel)
        bind(view: controller, presenter: presenter)
        return controller
}
```

>If you need to trigger a test link (regardless of the scheme), replace the next call.

```
let initialURL: URL = AppContext.shared.configurationService.getResourseURL(useForTesting: true)
```
