# [HowTo] UICollectionView Fade In & Out Animation

Fade In / Out Animations without using 3rd party library.

Fade animations basically involve adjusting a `UIView‘s` alpha value from 1.0 to 0.0 (fade out) or 0.0 to 1.0 (fade in) over a specified duration using some kind of easing option (like starting fast, then slowing down at the end of the animation, or starting slow and speeding up at the end of the animation).

<img src="https://user-images.githubusercontent.com/50784573/110588378-49f9b800-81b8-11eb-9cd9-cc2728da58a5.gif" height=500/>


```swift
//MARK: - Properties

private var collectionViewWidth: CGFloat {
    return collectionView.frame.size.width
}

//MARK: - Helpers

private func getCurrentIndex() -> Int {
    return Int(collectionView.contentOffset.x / collectionView.frame.width)
}

//MARK: - UICollectionViewDelegate, UICollectionViewDataSource

extension OnboardingViewController: UICollectionViewDelegate, UICollectionViewDataSource {

    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        let x = scrollView.contentOffset.x
        let index = getCurrentIndex()
        let fadeInAlpha = (x - collectionViewWidth * CGFloat(index)) / collectionViewWidth
        let fadeOutAlpha = CGFloat(1 - fadeInAlpha)
        let canShow = (index < items.count - 1)
        
        guard canShow else { return }
        
        imageViews[index].alpha = fadeOutAlpha
        imageViews[index + 1].alpha = fadeInAlpha
    }
}

//MARK: - UICollectionViewDelegateFlowLayout

extension OnboardingViewController: UICollectionViewDelegateFlowLayout {
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        return collectionView.frame.size
    }
}

```

## Github Link

[Fade In & Out Animation Github Link](https://github.com/cielgrisdemoscou/ios-onboarding/tree/master/onboarding-2)