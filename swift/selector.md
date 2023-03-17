### Selector

<aside>
💡 속성에 접근하거나 메서드를 호출하는 것이 아니라, 가리키는 역할만 한다.
코드 영역의 메모리 주소를 가리켜서, 연결시키는 기술이다.

</aside>

- 클로저와 유사하다.
    - 다만, 호출은 할 수 없고, 특정 이벤트를 전달해주는 역할이다.
- 메서드 주소를 통해 메서드를 가리킨다.
    - 매개변수에 메서드의 이름이 들어간다. (주소)
    - 내부적으로 Objective-C로 구현되어있다.
- 구조체 타입이므로 가리킬 수만 있고, 실행은 불가능하다.

### 예시

```swift
class Dog {
    var num = 1.0

    @objc var doubleNum: Double {
        get {
            return num * 2.0
        }
        set {
            num = newValue / 2.0
        }
    }

    @objc func run() {
        print("달리다")
    }
}

// (계산)속성을 가르킬때
let eyesSelector = #selector(**getter**: Dog.doubleNum)    // 계산(읽기) 속성
let nameSelector = #selector(**setter**: Dog.doubleNum)    // 계산(쓰기) 속성

// 메서드를 가르킬때
let runSelector = #selector(Dog.run) // 흔하지 않은 경우
```

- 클래스, Objective-C 프로토콜에 포함된 멤버에만 적용이 가능하다. (구조체에서 적용 불가능)
- 내부적으로 Objective C 프레임워크를 사용하고 있기 때문에 @objc 키워드를 사용해야한다.

### 예시

```swift
class ViewController: UIViewController {
    
    lazy var myButton: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("MyButton", for: .normal)
        button.frame = CGRect(x: 0.0, y: self.view.frame.height - 150, width: view.frame.width, height: 50.0)
        return button
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.addSubview(myButton)
        myButton.addTarget(self, action: **#selector(ViewController.doSomething(_:))**, for: .touchUpInside)
                // myButton.addTarget(self, action: #selector(doSomething), for: .touchUpInside)
                    // self를 앞에서 사용했기 때문에 ViewController는 생략할 수 있다.
    }
    
    // 셀렉터는 오브젝티브C에서 인식할 수 있는 속성/메서드만 가르킬 수 있음
    **@objc** func doSomething(_ sender: Any) {

    }
    
}
```

- `addTarget`
    - 코드로 버튼을 만들었을 때, 어떤 함수를 실행시킬지 결정해야햔다.
    - 즉, 버튼을 눌렀을 때, 어떤 인스턴스를 실행시킬지 적어줄 때 사용한다. 이때 매개변수에 selector를 통해 수행힌다.
