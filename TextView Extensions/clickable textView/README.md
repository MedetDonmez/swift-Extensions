
# Clickable Text View

An extension for Text View to make part of it clickable.
## Example Code
```swift


textView.setTappableText(text1: "Clickable Part", text2: " not clickable part", font1: UIFont.italicSystemFont(ofSize: 18), font2: UIFont.boldSystemFont(ofSize: 12), color1: .red, color2: .blue) {
    //add any function here
    print("text1 tapped")
        }
```

## Output

<img src="https://github.com/MedetDonmez/swift-Extensions/assets/76966943/83d77046-031b-4db4-9c6b-7890327a21df">

## Extension Code

```swift
import UIKit
extension UITextView: UITextViewDelegate {
  func setTappableText(text1: String, fullText: String, font1: UIFont, font2: UIFont, color1: UIColor, color2: UIColor, onTap: @escaping () -> Void) {
    // Create the normal attributes for fullText
    let normalAttributes: [NSAttributedString.Key: Any] = [
      .font: font2,
      .foregroundColor: color2
    ]
    // Create the link attributes for text1
    let linkAttributes: [NSAttributedString.Key: Any] = [
      .font: font1,
      .foregroundColor: color1,
      .underlineStyle: NSUnderlineStyle.single.rawValue
    ]
    // Combine fullText into a single attributed string
    let attributedString = NSMutableAttributedString(string: fullText, attributes: normalAttributes)
    // Search for text1 in fullText
    let ranges = self.ranges(of: text1, in: fullText)
    // Apply the link attributes to each instance of text1
    for range in ranges {
      attributedString.addAttributes(linkAttributes, range: range)
      attributedString.addAttribute(.link, value: "tappedText1://", range: range)
    }
    // Set the attributed text
    self.attributedText = attributedString
    self.linkTextAttributes = [
      .foregroundColor: color1,
    ]
    // Enable user interaction
    self.isUserInteractionEnabled = true
    // Save the onTap closure
    self.onTap = onTap
    // Set the delegate to self
    self.delegate = self
  }
  private func ranges(of substring: String, in string: String) -> [NSRange] {
    var ranges = [NSRange]()
    var range = (string as NSString).range(of: substring)
    while range.location != NSNotFound {
      ranges.append(range)
      if NSMaxRange(range) + 1 < string.count {
        let newStartLocation = NSMaxRange(range)
        let substringRange = NSRange(location: newStartLocation, length: string.count - newStartLocation)
        range = (string as NSString).range(of: substring, options: [], range: substringRange)
      } else {
        break
      }
    }
    return ranges
  }
  private struct AssociatedKeys {
    static var onTap = "onTap"
  }
  private var onTap: (() -> Void)? {
    get {
      return objc_getAssociatedObject(self, &AssociatedKeys.onTap) as? () -> Void
    }
    set {
      objc_setAssociatedObject(self, &AssociatedKeys.onTap, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
    }
  }
  public func textView(_ textView: UITextView, shouldInteractWith URL: URL, in characterRange: NSRange, interaction: UITextItemInteraction) -> Bool {
    if URL.scheme == "tappedText1" {
      self.onTap?()
      return false
    }
    return true
  }
}
```




