# [HowTo] Advanced UIVIew Shadow Effects

All subclasses of UIView, plus UIView itself, have access to a layer property where we can apply shadow effects directly in code. There are lots of parameters we can tweak:

- **shadowColor**:  controls the color of the shadow, and can be used to make shadows (dark colors) or glows (light colors). This defaults to black.
- **shadowOffset**: controls how far the shadow is moved away from its view. This defaults to 3 points up from the view. **You can think of where the lights are shining.**
- **shadowOpacity**: controls how transparent the shadow is. This defaults to 0, meaning “invisible”.
- **shadowPath**: controls the shape of the shadow. This defaults to nil, which causes UIKit to render the view offscreen to figure out the shadow shape.
- **shadowRadius**: controls how blurred the shadow is. This defaults to 3 points.

Making UIKit calculate the shadow path dynamically is significantly slower than explicitly setting the value, so if you use shadows it’s usually a good idea to set the path. Even better, you can create interesting shapes with the path to make different kinds of shadows.

## Default shadows

```swift
let width: CGFloat = 200
let height: CGFloat = 200

// Better to set the frame value at the beginning.
let vw = UIView(frame: CGRect(x: 0, y: 0, width: width, height: height))
vw.center = view.center
vw.backgroundColor = .white

view.addSubview(vw)
```

```swift
vw.layer.shadowOpacity = 1
```

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112991361-fe786f80-91a1-11eb-9f45-3281f2c9d64f.png"/>
</div>

```swift
vw.layer.shadowPath = UIBezierPath(rect: vw.bounds).cgPath
vw.layer.shadowRadius = 5
vw.layer.shadowOffset = .zero
vw.layer.shadowOpacity = 1
```

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112991367-00423300-91a2-11eb-9574-1c647e8bad2e.png"/>
</div>

## Contact shadows

A contact shadow is one that makes it look like our view is near to or even touching a surface, so the shadow exists directly below the view rather than around it.

```swift
let shadowSize: CGFloat = 20
let contactRect = CGRect(x: -shadowSize, y: height - (shadowSize * 0.4), width: width + shadowSize * 2, height: shadowSize)
vw.layer.shadowPath = UIBezierPath(ovalIn: contactRect).cgPath
vw.layer.shadowRadius = 5
vw.layer.shadowOpacity = 0.4
```

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112991368-00dac980-91a2-11eb-9868-e18151517ed4.png"/>
</div>

```swift
let shadowSize: CGFloat = 20
let shadowDistance: CGFloat = 20
let contactRect = CGRect(x: -shadowSize, y: height - (shadowSize * 0.4) + shadowDistance, width: width + shadowSize * 2, height: shadowSize)
vw.layer.shadowPath = UIBezierPath(ovalIn: contactRect).cgPath
vw.layer.shadowRadius = 5
vw.layer.shadowOpacity = 0.4
```

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112991373-01736000-91a2-11eb-90aa-7d4e83bbcd66.png" />
</div>

## Depth shadows

You can add a 3D effect to your view by casting a shadow in front of it. This is done by creating another custom UIBezierPath shadow path where we position the top of the shadow at the bottom of our view, and the bottom of the shadow some distance further down. We then bring the bottom edges of the shadow outwards so that it’s wider on the bottom than it is at the top, giving a perspective effect.

```swift
let shadowRadius: CGFloat = 5

// how wide and high the shadow should be, where 1.0 is identical to the view
let shadowWidth: CGFloat = 1.25
let shadowHeight: CGFloat = 0.5

let shadowPath = UIBezierPath()
shadowPath.move(to: CGPoint(x: shadowRadius / 2, y: height - shadowRadius / 2))
shadowPath.addLine(to: CGPoint(x: width - shadowRadius / 2, y: height - shadowRadius / 2))
shadowPath.addLine(to: CGPoint(x: width * shadowWidth, y: height + (height * shadowHeight)))
shadowPath.addLine(to: CGPoint(x: width * -(shadowWidth - 1), y: height + (height * shadowHeight)))
vw.layer.shadowPath = shadowPath.cgPath
vw.layer.shadowRadius = shadowRadius
vw.layer.shadowOffset = .zero
vw.layer.shadowOpacity = 0.2
```

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112991375-01736000-91a2-11eb-91a6-34e6aeaa895a.png" />
</div>


## Flat, long shadows

This shadow effect is particularly prevalent in modern design, where shadows are more of a design feature rather than providing any sort of function. Here, the shadow is sharp-edged and moves continuously off into the infinite distance at 45-degree angle.

```swift
vw.layer.shadowRadius = 0
vw.layer.shadowOffset = .zero
vw.layer.shadowOpacity = 0.2

// how far the bottom of the shadow should be offset
let shadowOffsetX: CGFloat = 2000
let shadowPath = UIBezierPath()
shadowPath.move(to: CGPoint(x: 0, y: height))
shadowPath.addLine(to: CGPoint(x: width, y: height))

// make the bottom of the shadow finish a long way away, and pushed by our X offset
shadowPath.addLine(to: CGPoint(x: width + shadowOffsetX, y: 2000))
shadowPath.addLine(to: CGPoint(x: shadowOffsetX, y: 2000))
vw.layer.shadowPath = shadowPath.cgPath

view.backgroundColor = UIColor(red: 230 / 255, green: 126 / 255, blue: 34 / 255, alpha: 1.0)
```

<div>
    <img src="https://user-images.githubusercontent.com/50784573/112991378-020bf680-91a2-11eb-81db-368db5fb75c9.png" />
</div>

## Extensions

```swift
extension UIView {
    
    func makeEdges(cornerRadius: CGFloat, borderWidth: CGFloat, _ color: CGColor = UIColor.white.cgColor) {
        layer.cornerRadius = cornerRadius
        layer.borderWidth = borderWidth
        layer.borderColor = color
    }
    
    func makeShadow(opacity: Float, radius: CGFloat, _ color: CGColor = UIColor.systemGray4.withAlphaComponent(0.7).cgColor) {
        layer.shadowColor = color
        layer.shadowOpacity = opacity
        layer.shadowRadius = radius
        layer.shadowOffset = CGSize(width: 0, height: 0)
    }
    
    func makeShadowWithCAShapeLayer(roundedRect: CGRect, opacity: Float, radius: CGFloat) {
        let shadowLayer = CAShapeLayer()
        
        shadowLayer.path = UIBezierPath(roundedRect: roundedRect, cornerRadius: radius).cgPath
        shadowLayer.fillColor = UIColor.black.cgColor

        shadowLayer.shadowColor = UIColor.systemGray4.cgColor
        shadowLayer.shadowPath = shadowLayer.path
        shadowLayer.shadowOffset = CGSize(width: 0.0, height: 0.0)
        shadowLayer.shadowOpacity = opacity
        shadowLayer.shadowRadius = radius

        layer.insertSublayer(shadowLayer, at: 0)
    }
}
```

## References

[Advanced UIView shadow effects using shadowPath](https://www.hackingwithswift.com/articles/155/advanced-uiview-shadow-effects-using-shadowpath)