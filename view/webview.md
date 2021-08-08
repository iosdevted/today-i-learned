# [HowTo] Implementable URL call processing method

Let's take a look at the following three methods of handling URL calls that can be implemented in iOS Webview.

## Connection to Safari

A specific URL connection method through an existing browser app.

Derive the result of moving away from the current app and move to another app(Safari).

```swift
let url = URL(string: "https://sunggweon.dev")
UIApplication.shared.open(url!, options: [:])
```

## WKWebview

[The Ultimate Guide to WKWebView](https://www.hackingwithswift.com/articles/112/the-ultimate-guide-to-wkwebview)

[WKWebView에 Back/Forward Button 만들기](https://zeddios.tistory.com/374)

It can be customized as desired, and can be used from within the app. But there is no back button in WKWebView. You have to make it all from scratch.

```swift
import WebKit

@IBOutlet var webView: WKWebview!

// Create URL object
let url = URL(string: "https://sunggweon.dev")
let request = URLRequest(url: url!)

self.webView.load(request)
```

## SFSafariViewController

Back/forward, bookmark, and share functions used in the Safari app are supported as they are, but can be used inside the app.

```swift
import SafariServices

// Create URL object
let url = URL(string: "https://sunggweon.dev")
let safariViewController = SFSafariViewController(url: url)

// Transition
present(safariViewController, animated: true, completion: nil)
```

