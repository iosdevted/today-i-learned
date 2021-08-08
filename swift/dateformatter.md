# [Swift] DateFormatter

Instances of DateFormatter create string representations of NSDate objects, and convert textual representations of dates and times into NSDate objects. For user-visible representations of dates and times, DateFormatter provides a variety of localized presets and configuration options. For fixed format representations of dates and times, you can specify a custom format string.

```
Wednesday, Sep 12, 2018           --> EEEE, MMM d, yyyy
09/12/2018                        --> MM/dd/yyyy
09-12-2018 14:11                  --> MM-dd-yyyy HH:mm
Sep 12, 2:11 PM                   --> MMM d, h:mm a
September 2018                    --> MMMM yyyy
Sep 12, 2018                      --> MMM d, yyyy
Wed, 12 Sep 2018 14:11:54 +0000   --> E, d MMM yyyy HH:mm:ss Z
2018-09-12T14:11:54+0000          --> yyyy-MM-dd'T'HH:mm:ssZ
12.09.18                          --> dd.MM.yy
10:41:02.112                      --> HH:mm:ss.SSS
```

## Thread Safety

On iOS 7 and later NSDateFormatter is thread safe.

In macOS 10.9 and later NSDateFormatter is thread safe so long as you are using the modern behavior in a 64-bit app.

On earlier versions of the operating system, or when using the legacy formatter behavior or running in 32-bit in macOS, NSDateFormatter is not thread safe, and you therefore must not mutate a date formatter simultaneously from multiple threads.

## Caching

Since both creating and updating DateFormatter are expensive, we create a `static` DateFormatter for every possible style in our app here.

```swift
extension DateFormatter {
    static let mediumDateFormatter: DateFormatter = {
        let df = DateFormatter()
        df.dateStyle = .medium
        df.timeStyle = .none
        
        return df
    }()

    static let mediumTimeFormatter: DateFormatter = {
        let df = DateFormatter()
        df.dateStyle = .none
        df.timeStyle = .medium
        
        return df
    }()
    
    static let mediumDateTimeFormatter: DateFormatter = {
        let df = DateFormatter()
        df.dateStyle = .medium
        df.timeStyle = .medium

        return df
    }()
}

let dateString = DateFormatter.mediumDateTimeFormatter.string(from: Date())
```

There are hidden bonus benefits from this.

- Code consistency and maintainability. This extension will be a centralized place for all of the DateFormatter. So, you can point your teammate to this file when they want to introduce a new style to see whether it already exists or not.
- One point of failure. Date manipulation is not an easy task, and you might find some bugs. With these shared styles, you only need to fix it in one spot.
- Better design decision. If this class keeps growing in styles, it might be a good time to talk with your designers for a chance to clean up your design system.

## Localization

In iOS, most changes in languages and locale preference would cause an app restart. So, our DateFormatter will get re-instantiate with an updated locale, so most of the time, you won't notice an outdated format after languages and locales change. But there is some configuration that doesn't require an app restart, such as a calendar change. In that case, our DateFormatter might not sync with the user preference. To make sure your DateFormatter always up to date with the user's current locale, you need to set `locale` to `autoupdatingCurrent`.

```swift
static let mediumDateFormatter: DateFormatter = {
    let df = DateFormatter()
    df.dateStyle = .medium
    df.timeStyle = .none
    df.locale = Locale.autoupdatingCurrent
    
    return df
}()
```

## Bad Example

When using the API, there is a possibility that the time is set based on UTC. In this case, you need to change it according to the local time.

```swift
extension String {
    func utcToLocal() -> String? {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss'Z'"
        dateFormatter.timeZone = TimeZone(abbreviation: "UTC")
        
        if let date = dateFormatter.date(from: self) {
            dateFormatter.timeZone = TimeZone.current
            dateFormatter.dateFormat = "h:mm a"
        
            return dateFormatter.string(from: date)
        }
        return nil
    }

    func localToUTC(dateStr: String) -> String? {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "h:mm a"
        dateFormatter.calendar = Calendar.current
        dateFormatter.timeZone = TimeZone.current
        
        if let date = dateFormatter.date(from: dateStr) {
            dateFormatter.timeZone = TimeZone(abbreviation: "UTC")
            dateFormatter.dateFormat = "H:mm:ss"
        
            return dateFormatter.string(from: date)
        }
        return nil
    }

    func utcToLocalWithDate() -> String? {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss'Z'"
        dateFormatter.timeZone = TimeZone(abbreviation: "UTC")
        
        if let date = dateFormatter.date(from: self) {
            dateFormatter.timeZone = TimeZone.current
            dateFormatter.dateFormat = "MMM d, h:mm a"
        
            return dateFormatter.string(from: date)
        }
        return nil
    }
}
```

## References

[Apple Developer Document](https://developer.apple.com/documentation/foundation/dateformatter)

[How expensive is DateFormatter](https://sarunw.com/posts/how-expensive-is-dateformatter/)

[How to use DateFormatter in Swift](https://sarunw.com/posts/how-to-use-dateformatter/)