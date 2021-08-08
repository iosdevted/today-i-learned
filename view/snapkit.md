# [HowTo] SnapKit


```
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '10.0'
use_frameworks!

target '<Your Target Name>' do
    pod 'SnapKit', '~> 4.0'
end
```

SnapKit is designed to be extremely easy to use. Let’s say we want to layout a box that is constrained to it’s superview’s edges with 20pts of padding.

| ViewAttribute  |    NSLayoutAttribute   |
|----------------|------------------------|
| view.snp.left  | NSLayoutAttribute.left |
| view.snp.right |	NSLayoutAttribute.right|
| view.snp.top	 |  NSLayoutAttribute.top|
| view.snp.bottom|	NSLayoutAttribute.bottom|
| view.snp.leading|	NSLayoutAttribute.leading|
| view.snp.trailing|	NSLayoutAttribute.trailing|
| view.snp.width|	NSLayoutAttribute.width|
| view.snp.height|	NSLayoutAttribute.height|
| view.snp.centerX|	NSLayoutAttribute.centerX|
| view.snp.centerY|	NSLayoutAttribute.centerY|
| view.snp.lastBaseline|	NSLayoutAttribute.lastBaseline|

<br />

```swift
let box = UIView()
superview.addSubview(box)

box.snp.makeConstraints { (make) -> Void in
    make.top.equalTo(superview).offset(20)
    make.left.equalTo(superview).offset(20)
    make.bottom.equalTo(superview).offset(-20)
    make.right.equalTo(superview).offset(-20)
}
```

Or even shorter:

```swift
let box = UIView()
superview.addSubview(box)

box.snp.makeConstraints { (make) -> Void in
    make.edges.equalTo(superview).inset(UIEdgeInsetsMake(20, 20, 20, 20))
}
```

```swift
make.top.equalTo(42)
make.height.equalTo(20)
make.size.equalTo(CGSize(width: 50, height: 100))
make.edges.equalTo(UIEdgeInsets(top: 10, left: 0, bottom: 10, right: 0))
make.left.equalTo(view).offset(UIEdgeInsets(top: 10, left: 0, bottom: 10, right: 0))

make.width.equalToSuperview().multipliedBy(0.45)
make.height.equalTo(45)
make.top.equalTo(viewProgress.snp.bottom).offset(32)
make.centerX.equalToSuperview()
```

## Prioritize

You may also use priority shortcuts: `.low`, `.medium`, `.high`, `.required`

```swift
make.top.equalTo(label.snp.top).priority(600)
make.top.equalTo(label.snp.top).priority(.medium)
```

## Center

```swift
// make centerX and centerY = button1
make.center.equalTo(button1)

// make centerX = superview.centerX + 5, centerY = superview.centerY + 5
make.center.equalTo(superview).offset(5)
```

## Size

```swift
// make width and height greater than or equal to titleLabel
make.size.greaterThanOrEqualTo(titleLabel)

// make width = superview.width + 100, height = superview.height + 100
make.size.equalTo(superview).offset(100)
```

## References

[Snapkit.io](http://snapkit.io/docs/)

[SnapKit for iOS: Constraints in a Snap](https://www.raywenderlich.com/3225401-snapkit-for-ios-constraints-in-a-snap)
