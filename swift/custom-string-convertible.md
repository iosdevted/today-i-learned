# [Swift] Custom String Convertible

Types that conform to the CustomStringConvertible protocol can provide their own representation to be used when converting an instance to a string. The String(describing:) initializer is the preferred way to convert an instance of any type to a string. `If the passed instance conforms to CustomStringConvertible, the String(describing:) initializer and the print(_:) function use the instance’s custom description property.`

```swift
struct Point {
    let x: Int, y: Int
}

let p = Point(x: 21, y: 30)
print(p)
// Prints "Point(x: 21, y: 30)"

extension Point: CustomStringConvertible {
    var description: String {
        return "(\(x), \(y))"
    }
}

print(p)
// Prints "(21, 30)"
```

## Application

This Protocol is useful when we need to implement the element of UITableViewCell.

```swift
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
}

...

override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: reuseIdentifer, for: indexPath) as! MenuOptionCell
    guard let option = MenuOptions(rawValue: indexPath.row) else { return UITableViewCell() }
    cell.iconImageView.image = option.image
    cell.descriptionLabel.text = option.description
    return cell
}
```

## References

[Apple Developer Document](https://developer.apple.com/documentation/swift/customstringconvertible)