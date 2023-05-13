
# Color to Image

An extension for setting color to as UIImage.
## Example Code
```swift
imageView.image = UIImage(color: .red)
```
## Extension Code

```swift
import UIKit

extension UIImage {
    convenience init(color: UIColor, size: CGSize = CGSize(width: 1, height: 1)) {
        let rect = CGRect(origin: .zero, size: size)
        UIGraphicsBeginImageContextWithOptions(rect.size, true, 0)
        color.setFill()
        UIRectFill(rect)
        let image = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
        guard let cgImage = image?.cgImage else { fatalError("Failed to create CGImage") }
        self.init(cgImage: cgImage)
    }
}
```

