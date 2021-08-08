# [HowTo] CoreLocation Basic Setting

The location manager calls its delegate’s methods to report location-related events to your app. Implement this protocol in an app-specific object and use the methods to update the app. For example, you might use the current location to update the user’s position on a map or you might return search results relevant only to the user’s current location.

```swift
//ViewController

import CoreLocation

...

//MARK: - Properties
private lazy var locationManager: CLLocationManager = {
    let manager = CLLocationManager()
    manager.delegate = self
    return manager
}()

...

//MARK: - Selectors
@objc func locationTapped() {
    switch CLLocationManager.authorizationStatus() {
    case .authorizedAlways, .authorizedWhenInUse:
        locationManager.requestLocation()
    case .notDetermined:
        locationManager.requestWhenInUseAuthorization()
    default:
        promptForLocationPermission()
    }
}

//MARK: - Helpers
private func promptForLocationPermission() {
    let alertController = UIAlertController(title: "Requires Location Permission", message: "Would you like to enable location permission in Settings?", preferredStyle: .alert)
    let enableAction = UIAlertAction(title: "Go to Settings", style: .default) { _ in
        guard let settingsURL = URL(string: UIApplication.openSettingsURLString) else { return }
        UIApplication.shared.open(settingsURL, options: [:], completionHandler: nil)
    }
    let cancelAction = UIAlertAction(title: "Cancel", style: .cancel, handler: nil)
    alertController.addAction(enableAction)
    alertController.addAction(cancelAction)
    self.present(alertController, animated: true, completion: nil)
}

private func fetchWeather(byLocation location: CLLocation) {
    let lat = location.coordinate.latitude
    let lon = location.coordinate.longitude
    weatherManager.fetchWeather(lat: lat, lon: lon) { [weak self] result in
        guard let self = self else { return }
        self.handleResult(result)
    }
}

private func handleResult(_ result: Result<WeatherModel, Error>) {
    switch result {
    case .success(let model):
        updateView(with: model)
    case .failure(let error):
        print(error.localizedDescription)
    }
}

...

//MARK: - CLLocationManagerDelegate
extension ViewController: CLLocationManagerDelegate {
    
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        if let location = locations.last {
            manager.stopUpdatingLocation()
            fetchWeather(byLocation: location)
        }
    }
    
    func locationManager(_ manager: CLLocationManager, didFailWithError error: Error) {
        print(error.localizedDescription)
    }
}
```

### Info.plist

```
Privacy - Location When In Use Usage Description
```

<img src="https://user-images.githubusercontent.com/50784573/109624130-2fed2380-7b81-11eb-83ee-c0af233c1abd.png"/>

## References

[Apple Developer Document](https://developer.apple.com/documentation/corelocation)

[Alamofire Project Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/AFWeather)