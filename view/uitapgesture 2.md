# [HowTo] UITapGestureRecognizer; Dismiss if any tap detected on superview

Two ways for dismissing the superview by UITapGestureRecognizer.

## UIGestureRecognizerDelegate

```swift
//MARK: - Selectors
@objc func dismissViewController() {
    dismiss(animated: false, completion: nil)
}
    
//MARK: - Helpers
private func setupGestures() {
    let tapGesture = UITapGestureRecognizer(target: self, action: #selector(dismissViewController))
    tapGesture.delegate = self
    view.addGestureRecognizer(tapGesture)
}

//MARK: - UIGestureRecognizerDelegate
extension AddCityViewController: UIGestureRecognizerDelegate {
    
    func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldReceive touch: UITouch) -> Bool {
        return touch.view == self.view
    }
}
```

## Gesture Recognizer To Limited Frame

```swift
func configureBlackView() {
    blackView.frame = CGRect(x: xOrigin, y: 0, width: xOrigin, height: self.view.frame.height)
    blackView.backgroundColor = UIColor(white: 0, alpha: 0.5)
    blackView.alpha = 0
    view.addSubview(blackView)
    
    let tap = UITapGestureRecognizer(target: self, action: #selector(dismissMenu))
    blackView.addGestureRecognizer(tap)
}
```