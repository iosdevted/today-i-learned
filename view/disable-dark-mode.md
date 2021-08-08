# [HowTo] Disable / Enable Dark Mode for iOS App

I thought I had to set the Dark Mode separately, but when I uploaded it to the App Store, I found out that the Dark Mode is automatically set. So, if you haven't considered Dark mode individually, it is better to turn off the dark mode setting as follows. There are two ways to cancel Dark Mode.

## To override the ViewController style

1: overrideUserInterfaceStyle = .dark //For dark mode

2: overrideUserInterfaceStyle = .light //For light mode

```swift
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        overrideUserInterfaceStyle = .light    
    }
}
```

## Adding a key in info.plist

Simply you can add a new key Appearance(UIUserInterfaceStyle) in your app info.plist and set its value to Light or Dark. this will override the app default style to the value you provide.

```
Appearance(UIUserInterfaceStyle) - Light
```

## References

[Disable “Dark Mode” in iOS 13 for your iOS App](https://medium.com/@akshay.s.somkuwar/disable-dark-mode-in-ios-13-for-your-ios-app-c025b446d87b)