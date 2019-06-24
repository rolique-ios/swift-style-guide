# The Official Rolique Swift & iOS Style Guide.

### Localization
For localization we using separate file which contains all strings which are grouped by screens. Strings should be set only from code.

**Preferred**:
```swift
struct Localization {
    struct General {
        static var save: String { return "General.save".localized }
        static var close: String { return "General.close".localized }
    }
    
    struct UserProfile {
        static var tags: String { return "UserProfile.tags".localized }
        static var rating: String { return "UserProfile.rating".localized }
    }
    
    struct UsersList {
        static var search: String { return "UsersList.search".localized }
    }
}
```
All strings should be `lowercased`. Uppercase or First-Letter-Uppercase should be applied only after localization. Here are helpful extension methods:
```swift
extension String {
    var localized: String {
        return NSLocalizedString(self, comment: "localized version of \(self)")
    }
    
    func localizeWithFormat(arguments: CVarArg...) -> String {
        return String(format: self.localized, arguments: arguments)
    }
    
    var capitalizingFirstLetter: String {
        return prefix(1).uppercased() + self.lowercased().dropFirst()
    }
}
```
### Fonts
If app uses custom fonts then they should be moved to separate enum with future access through `convenience init`

**Preferred**:
```swift
enum CustomFont: String {
    case productSansBold = "ProductSans-Bold",
    smartKid = "SmartKid",
    nanumPenScript = "NanumPen",
    productSansRegular = "ProductSans-Regular",
    shadowIntoLightRegular = "ShadowsIntoLightTwo-Regular"
}

extension UIFont {
    convenience init(font: CustomFont, with size: CGFloat) {
        self.init(name: font.rawValue, size: size)!
    }
}
```

### Constants
For constants we using static computed properties over stored static properties to reduce RAM usage. If constants belong only to the distinct class, it should be moved to private `struct`:

**Preferred**:
```swift
private struct Constants {
    static var playerWidth: CGFloat { return 100 }
    static var trophySize: CGSize { return CGSize(width: 16, height: 18) }
}

final class ResultViewController: UIViewController {
}
```
Otherwise, if it belongs to more than 1 class it should be moved to `GlobalConsntants`.

**Preferred**:
```swift
struct GlobalConstants {
    struct Game {
        static var maxPlayersCount: Int { return 12 }
    }
}
```
