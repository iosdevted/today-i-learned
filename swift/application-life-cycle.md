# Application Life Cycle

## How the application starts:

When The User just turned on his phone, no applications are running except those that are belong to the operating system. Your Application is not running. After the User taps your app icon, Springboard the part of the OS, that operates the home screen of iOS, launches your app. Your app and the shared libraries it needs to execute, will be loaded into the memory, while springboard animates your app’s launch screen. eventually your app begins execution and application delegate receives the notifications.

During startup, the `UIApplicationMain` function sets up several key objects and starts the app running. At the heart of every iOS app is the `UIApplication` object, whose job is to facilitate the interactions between the system and other objects in the app.

## The Main run Loop

> Run Loop is a mechanism that allows threads to process events at any time without exiting.

An app’s main run loop processes all user-related events. The UIApplication object sets up the main run loop at launch time and uses it to process events and handle updates to view-based interfaces. As the name suggests, the main run loop executes on the app’s main thread. This behavior ensures that user-related events are processed serially in the order in which they were received.

![alt text](<https://dl.dropboxusercontent.com/s/i6ed655jlzrizs1/IMG_1006.PNG> "Main run Loop")

Events are queued internally by the app and dispatched one-by-one to the main run loop for execution. The UIApplication object is the first object to receive the event and make the decision about what needs to be done. A touch event is usually dispatched to the main window object, which in turn dispatches it to the view in which the touch occurred.

## UIApplication

> The centralized point of control and coordination for apps running in iOS.

Every iOS app has exactly one instance of UIApplication (or, very rarely, a subclass of UIApplication). When an app is launched, the system calls the `UIApplicationMain(_:_:_:_:)` function; among its other tasks, this function creates a Singleton UIApplication object. Thereafter you access the object by calling the shared class method.

A major role of your app’s application object is to handle the initial routing of incoming user events. It dispatches action messages forwarded to it by control objects (instances of the `UIControl` class) to appropriate target objects. The application object maintains a list of open windows (`UIWindow` objects) and through those can retrieve any of the app’s `UIView` objects.

## App State

- **Not Running** — Either the application has not started yet or was running and has been terminated by the system.

- **Inactive** — An application is running in the Foreground but is not receiving any events. This could happen in case a Call or Message is received. An application could also stay in this state while in transition to a different state. In this State, we can not interact with app’s UI.

- **Active** — An application is running in the Foreground and receiving the events. This is the normal mode for the Foreground apps. The only way to go to or from the Active state is through the Inactive state. User normally interacts with UI, and can see the response/result for user actions.

- **Background** — An application is running in the background and executing the code. Freshly launching apps directly enter into In-Active state and then to Active state. Apps that are suspended, will come back to this background state, and then transition to In-Active → Active states. In addition, an application being launched directly into the background enters this state instead of the inactive state.

- **Suspended** — An application is in the background but is not executing the code. The system moves the application to this state automatically and does not notify. In case of low memory, the system may purge suspended application without notice to make free space for the foreground application. Usually after 5 secs spent in the background, apps will transition to Suspend state, but we can extend the time if app needs.

![alt text](<https://dl.dropboxusercontent.com/s/wpmf59gfnaiuafr/IMG_1008.PNG> "App State")

## Process Life Cycle

* `applicationWillEnterForeground`

	* In iOS 4.0 and later, UIKit calls this method as part of the transition from the background to the active state. You can use this method to undo many of the changes you made to your app upon entering the background. 

	* App State : Background or Not Running -> In-Active -> Active

* `applicationDidBecomeActive`

	* UIKit calls This method to let your app know that it moved from the inactive to active state. The app moves to the active state because it was launched by the user or the system, or because the user ignores an interruption (like an incoming phone call or SMS message) that sent the app temporarily to the inactive state.

	* App State : Active

* `applicationWillResignActive`

	* UIKit calls this method to let your app know that it is about to move from the active to inactive state. The app moves to the inactive state because of temporary interruptions like an incoming phone call or SMS message, or when the user quits the app and it begins the transition to the background state.

	* App State : Active -> In-Active -> Background

* `applicationDidEnterBackground`

	* Use this method to release shared resources, invalidate timers, and store enough app state information to restore your app to its current state in case it is terminated later. 

	* Perform any tasks relating to adjusting your user interface before this method exits. Move other tasks (such as saving state) to a concurrent dispatch queue or secondary thread as needed. 

	* App State : Background

## The lunch cycle

![alt text](<https://miro.medium.com/max/700/1*0XS9grFLWcz6Quzdu5syGw.png> "Launching an app into the foreground")
##### Launching an app into the foreground 

![alt text](<https://miro.medium.com/max/700/1*5LKm3FR67tuGYEeh_eDgIA.png> "Launching an app into the background")
##### Launching an app into the background

![alt text](<https://miro.medium.com/max/700/1*xPCYq-6QcCR8FvB1L1_jfw.png> "Moving from the foreground to the background")
##### Moving from the foreground to the background


## Execution Methods 

- `application:willFinishLaunchingWithOptions` — This method is called after your application has been launched successfully. It is the first method from our app delegate , which will be called. You can execute your code if the launch was successful.

- `application:didFinishLaunchingWithOptions` — This method is called before the app’s window is displayed. You can finalise your interface and can provide the root ViewController to the window.

- `applicationDidBecomeActive` — This method is either called to let your app know that it moved from the inactive to active state or your app was launched by the user or the system or in case user ignores an interruption (such as an incoming phone call or SMS message) that sent the application temporarily to the inactive state. You should use this method to restart any tasks that were paused (or not yet started) while the app was inactive.

- `applicationWillResignActive` — This method is called to let your app know that it is about to move from active to inactive state. This can happen in case of any interruptions (such as an incoming phone call or SMS message or Calendar alerts) or when the user quits the app. You should use this method to pause any ongoing tasks or disable timers etc.

- `applicationDidEnterBackground` — This method is called to let app know that it is not running in the foreground. You have approximately five seconds to perform any tasks and return back. In case you need additional time, you can request additional execution time from the system by calling beginBackgroundTask(expirationHandler:). If the method does not return before time runs out your app is terminated and purged from memory.

- `applicationWillEnterForeground` — This method is called as a part of the transition from the background to the active state. You should use this to undo any change you made to your app upon entering the background. applicationDidBecomeActive method is called soon after this method has finished its execution which then moves the app from the inactive to the active state.

- `applicationWillTerminate` — This method is called to let you know that your app is about to terminate. You should use this method to perform any final clean-up task. You have approximately five seconds to perform any tasks and return back. If the method does not return before time expires, the system may kill the process altogether. This method may be called in situations where the app is running in the background (not suspended) and the system needs to terminate it for some reason. You shouldn’t wait applicationWillTerminate to be called in order to save your data. There are some cases when applicationWillTerminate won’t be called before app termination. For example the system will not call applicationWillTerminate when the device reboots.

## References

More information on Markdown can be found at the following links:

- [Apple UIKit](https://developer.apple.com/documentation/uikit)
- [Apple UIApplication](https://developer.apple.com/documentation/uikit/uiapplication)
- [Apple UIApplicationMain](https://developer.apple.com/documentation/uikit/1622933-uiapplicationmain?language=objc)
- [Apple UIApplicationDelegate](https://developer.apple.com/documentation/uikit/uiapplicationdelegate)
- [Medium iOS App Life Cycle](https://medium.com/@neroxiao/ios-app-life-cycle-ec1b31cee9dc)
- [Medium The iOS Application Lifecycle](https://medium.com/hackernoon/application-life-cycle-in-ios-12b6ba6af78b)
- [Apple Responding to the Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app)
- [Blog iOS Application Life Cycle](https://hcn1519.github.io/articles/2017-09/ios_app_lifeCycle) 


