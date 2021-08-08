# [HowTo] UIView Transition Animation

Transitions are predefined animations you can apply to views. These predefined animations don’t attempt to interpolate between the start and end states of your view. Instead, you’ll design the animations with the transitions API so that the various changes in your UI appear natural.

<img src="https://user-images.githubusercontent.com/50784573/110328329-929d5e00-805e-11eb-83a0-e6c844060dcf.gif" height=500/>

```swift
//MARK: - Life Cycle

override func viewDidLoad() {
    super.viewDidLoad()
    configureUI()
    setupPlaceholderItems()
    setupPageControl()
    setupScreen(index: currentPage)
    setupGestures()
}

//MARK: - Selectors

@objc private func handleTapAnimation() {
    self.view.isUserInteractionEnabled = false
    
    UIView.animate(withDuration: 0.5, delay: 0, usingSpringWithDamping: 0.5, initialSpringVelocity: 0.5, options: .curveEaseInOut, animations: {
        self.mainLabel.alpha = 0.5
        self.mainLabel.transform = CGAffineTransform(translationX: -20, y: 0)
        self.detailLabel.alpha = 0.5
        self.detailLabel.transform = CGAffineTransform(translationX: -20, y: 0)
    }, completion: { _ in
        self.currentPage += 1
        
        if self.isOverLastItem() {
            self.showMainApp()
        } else {
            self.setupScreen(index: self.currentPage)
        }
        
        self.view.isUserInteractionEnabled = true
    })
}

//MARK: - Helpers
private func setupScreen(index: Int) {
    mainLabel.text = items[index].title
    detailLabel.text = items[index].detail
    updateBackgroundImage(index: index)
    pageControl.currentPage = index
    mainLabel.alpha = 1.0
    detailLabel.alpha = 1.0
    //apply a transform to a UILabel before animating
    mainLabel.transform = .identity
    detailLabel.transform = .identity
}

private func updateBackgroundImage(index: Int) {
    let image = items[index].bgImage
    
    UIView.transition(with: bgImageView, duration: 0.5, options: .transitionCrossDissolve, animations: {
        self.bgImageView.image = image
    }, completion: nil)
    
}
```

## References

[Apple Developer Document](https://developer.apple.com/documentation/uikit/uiview/1622574-transition)

[AnimationOptions - transition](https://zeddios.tistory.com/743)

[UIView Transition Animation Github Link](https://github.com/cielgrisdemoscou/ios-onboarding/tree/master/onboarding-1)