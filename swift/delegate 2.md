# Extended Meaning of the Delegate Pattern

지금까지 델리게이트를 위임의 기능으로서만 생각했던 나 자신에게 충격을 준 블로그 포스트.

글쓴이에 따르면 델리게이트는 위의자의 의미가 아닌 어떤 객체를 해야 하는 일을 부분적으로 확장해서 대신 처리를 한다고 정의를 내렸다.

예시와 코드를 보고 지금까지 델리게이트를 제대로 알지 못했다는 것을 알게 되었다.

예시와 코드는 다음과 같다.

```
자! 고객이 여러곳에서 사용할수 있는 메시지창을 하나 만들어 달라고 합니다.
메시지창에는 버튼이 있고 누르면 뷰컨트롤러는 무슨 버튼을 눌렀는지 체크 해서
그 버튼이 이벤트가 발생하면 해야하는 일들을 처리 해야 한다고 합니다.
```

```swift
//
//  MessageBox.swift
//  delegate
//
//  Created by 황병국 on 05/07/2017.
//  Copyright (c) 2017 magi. All rights reserved.
//
import UIKit

protocol MessageBoxDelegate: class {
  func touchButton()
}

class MessageBox: UIView {

  weak var delegate: MessageBoxDelegate?
  var button: UIButton?

  public override init(frame: CGRect) {
    super.init(frame: frame)

    configure()
  }

  public required init?(coder aDecoder: NSCoder) {
    super.init(coder: aDecoder)

    configure()
  }

  func configure() {
    button = UIButton(type: .system)
    if let btn = button {
      btn.setTitle("SEND", for: .normal)
      btn.sizeToFit()
      btn.frame.origin = CGPoint(x: (self.bounds.width - btn.bounds.width) * 0.5,
                                 y: (self.bounds.height - btn.bounds.height) * 0.5)
      btn.addTarget(self, action: #selector(tapButton), for: .touchUpInside)
      self.addSubview(btn)
    }
  }

  func tapButton() {
    delegate?.touchButton()
  }
}
```

나는 데이터간의 이동을 중점으로 델리게이트를 사용했다면 이 분은 버튼의 구현을 델리게이트를 통해 다 구현했음을 볼 수 있다.


```swift
//
//  ViewController.swift
//  delegate
//
//  Created by 황병국 on 05/07/2017.
//  Copyright (c) 2017 magi. All rights reserved.
//
import UIKit


class ViewController: UIViewController {

  var messageBox: MessageBox?

  override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
    messageBox = MessageBox(frame: CGRect(origin: .zero, size: CGSize(width: 300, height: 200)))
    if let msg = messageBox {
      msg.frame.origin = CGPoint(x: (UIScreen.main.bounds.width - msg.bounds.width) * 0.5,
                                 y: (UIScreen.main.bounds.height - msg.bounds.height) * 0.5)

      msg.backgroundColor = .lightGray
      msg.delegate = self
      self.view.addSubview(msg)
    }
  }
}

extension ViewController: MessageBoxDelegate {
    func touchButton() {
    print("touchButton")
  }
}
```

그리고 각 뷰컨트럴에 

```swift 
msg.delegate = self
```

```swift 
extension ViewController: MessageBoxDelegate {
    func touchButton() {
    print("touchButton")
  }
}
```

를 통해 간편히 추가하고 구현하였다.

## References

[iOS Delegate 패턴에 대해서 알아보기](https://magi82.github.io/ios-delegate/)