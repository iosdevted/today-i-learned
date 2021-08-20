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
weak var delegate: ViewControllerDelegate?

...

//MARK: - Helpers
private func handleSearchSuccess(model: WeatherModel) {
    //statusLabel.isHidden = false
    //statusLabel.textColor = UIColor(red: 98/255, green: 149/255, blue: 156/255, alpha: 1/1)
    //statusLabel.text = "Success!"
    delegate?.didUpdateWeatherFromSearch(model: model)
}
```

위의 경우 같은 경우

```swift
// 한 쪽에서는 인스턴스 생성 
let nav = AddCityViewController()

// 한 쪽에서는 weak var delegate로 delegate의 하위로 만들어주기
weak var delegate: ViewControllerDelegate?

// 그리고 처음 한 곳에서 바인딩 해주기
nav.delegate = self
```

다른 방법으로는

```swift
//ViewController 에서 presenter를 만들어 줄 경우 
let presenter = Presenter()

override func viewDidLoad() {
    super.viewDidLoad()
    //presenter에서 마든 setView method를 이용해 view와 연결해 준다.
    presenter.setView(colorButtonView: self)
}

//presenter 

weak var view: MainView? //MainViewProtocol

func setView(mainView: MainView?){
    self.view = mainView
}
```

VIPER 구조에서는

```swift
//SceneDelegate
guard let scene = (scene as? UIWindowScene) else { return }

let listRouter = ListRouter()

window = UIWindow(frame: UIScreen.main.bounds)
window?.windowScene = scene
window?.rootViewController = listRouter.viewController
window?.makeKeyAndVisible()


//ListRouter
class ListRouter: Router {
    var viewController: UIViewController {
        let view = ListViewController()
        let interactor = ListInteractor(coreDataManager: coreDataManager)
        let presenter = ListPresenter(interactor: interactor, router: self)
        let navigationController = UINavigationController(rootViewController: view)
        
        presenter.view = view
        view.presenter = presenter
        interactor.output = presenter
        
        return navigationController
    }
    
    func pushAddTodoListViewController(from view: UIViewController?, animated: Bool) {
        let addTodoRouter = AddTodoRouter()

        view?.navigationController?.pushViewController(addTodoRouter.viewController, animated: animated)
    }
}
```

이렇게 router를 이용하여 미리 다 묶어버릴 수 있다.

## References

[Protocol Example Code Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/AFWeather)