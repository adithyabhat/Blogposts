Below is an UIView extension which can be used to add masking over a view instance. Here I have made use of the CAShapeLayer; set a path to it for masking.

```
import Foundation
import UIKit

extension UIView {
    
    func mask(rect: CGRect, inverse: Bool, isRounded rounded: Bool) {
        let path = UIBezierPath(roundedRect: rect,
                                cornerRadius: rounded ? min(rect.width, rect.height) / 5.0 : 0)
        let maskLayer = CAShapeLayer()
        
        if inverse {
            path.append(UIBezierPath(rect: self.bounds))
            maskLayer.fillRule = kCAFillRuleEvenOdd
        }
        
        maskLayer.path = path.cgPath
        
        self.layer.mask = maskLayer
    }
    
    func clearMask() {
        guard let subLayers = layer.sublayers else {
            return
        }
        for layer in subLayers {
            layer.removeFromSuperlayer()
        }
    }
    

```

There are 2 variants in masking; you can pass the inverse property value to get this.

You can test this implementation in the [Github Project](https://github.com/adithyabhat/ShapelayerInverseMaskDemo)
