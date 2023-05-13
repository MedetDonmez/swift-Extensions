
# Clickable Text View

An extension for Text View to make part of it clickable.
## Example Code
```swift


textView.setTappableText(text1: "Clickable Part", text2: " not clickable part", font1: UIFont.italicSystemFont(ofSize: 35), font2: UIFont.boldSystemFont(ofSize: 25), color1: .red, color2: .blue) {
    //add any function here
    print("text1 tapped")
        }
```

## Screenshots

src="https://github.com/MedetDonmez/swift-Extensions/assets/76966943/83d77046-031b-4db4-9c6b-7890327a21df">
m/468x300?text=App+Screenshot+Here)
## Extension Code

```swift
import UIKit

extension UITextView {

    func setTappableText(text1: String, text2: String, font1: UIFont, font2: UIFont, color1: UIColor, color2: UIColor, onTap: (() -> Void)?) {

        // Create the link attributes
        let linkAttributes: [NSAttributedString.Key: Any] = [
            .font: font1,
            .foregroundColor: color1,
            .underlineStyle: NSUnderlineStyle.single.rawValue
        ]
        
        linkTextAttributes = linkAttributes

        // Create the normal attributes for text2
        let normalAttributes: [NSAttributedString.Key: Any] = [
            .font: font2,
            .foregroundColor: color2
        ]

        // Combine text1 and text2 into a single attributed string
        let attributedString = NSMutableAttributedString(string: text1 + text2, attributes: normalAttributes)

        // Apply the link attributes to text1
        let text1Range = (attributedString.string as NSString).range(of: text1)
        attributedString.addAttributes(linkAttributes, range: text1Range)

        // Make text1 tappable
        attributedString.addAttribute(.link, value: "tappedText1", range: text1Range)

        // Set the attributed text
        self.attributedText = attributedString

        // Enable user interaction
        self.isUserInteractionEnabled = true

        // Add tap gesture recognizer
        let tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(self.textViewTapped(_:)))
        self.addGestureRecognizer(tapGestureRecognizer)

        // Save the onTap closure
        self.onTap = onTap
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

    @objc private func textViewTapped(_ sender: UITapGestureRecognizer) {
        guard let onTap = onTap else { return }

        // Get the tapped character index
        let layoutManager = self.layoutManager
        var location = sender.location(in: self)
        location.x -= self.textContainerInset.left
        location.y -= self.textContainerInset.top
        let characterIndex = layoutManager.characterIndex(for: location, in: self.textContainer, fractionOfDistanceBetweenInsertionPoints: nil)

        // Check if the tapped character is in the text1 range
        if let range = (self.attributedText.string as NSString?)?.range(of: self.attributedText.string), range.contains(characterIndex), self.attributedText.attribute(.link, at: characterIndex, effectiveRange: nil) != nil {
            onTap()
        }
    }
}

```




