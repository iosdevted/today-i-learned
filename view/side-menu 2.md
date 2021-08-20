# [HowTo] Side Menu Fully Programmatic Approach

Side menus without using any 3rd party library and fully programmatic approach

<div>
    <img src="https://user-images.githubusercontent.com/50784573/109942301-69f02e00-7d17-11eb-8edd-7a2a44ce02f6.gif" height=400/>
    <img src="https://user-images.githubusercontent.com/50784573/109942328-6fe60f00-7d17-11eb-9953-e9f821ace537.png" height=400/>
    <img src="https://user-images.githubusercontent.com/50784573/109942325-6f4d7880-7d17-11eb-8401-c1b6fdb756f3.png" height=400/>
</div>

## Code

<br/>

1. RootViewController -> ContainerController

2. Move UINavigationController(HomeController) and MenuController to Parent View in ContainerController.

3. Add animation code.

### SceneDelegate

```swift
var window: UIWindow?

func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
    guard let scene = (scene as? UIWindowScene) else { return }
    window = UIWindow(windowScene: scene)
    window?.rootViewController = ContainerController()
    window?.makeKeyAndVisible()
}
```

### HomeController

```swift
//  HomeController.swift

import UIKit

protocol HomeControllerDelegate: class {
    func handleMenuToggle()
}

class HomeController: UIViewController {
    
    //MARK: - Properties
    
    weak var delegate: HomeControllerDelegate?
    
    lazy var menubarButtonItem = UIBarButtonItem(image: UIImage(systemName: "list.dash")?.withRenderingMode(.alwaysOriginal), style: .done, target: self, action: #selector(sideMenuButtonTapped))
    
    //MARK: - Life Cycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        
        configureNavigationBarUI()
    }
    
    //MARk: - Selectors
    
    @objc func sideMenuButtonTapped() {
        delegate?.handleMenuToggle()
    }

    //MARK: - Helpers
    
    private func configureNavigationBarUI() {
        navigationController?.navigationBar.prefersLargeTitles = true
        navigationController?.navigationBar.barTintColor = .white
        navigationItem.setLeftBarButton(menubarButtonItem, animated: true)
        navigationItem.leftBarButtonItem?.tintColor = .white
        navigationController?.navigationBar.topItem?.title = "Main"
    }
}
```

### ContainerController

```swift
//  ContainerController.swift

import UIKit

class ContainerController: UIViewController {
    
    //MARK: - Properties
    
    private let homeController = HomeController()
    private var centerController: UIViewController!
    private var menuController: MenuController!
    private var isExpaned = false
    private let blackView = UIView()
    private lazy var xOrigin = self.view.frame.width - 80
    
    //MARK: - Life Cycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        configure()
    }
    
    override var prefersStatusBarHidden: Bool {
        return isExpaned
    }
    
    override var preferredStatusBarUpdateAnimation: UIStatusBarAnimation {
        return .slide
    }
    
    // MARK: - Selectors
    
    @objc func dismissMenu() {
        isExpaned = false
        animateMenu(shouldExpand: isExpaned)
    }
    
    
    //MARK: - Helpers
    
    func configure() {
        view.backgroundColor = UIColor(red: 25/255, green: 25/255, blue: 25/255, alpha: 1.0)
        configureHomeNavigationController()
        configureMenuController()
    }
    
    func configureHomeNavigationController() {
        centerController = UINavigationController(rootViewController: homeController)
        
        view.addSubview(centerController.view)
        addChild(centerController)
        centerController.didMove(toParent: self)
        
        homeController.delegate = self
    }
    
    func configureMenuController() {
        menuController = MenuController()
        
        view.insertSubview(menuController.view, at: 0)
        addChild(menuController)
        menuController.didMove(toParent: self)
        
        menuController.view.frame = CGRect(x: 0, y: 0, width: xOrigin + 5, height: self.view.frame.height)
        menuController.delegate = self
        
        configureBlackView()
    }
    
    func configureBlackView() {
        blackView.frame = CGRect(x: xOrigin, y: 0, width: xOrigin, height: self.view.frame.height)
        blackView.backgroundColor = UIColor(white: 0, alpha: 0.5)
        blackView.alpha = 0
        view.addSubview(blackView)
        
        let tap = UITapGestureRecognizer(target: self, action: #selector(dismissMenu))
        blackView.addGestureRecognizer(tap)
    }
    
    func animateMenu(shouldExpand: Bool, completion: ((Bool) -> Void)? = nil) {
        if shouldExpand {
            UIView.animate(withDuration: 0.3, delay: 0, usingSpringWithDamping: 0.9, initialSpringVelocity: 0, options: .curveEaseInOut, animations: {
                
                self.centerController.view.frame.origin.x = self.xOrigin
                self.blackView.alpha = 1

            }, completion: nil)
            
        } else {
            self.blackView.alpha = 0
            UIView.animate(withDuration: 0.3, delay: 0, usingSpringWithDamping: 0.9, initialSpringVelocity: 0, options: .curveEaseInOut, animations: {
                self.centerController.view.frame.origin.x = 0
                self.blackView.alpha = 0

            }, completion: completion)
        }
        
        animateStatusBar()
    }
    
    func animateStatusBar() {
        UIView.animate(withDuration: 0.3, delay: 0, usingSpringWithDamping: 0.9, initialSpringVelocity: 0, options: .curveEaseInOut, animations: {
            self.setNeedsStatusBarAppearanceUpdate()
        }, completion: nil)
    }
}

//MARK: - HomeControllerDelegate

extension ContainerController: HomeControllerDelegate {
    func handleMenuToggle() {

        isExpaned.toggle()
        animateMenu(shouldExpand: isExpaned)
    }
}

//MARK: - MenuControllerDelegate

extension ContainerController: MenuControllerDelegate {
    func didSelect(options: MenuOptions) {
        isExpaned.toggle()
        animateMenu(shouldExpand: isExpaned) { _ in
            switch options {
            case .profile:
                print("Show Profile")
            case .settings:
                print("Show Settings")
            case .logout:
                print("Show Logout")
            }
        }
    }
}
```

### MenuController

```swift
//  MenuController.swift

import UIKit

private let reuseIdentifer = "MenuCell"

enum MenuOptions: Int, CaseIterable, CustomStringConvertible {
    case profile
    case settings
    case logout
    
    var description: String {
        switch self {
        case .profile: return "Profile"
        case .settings: return "Settings"
        case .logout: return "Log Out"
        }
    }
    
    var image: UIImage {
        switch self {
        case .profile: return UIImage(systemName: "person.crop.circle") ?? UIImage()
        case .settings: return UIImage(systemName: "lock.circle") ?? UIImage()
        case .logout: return UIImage(systemName: "arrowshape.turn.up.left") ?? UIImage()
        }
    }
}

protocol MenuControllerDelegate: class {
    func didSelect(options: MenuOptions)
}

class MenuController: UITableViewController {
    
    // MARK: - Properties
    
    weak var delegate: MenuControllerDelegate?
    
    private lazy var menuHeader: UIView = {
        let frame = CGRect(x: 0, y: 0, width: self.view.frame.width, height: 50)
        
        let view = UIView(frame: frame)
        return view
    }()
    
    // MARK: - Lifecycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        configureTableView()
    }
    
    // MARK: -  Helpers
    
    func configureTableView() {
        
        tableView.backgroundColor = .white
        tableView.separatorStyle = .none
        tableView.isScrollEnabled = false
        tableView.rowHeight = 60
        tableView.register(MenuOptionCell.self, forCellReuseIdentifier: reuseIdentifer)
        tableView.tableHeaderView = menuHeader
    }
    
}

//MARK: - TableView

extension MenuController {
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return MenuOptions.allCases.count
    }
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: reuseIdentifer, for: indexPath) as! MenuOptionCell
        guard let option = MenuOptions(rawValue: indexPath.row) else { return UITableViewCell() }
        cell.iconImageView.image = option.image
        cell.descriptionLabel.text = option.description
        return cell
    }
    
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        guard let option = MenuOptions(rawValue: indexPath.row) else { return }
        tableView.deselectRow(at: indexPath, animated: true)
        delegate?.didSelect(options: option)
    }
}
```

### MenuOptionCell

```swift
//  MenuOptionCell.swift

import UIKit

class MenuOptionCell: UITableViewCell {
    
    //MARK: - Properties
    
    let iconImageView: UIImageView = {
        let iv = UIImageView()
        iv.contentMode = .scaleAspectFit
        iv.clipsToBounds = true
        iv.tintColor = .black
        return iv
    }()
    
    let descriptionLabel: UILabel = {
        let label = UILabel()
        label.textColor = .black
        label.font = UIFont.systemFont(ofSize: 28)
        return label
    }()
    
    //MARK: - Lifecycle
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        configureUI()

    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    //MARK: - Helpers
    
    private func configureUI() {
        backgroundColor = .white
        
        addSubview(iconImageView)
        iconImageView.translatesAutoresizingMaskIntoConstraints = false
        iconImageView.centerYAnchor.constraint(equalTo: centerYAnchor).isActive = true
        iconImageView.leftAnchor.constraint(equalTo: leftAnchor, constant: 12).isActive = true
        iconImageView.heightAnchor.constraint(equalToConstant: 24).isActive = true
        iconImageView.widthAnchor.constraint(equalToConstant: 24).isActive = true
        
        addSubview(descriptionLabel)
        descriptionLabel.translatesAutoresizingMaskIntoConstraints = false
        descriptionLabel.centerYAnchor.constraint(equalTo: centerYAnchor).isActive = true
        descriptionLabel.leftAnchor.constraint(equalTo: iconImageView.rightAnchor, constant: 12).isActive = true
    }
}

```

## Github Links

[Sophiscated Side Menu Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/custom-side-menu-2)

[Simple Side Menu Github Link](https://github.com/cielgrisdemoscou/ios-project/tree/master/custom-side-menu)
