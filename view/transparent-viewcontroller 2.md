# [HowTo] Transparent SubViewController

```swift
@objc func addButtonTapped() {
    let nav = //AddCityViewController()
    nav.modalPresentationStyle = .overFullScreen
    self.present(nav, animated: false, completion: nil)
}

//AddCityViewController

override func viewDidLoad() {
    super.viewDidLoad()
    view.backgroundColor = UIColor.init(white: 0.3, alpha: 0.4)  
}
```

## Github Link

[Transparent SubViewController Example Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/AFWeather)