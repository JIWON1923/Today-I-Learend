### Selector

<aside>
π‘ μμ±μ μ κ·Όνκ±°λ λ©μλλ₯Ό νΈμΆνλ κ²μ΄ μλλΌ, κ°λ¦¬ν€λ μ­ν λ§ νλ€.
μ½λ μμ­μ λ©λͺ¨λ¦¬ μ£Όμλ₯Ό κ°λ¦¬μΌμ, μ°κ²°μν€λ κΈ°μ μ΄λ€.

</aside>

- ν΄λ‘μ μ μ μ¬νλ€.
    - λ€λ§, νΈμΆμ ν  μ μκ³ , νΉμ  μ΄λ²€νΈλ₯Ό μ λ¬ν΄μ£Όλ μ­ν μ΄λ€.
- λ©μλ μ£Όμλ₯Ό ν΅ν΄ λ©μλλ₯Ό κ°λ¦¬ν¨λ€.
    - λ§€κ°λ³μμ λ©μλμ μ΄λ¦μ΄ λ€μ΄κ°λ€. (μ£Όμ)
    - λ΄λΆμ μΌλ‘ Objective-Cλ‘ κ΅¬νλμ΄μλ€.
- κ΅¬μ‘°μ²΄ νμμ΄λ―λ‘ κ°λ¦¬ν¬ μλ§ μκ³ , μ€νμ λΆκ°λ₯νλ€.

### μμ

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
        print("λ¬λ¦¬λ€")
    }
}

// (κ³μ°)μμ±μ κ°λ₯΄ν¬λ
let eyesSelector = #selector(**getter**: Dog.doubleNum)    // κ³μ°(μ½κΈ°) μμ±
let nameSelector = #selector(**setter**: Dog.doubleNum)    // κ³μ°(μ°κΈ°) μμ±

// λ©μλλ₯Ό κ°λ₯΄ν¬λ
let runSelector = #selector(Dog.run) // ννμ§ μμ κ²½μ°
```

- ν΄λμ€, Objective-C νλ‘ν μ½μ ν¬ν¨λ λ©€λ²μλ§ μ μ©μ΄ κ°λ₯νλ€. (κ΅¬μ‘°μ²΄μμ μ μ© λΆκ°λ₯)
- λ΄λΆμ μΌλ‘ Objective C νλ μμν¬λ₯Ό μ¬μ©νκ³  μκΈ° λλ¬Έμ @objc ν€μλλ₯Ό μ¬μ©ν΄μΌνλ€.

### μμ

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
                    // selfλ₯Ό μμμ μ¬μ©νκΈ° λλ¬Έμ ViewControllerλ μλ΅ν  μ μλ€.
    }
    
    // μλ ν°λ μ€λΈμ ν°λΈCμμ μΈμν  μ μλ μμ±/λ©μλλ§ κ°λ₯΄ν¬ μ μμ
    **@objc** func doSomething(_ sender: Any) {

    }
    
}
```

- `addTarget`
    - μ½λλ‘ λ²νΌμ λ§λ€μμ λ, μ΄λ€ ν¨μλ₯Ό μ€νμν¬μ§ κ²°μ ν΄μΌνλ€.
    - μ¦, λ²νΌμ λλ μ λ, μ΄λ€ μΈμ€ν΄μ€λ₯Ό μ€νμν¬μ§ μ μ΄μ€ λ μ¬μ©νλ€. μ΄λ λ§€κ°λ³μμ selectorλ₯Ό ν΅ν΄ μννλ€.
