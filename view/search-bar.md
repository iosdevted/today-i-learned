# [HowTo] Search Bar Fully Programmatic Approach

Search Bar without using any 3rd party library and fully programmatic approach

<div>
    <img src="https://user-images.githubusercontent.com/50784573/110197658-7ba91100-7e90-11eb-8caf-c56d2138e6ea.png" height=400/>
    <img src="https://user-images.githubusercontent.com/50784573/110197660-7d72d480-7e90-11eb-9b1f-2662c2029305.png" height=400/>
</div>

## Code

```swift 
//  ViewController.swift

import UIKit

class ViewController: UIViewController {
    
    //MARK: - Properties
    
    private let searchBar = UISearchBar()
    
    //MARK: - Life Cycle

    override func viewDidLoad() {
        super.viewDidLoad()
        configureUI()
    }
    
    //MARk: - Selectors
    
    @objc func handleShowSearhBar() {
        search(shouldShow: true)
        searchBar.becomeFirstResponder()
    }
    
    //MARk: - Helpers
    
    private func configureUI() {
        view.backgroundColor = .white
        
        navigationController?.navigationBar.prefersLargeTitles = true
        navigationController?.navigationBar.tintColor = .black
        navigationItem.title = "Search Bar"
        
        showSearchBarButton(shouldShow: true)
        
        searchBar.sizeToFit()
        searchBar.delegate = self
    }
    
    private func search(shouldShow: Bool) {
        showSearchBarButton(shouldShow: !shouldShow)
        searchBar.showsCancelButton = shouldShow
        navigationItem.titleView = shouldShow ? searchBar : nil
    }
    
    private func showSearchBarButton(shouldShow: Bool) {
        if shouldShow {
            navigationItem.rightBarButtonItem = UIBarButtonItem(barButtonSystemItem: .search, target: self, action: #selector(handleShowSearhBar))
        } else {
            navigationItem.rightBarButtonItem = nil
        }
    }
}

//MARK: - UISearchBarDelegate

extension ViewController: UISearchBarDelegate {
    
    func searchBarTextDidBeginEditing(_ searchBar: UISearchBar) {
        print("Search bar did begin editing")
    }
    
    func searchBarTextDidEndEditing(_ searchBar: UISearchBar) {
        print("Search bar did end editing")
    }
    
    func searchBarCancelButtonClicked(_ searchBar: UISearchBar) {
        search(shouldShow: false)
    }
    
    func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
        print("Search text is \(searchText)")
    }
}

```

## References

[Search Bar Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/search-bar)