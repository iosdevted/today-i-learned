# [Swift] prepareForReuse()

If you have images loading in to your cell and you’re scrolling through fast and your internet connection isn’t the best, `you’ll start noticing some images are where they shouldn’t be. This is because we are reusing cells`, so the text and images are being rolled over, leading to the wrong images in the wrong cells.

All we need to do to is use the prepareForReuse() function `in the custom cell`. This function is called before cell reuse, letting you cancel current requests and perform a ‘reset’.

```swift
//https://github.com/cielgrisdemoscou/ios-project/blob/master/custom-tabs/custom-tabs/Views/TabCell.swift
override func prepareForReuse() {
    super.prepareForReuse()
    tabTitle.text = ""
    tabIcon.image = nil
}
```

Now your table view scrolling will be smooth (through cell reuse) and incorrect images will not carry over (through prepareForReuse()).

## References

[Apple Developer Document](https://developer.apple.com/documentation/uikit/uitableviewcell/1623223-prepareforreuse)

[Custom Cell을 재사용할 때 생기는 문제점](https://baked-corn.tistory.com/51)