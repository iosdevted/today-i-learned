# [HowTo] Extensions for UIColor & UIFont

자주 사용하게 되는  색상이나 폰트는 Extension을 이용하여 코드를 간편하게 만들 수 있다. 

```swift
import UIKit

extension UIFont {
    
    private static func customFont(name: String, size: CGFloat) -> UIFont {
        let font = UIFont(name: name, size: size)
        return font ?? UIFont.systemFont(ofSize: size)
    }
    
    static func mainRegularFont(ofSize size: CGFloat) -> UIFont {
        return customFont(name: "RedHatDisplay-Regular", size: size)
    }
    
    static func mainBoldFont(ofSize size: CGFloat) -> UIFont {
        return customFont(name: "RedHatDisplay-Bold", size: size)
    }
}
```

```swift
import UIKit

extension UIColor {
    
    static var oceanBlue: UIColor {
        return .init(red: 70/255, green: 75/255, blue: 114/255, alpha: 1/1)
    }
    
}

```