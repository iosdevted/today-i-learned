# [HowTo] UITableView Fully Programmatic Approach

```swift
var window: UIWindow?

func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        
    guard let scene = scene as? UIWindowScene else { return }
    window = UIWindow(windowScene: scene)
    window?.rootViewController = //Controller()
    window?.makeKeyAndVisible()
}

    //navigation controller
    guard let scene = (scene as? UIWindowScene) else { return }
    window = UIWindow(windowScene: scene)
    let nav = UINavigationController(rootViewController: //Controller())
    window?.rootViewController = nav
    window?.makeKeyAndVisible()
```

## MainViewController + Tableview inside + Custom Cell

<img src="https://user-images.githubusercontent.com/50784573/108624942-6fce4f80-748b-11eb-8af6-4866ca8eae83.png" width=250/>

```swift
import UIKit

private let ReuseIdentifier: String = "CellReuseIdentifier"

class NewsViewController: UIViewController {
    
    // MARK: - Properties
    
    private let tableView = UITableView()
    
    private let data = [
        ["Apple", "MacOS", "iOS", "IpadOS"],
        ["one", "two", "three", "four"]
    ]
    
    // MARK: - Life Cycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupTableView()
        
        tableView.delegate = self
        tableView.dataSource = self
        
        //self.navigationController?.navigationBar.prefersLargeTitles = true
        self.tableView.register(Cell.self, forCellReuseIdentifier: ReuseIdentifier)
        
        let header = UIView(frame: CGRect(x: 0, y: 0, width: view.frame.size.width, height: 150))
        let footer = UIView(frame: CGRect(x: 0, y: 0, width: view.frame.size.width, height: 150))
        
        header.backgroundColor = .systemOrange
        footer.backgroundColor = .systemTeal
        
        let headerLabel = UILabel(frame: header.bounds)
        headerLabel.text = "This is tableView Header"
        headerLabel.textAlignment = .center
        header.addSubview(headerLabel)
        
        let footerLabel = UILabel(frame: footer.bounds)
        footerLabel.text = "This is tableView Footer"
        headerLabel.textAlignment = .center
        footer.addSubview(footerLabel)
        
        tableView.tableHeaderView = header
        tableView.tableFooterView = footer
    }
    
    // MARK: - Helpers

    func setupTableView() {
      view.addSubview(tableView)
      tableView.translatesAutoresizingMaskIntoConstraints = false
      tableView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
      tableView.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
      tableView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
      tableView.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true
    }
}

//MARK: - UITableViewDelegate/DataSource

extension NewsViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        tableView.deselectRow(at: indexPath, animated: true)
    }
}

extension NewsViewController: UITableViewDataSource {

    func tableView(_ tableView: UITableView, heightForHeaderInSection section: Int) -> CGFloat {
        return 20.0
    }
    
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        return "This is Section Header"
    }
    
    func tableView(_ tableView: UITableView, heightForFooterInSection section: Int) -> CGFloat {
        return 30.0
    }
    
    func tableView(_ tableView: UITableView, titleForFooterInSection section: Int) -> String? {
        return "This is Section Footer"
    }
    func numberOfSections(in tableView: UITableView) -> Int {
        return data.count
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data[section].count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: ReuseIdentifier, for: indexPath) as! Cell
        
        cell.textLabel?.text = data[indexPath.section][indexPath.row]
        cell.backgroundColor = .systemBlue
        
        return cell
    }
}
```

```swift
import UIKit

class Cell: UITableViewCell {
    
    // MARK: - Lifecycle
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

## Basic TableView

<img src="https://user-images.githubusercontent.com/50784573/108625695-97bfb200-748f-11eb-8d33-4ec72f372f5b.png" width=250/>

```swift
import UIKit

private let ReuseIdentifier: String = "CellReuseIdentifier"

class NewsViewController: UIViewController {
    
    // MARK: - Properties
    
    private let tableView = UITableView()
    private let data: [String] = ["Apple", "MacOS", "iOS", "IpadOS"]

    // MARK: - Life Cycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.navigationController?.navigationBar.prefersLargeTitles = true
        self.navigationController?.navigationBar.topItem?.title = "TableView"

        setupTableView()
        self.tableView.dataSource = self
        self.tableView.delegate = self
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: ReuseIdentifier)
        
    }
    
    // MARK: - Helpers
    
    func setupTableView() {
      view.addSubview(tableView)
      tableView.translatesAutoresizingMaskIntoConstraints = false
      tableView.topAnchor.constraint(equalTo: view.topAnchor).isActive = true
      tableView.leftAnchor.constraint(equalTo: view.leftAnchor).isActive = true
      tableView.bottomAnchor.constraint(equalTo: view.bottomAnchor).isActive = true
      tableView.rightAnchor.constraint(equalTo: view.rightAnchor).isActive = true
    }
}

//MARK: - UITableViewDelegate/DataSource

extension NewsViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        tableView.deselectRow(at: indexPath, animated: true)
    }
}

extension NewsViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: ReuseIdentifier, for: indexPath)
        cell.textLabel?.text = data[indexPath.row]
        
        return cell
    }
}
```

<img src="https://user-images.githubusercontent.com/50784573/108712515-387aa400-755a-11eb-9b89-09c019ff3cb8.png" width=250/>

## Custom UITableViewCell

```swift
import UIKit

class ArticleTableViewCell: UITableViewCell {
    
    var titleLabel: UILabel = {
        let label = UILabel()
        label.font = UIFont.systemFont(ofSize: 14)
        return label
    }()
    
    var descriptionLabel: UILabel = {
        let label = UILabel()
        label.font = UIFont.systemFont(ofSize: 12)
        label.textColor = .lightGray
        return label
    }()
    
    // MARK: - Lifecycle
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        
        selectionStyle = .none
                
        let stack = UIStackView(arrangedSubviews: [titleLabel, descriptionLabel])
        stack.axis = .vertical
        stack.distribution = .fillEqually
        stack.spacing = 4
        
        addSubview(stack)
        stack.translatesAutoresizingMaskIntoConstraints = false
        stack.topAnchor.constraint(equalTo: topAnchor, constant: 5).isActive = true
        stack.leftAnchor.constraint(equalTo: leftAnchor, constant: 20).isActive = true
        stack.bottomAnchor.constraint(equalTo: bottomAnchor, constant: -5).isActive = true
        stack.rightAnchor.constraint(equalTo: rightAnchor).isActive = true
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

## Custom Cell Line height, numberofLines

```swift
//properties 
private let tableView = UITableView()

//viewDidLoad
self.tableView.rowHeight = UITableView.automaticDimension

//private func allowMultipleLines(tableViewCell: UITableViewCell) {
//    tableViewCell.textLabel?.numberOfLines = 0
//    tableViewCell.textLabel?.lineBreakMode = .byWordWrapping
//}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: ReuseIdentifier, for: indexPath) as! ArticleTableViewCell
    cell.descriptionLabel.text = data[indexPath.row]
    cell.titleLabel.text = data[indexPath.row]
    cell.descriptionLabel.numberOfLines = 0
    cell.descriptionLabel.lineBreakMode = .byWordWrapping
    return cell
    }
```

## References

[Apple Developer Document](https://developer.apple.com/documentation/uikit/uitableview)
