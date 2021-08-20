# [HowTo] Custom Tabs Fully Programmatic Approach

Custom Tabs without using any 3rd party library and fully programmatic approach.

The view for the tabs (TabsView) will be a UICollectionView, and each cell (TabCell) has an UIImageView for the icon (optional) and an UILabel for the title.

<div>
    <img src="https://user-images.githubusercontent.com/50784573/110238745-99f13880-7f86-11eb-9400-caae02dcb795.gif" height=400/>
    <img src="https://user-images.githubusercontent.com/50784573/110238787-e63c7880-7f86-11eb-978e-e7c473f2c0a5.png" height=400/>
</div>

## Code

Refresh CollectionView Layout when you rotate the device

```swift
//ViewController.swift
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    tabsView.collectionView.collectionViewLayout.invalidateLayout()
}
```

Set TabMode to '.fixed' for stretched tabs in full width of screen or '.scrollable' for scrolling to see all tabs

```swift
//ViewController.swift
private func setupTabs() {
    tabsView.tabs = [
        Tab(icon: UIImage(systemName: "music.note.list"), title: "Music"),
        Tab(icon: UIImage(systemName: "film"), title: "Movie"),
        Tab(icon: UIImage(systemName: "book"), title: "Book")
    ]

    tabsView.tabMode = .fixed
    tabsView.titleColor = .black
    tabsView.iconColor = .black
    tabsView.indicatorColor = .black
    tabsView.titleFont = UIFont.systemFont(ofSize: 20, weight: .semibold)
    tabsView.collectionView.backgroundColor = .white
    
    tabsView.delegate = self
    
    tabsView.collectionView.selectItem(at: IndexPath(item: 0, section: 0), animated: true, scrollPosition: .centeredVertically)
    
}
```

Select transitionStyle to '.scroll' for cool animation and set the selected ViewController in the PageViewController when the app starts.

```swift
private func setupPageViewController() {
    // PageViewController
    self.pageController = TabsPageViewController(transitionStyle: .scroll, navigationOrientation: .horizontal, options: nil)
    
    self.addChild(self.pageController)
    self.view.addSubview(self.pageController.view)
    
    // Set PageViewController Delegate & DataSource
    pageController.delegate = self
    pageController.dataSource = self
    
    // Set the selected ViewController in the PageViewController when the app starts
    pageController.setViewControllers([showViewController(0)!], direction: .forward, animated: true, completion: nil)
    
    // PageViewController Constraints
    self.pageController.view.translatesAutoresizingMaskIntoConstraints = false
    NSLayoutConstraint.activate([
        self.pageController.view.topAnchor.constraint(equalTo: self.tabsView.bottomAnchor),
        self.pageController.view.leadingAnchor.constraint(equalTo: self.view.leadingAnchor),
        self.pageController.view.trailingAnchor.constraint(equalTo: self.view.trailingAnchor),
        self.pageController.view.bottomAnchor.constraint(equalTo: self.view.bottomAnchor)
    ])
    self.pageController.didMove(toParent: self)
}
```

## References

[Custom Tabs Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/custom-tabs)

[[Swift] prepareForReuse()](https://sunggweon.dev/TIL/swift/prepare-for-reuse.html)