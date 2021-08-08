# [HowTo] Protocol; Data from subview to superview

To pass the data to another ViewController without using CompletionHandler.

## Superview

```swift
//Superview
protocol ViewControllerDelegate {
    func didUpdateWeatherFromSearch(model: WeatherModel)
}

...

//MARK: - Selectors
@objc func addTapped() {
    let nav = AddCityViewController()
    nav.modalPresentationStyle = .overFullScreen
    nav.delegate = self
    self.present(nav, animated: false, completion: nil)
}

...

//MARK: - ViewControllerDelegate

extension ViewController: ViewControllerDelegate {
    func didUpdateWeatherFromSearch(model: WeatherModel) {
        presentedViewController?.dismiss(animated: false, completion: { [weak self] in
        guard let self = self else { return }
        self.updateView(with: model)
        })
    }
}
```

## Subview

```swift
//Subview
//MARK: - Properties
var delegate: ViewControllerDelegate?

...

//MARK: - Helpers
private func handleSearchSuccess(model: WeatherModel) {
    //statusLabel.isHidden = false
    //statusLabel.textColor = UIColor(red: 98/255, green: 149/255, blue: 156/255, alpha: 1/1)
    //statusLabel.text = "Success!"
    delegate?.didUpdateWeatherFromSearch(model: model)
}
```

## References

[Protocol Example Code Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/AFWeather)