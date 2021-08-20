# [HowTo] UICollectionView Fully Programmatic Approach

 UICollectionView must be initialized with a non-nil layout parameter. The frame rectangle for the collection view, measured in points. The origin of the frame is relative to the superview in which you plan to add it. This frame is passed to the superclass during initialization.

## UICollectionView for Onboarding Scene.

<div>
    <img src="https://user-images.githubusercontent.com/50784573/110463730-a0fa8100-8115-11eb-9d7e-d4d1fb3e3cb1.gif" height=400/>
</div>


```swift
private let cellId = "collectionCell"

...
    
//MARK: - Properties
    
private var collectionView: UICollectionView!

...

override func viewDidLoad() {
    super.viewDidLoad()

    let layout = UICollectionViewFlowLayout()
    //layout.sectionInset = UIEdgeInsets(top: 0, left: 0, bottom: 0, right: 0)
    //layout.itemSize = CGSize(width: view.frame.width, height: 700)
    layout.scrollDirection = .horizontal
    
    collectionView = UICollectionView(frame: self.view.frame, collectionViewLayout: layout)
    collectionView.isPagingEnabled = true
    
    collectionView.delegate = self
    collectionView.dataSource = self
    
    collectionView.register(CollectionViewCell.self, forCellWithReuseIdentifier: cellId)
    
    view.addSubview(collectionView)
    
    collectionView.snp.makeConstraints { (make) -> Void in
        make.top.equalTo(view.safeAreaLayoutGuide.snp.top).offset(90)
        make.leading.trailing.bottom.equalToSuperview()
    }
}

...

//MARK: - UICollectionViewDelegate, UICollectionViewDataSource

extension OnboardingViewController: UICollectionViewDelegate, UICollectionViewDataSource {
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 5
    }
    
    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellId, for: indexPath) as! CollectionViewCell
        cell.backgroundColor = indexPath.item %  2 == 0 ? .red : .blue
        return cell
    }
    
}

//MARK: - UICollectionViewDelegateFlowLayout

extension OnboardingViewController: UICollectionViewDelegateFlowLayout {
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        return collectionView.frame.size
    }
    
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumLineSpacingForSectionAt section: Int) -> CGFloat {
        return 0
    }
}

```

## References

[Apple Developer Document](https://developer.apple.com/documentation/uikit/uicollectionview)