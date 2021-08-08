# [HowTo] UINavigationBar Fully Programmatic Approach

```swift
var window: UIWindow?

func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
    guard let scene = (scene as? UIWindowScene) else { return }
    window = UIWindow(windowScene: scene)
    let nav = UINavigationController(rootViewController: //Controller())
    window?.rootViewController = nav
    window?.makeKeyAndVisible()
}
```

## UINavigationBar with Rounded Corners

```swift
//  ViewController.swift

import UIKit

protocol RoundedCornerNavigationBar {
    func addRoundedCorner(OnNavigationBar navigationBar: UINavigationBar, cornerRadius: CGFloat)
}

class ViewController: UIViewController, RoundedCornerNavigationBar {

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        setupNavBar()
    }
    
    private func setupNavBar(){
        title = "Navigation Bar"
        navigationController?.navigationBar.prefersLargeTitles = true
        addRoundedCorner(OnNavigationBar: navigationController!.navigationBar, cornerRadius: 20)
    }
}

extension RoundedCornerNavigationBar where Self: UIViewController{
    
    func addRoundedCorner(OnNavigationBar navigationBar: UINavigationBar, cornerRadius: CGFloat){
        //navigationBar.isTranslucent = false
        //navigationBar.setBackgroundImage(UIImage(), for: .default)
        navigationBar.backgroundColor = .white
        
        let customView = UIView(frame: CGRect(x: 0, y: navigationBar.bounds.maxY, width: navigationBar.bounds.width, height: cornerRadius))
        customView.backgroundColor = .clear
        navigationBar.insertSubview(customView, at: 1)
        
        let shapeLayer = CAShapeLayer()
        shapeLayer.path = UIBezierPath(roundedRect: customView.bounds, byRoundingCorners: [.bottomLeft,.bottomRight], cornerRadii: CGSize(width: cornerRadius, height: cornerRadius)).cgPath
        shapeLayer.shadowColor = UIColor.lightGray.cgColor
        shapeLayer.shadowOffset = CGSize(width: 0, height: 4.0)
        shapeLayer.shadowOpacity = 0.8
        shapeLayer.shadowRadius = 2
        shapeLayer.fillColor = UIColor.white.cgColor
        customView.layer.insertSublayer(shapeLayer, at: 0)
    }
}

```

## UINavigationBar with LargeTitle & Custom Color

```swift
 override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    navigationController?.navigationBar.prefersLargeTitles = true

    let appearance = UINavigationBarAppearance()
    //UINavigationBar.appearance().largeTitleTextAttributes
    appearance.backgroundColor = UIColor(red: 142/255, green: 68/255, blue: 173/255, alpha: 1.0)
    //if it doesn't work in ios 13+
    //navigationController?.navigationBar.barTintColor = UIColor(red: 142/255, green: 68/255, blue: 173/255, alpha: 1.0)
    appearance.titleTextAttributes = [.foregroundColor: UIColor.white]
    appearance.largeTitleTextAttributes = [.foregroundColor: UIColor.white]
    
    navigationController?.navigationBar.topItem?.title = "LargeTitle"
    navigationController?.navigationBar.tintColor = .white
    navigationController?.navigationBar.standardAppearance = appearance
    navigationController?.navigationBar.compactAppearance = appearance
    navigationController?.navigationBar.scrollEdgeAppearance = appearance

    //var image = UIImage(systemName: "location")
    //image = image?.withRenderingMode(.alwaysOriginal)
    //navigationController?.navigationBar.prefersLargeTitles = true
    //navigationItem.leftBarButtonItem = UIBarButtonItem(image: image, style:.plain, target: self, action:  #selector(locationTapped))
    //navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .add, target: self, action: #selector(addTapped))
    //navigationItem.rightBarButtonItem?.tintColor = .black
}
```

## References

[Apple Developer Document](https://developer.apple.com/documentation/uikit/uinavigationcontroller/customizing_your_app_s_navigation_bar)