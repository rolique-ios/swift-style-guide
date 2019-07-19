# The Official Rolique Swift & iOS Style Guide.

### Defer
When method has multiple exit point and there is logic that must be called before every `return` or you want to emphasize that it's important to call something before exit(`.unclock()`, `.endUpdates()`) don't forget to use `defer` statement. `defer` should be always declared on the top of the function.
**Preferred**:
```swift
extension NSLocking {
    func `do`<Result>(_ action: () -> Result) -> Result {
        self.lock()
        defer { self.unlock() }
        return action()
    }
}

func process() {
    defer { reset() }
    
    if condition1 {
        prepare()
        showScreen1()
        return
    }
    
    if condition2 {
        prepare()
        showScreen2()
        return
    }
}
```

**Not Preferred**:
```swift
func writeToDB() {
    lock.lock()
    // doing smth
    lock.unlock()
}

func process() {
    if condition1 {
        prepare()
        showScreen1()
        reset()
        return
    }
    
    if condition2 {
        prepare()
        showScreen2()
        reset()
        return
    }
}
```
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

### @IBAction
@IBAction method name should point out on `UIControl.Event` which triggers it. 

**Preferred**:
```swift
    self.nextRoundButton.addTarget(self, action: #selector(startNextRoundTouchUpInside(sender:)), for: .touchUpInside)
    @objc func startNextRoundTouchUpInside(sender: UIButton) {}

    @IBAction func nameTextFieldEditingChanged(sender: UITextField) {}
```

### Extensions
All protocols confirmations, private methods, IBActions methods should be moved to separate extensions, if it's possible (In current version of Swift conformance of generic class to `@objc` protocol cannot be in an extension), and divided with `// MARK: - `

**Preferred**:
```swift
// MARK: - UITableViewDelegate
extension ResultViewController: UITableViewDelegate {
    
}

// MARK: - Private
private extension ResultViewController {
    func doSmthSpecific() {}
}

// MARK: - Actions
private extension ResultViewController {
    @IBAction func doneTouchUpInside(sender: UIButton) {}
}
```

### Access modifiers
Always use the lowest access modifier that's possible, class shouldn't expose any implementation details except his interface. Never explicitly use `internal` (everything is internal by default). `private` property should be declared on the very top then `fileprivate` -> `internal` -> `public` -> `open`. Use access control as the leading property specifier. The only things that should come before access control are the `static` specifier or attributes such as `@IBAction`, `@IBOutlet` and `@discardableResult`.

**Preferred**:
```swift
final class A {
    private lazy var counter = 0
    fileprivate lazy var message = ""
    var onSuccess: (() -> Void)?
}
```

## Control Flow
Prefer the `for-in` style of `for` loop over the `while-condition-increment` style.

**Preferred**:
```swift
for _ in 0..<3 {
  print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
  print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
  print(index)
}

for index in (0...3).reversed() {
  print(index)
}
```

**Not Preferred**:
```swift
var i = 0
while i < 3 {
  print("Hello three times")
  i += 1
}


var i = 0
while i < attendeeList.count {
  let person = attendeeList[i]
  print("\(person) is at position #\(i)")
  i += 1
}
```

### Minimal Imports
Import only the modules a source file requires. For example, don't import `UIKit` when importing `Foundation` will suffice. Likewise, don't import `Foundation` if you must import `UIKit`.

**Preferred**:
```
import UIKit
var view: UIView
var deviceModels: [String]
```

**Preferred**:
```
import Foundation
var deviceModels: [String]
```

**Not Preferred**:
```
import UIKit
import Foundation
var view: UIView
var deviceModels: [String]
```

**Not Preferred**:
```
import UIKit
var deviceModels: [String]
```
