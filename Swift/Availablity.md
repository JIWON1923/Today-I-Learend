<aside>
π‘ νΉμ  Swift λ²μ  λλ νΉμ  νλ«νΌ, μ΄μμ²΄μ  λ²μ κ³Ό κ΄λ ¨λ λΌμ΄νμ¬μ΄ν΄μ λνλΈλ€.

</aside>

### @available

- νμ, μμ±, λ©μλ μμ μ μΈ
- iOS λ²μ  x μ΄μμ μ μ©νκ³ μν  λ μ¬μ©νλ€.
- **μ»΄νμΌλ¬**κ° APIμ μ¬μ©κ°λ₯μ±μ κ²°μ 

### #available

- μ‘°κ±΄λ¬Έμμ μ¬μ©νλ€. (if, guard, while)
- **λ°νμ**μ APIμ μ¬μ© κ°λ₯μ±μ κ²°μ νλ€

### [λ§€κ°λ³μ](https://bbiguduk.gitbook.io/swift/language-reference/attributes)

1. νλ«νΌ μ΄λ¦
    
    `iOS`  `iOSApplicationExtension` `macOS` `macOSApplicationExtension` `macCatalyst` `macCatalystApplicationExtension` `watchOS`  `watchOSApplicationExtension` `tvOS` `tvOSApplicationExtension` `swift` 
    
2. κ°μ©μ±
    
    `*`
    
3. μΆκ°μ λ³΄
    
    `unavailable` : μ§μ λ νλ«νΌμμ λΆκ°λ₯νλ€. (`Swift`μμλ μ¬μ©ν  μ μλ€)
    
    `introduced` : μ μΈμ΄ λμλ νλ«νΌμ΄λ μΈμ΄μ μ²« λ²μ§Έ λ²μ μ μλ―Ένλ€.
    
    `deprecated` : μ μΈμ΄ μ¬μ©λμ§ μμ μ§μ λ νλ«νΌμ΄λ μΈμ΄μ μ²« λ²μ§Έ λ²μ 
    
    λ±λ±.. νμν  λ κ³΅μ λ¬Έμλ₯Ό μ°Έκ³ ν΄μ μμ±ν΄λ³΄μ!
    

### μμ 

```swift
@available(iOS 11.0, *)
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        
    }
    
    @available(iOS 11.0, *)
    func doSomething() {
        // (if, while, guardλ¬ΈμΌλ‘λ μ¬μ© κ°λ₯)
        if #available(iOS 11.0, *) {
            // iOS 11λ²μ μ΄μμΈ κ²½μ° μ μ© μν¬ μ½λλ₯Ό μμ±
            
        } else {
            // iOS 11λ²μ λ―Έλ§μΈ κ²½μ° μ μ© μν¬ μ½λλ₯Ό μμ±
        }
        
    }

}
```

- ν΄λΉ ν΄λμ€λ iOS 11 μ΄μμμλ§ ν΄λΉ ν΄λμ€λ₯Ό μ½μ μ μλ€.
- doSomethingμ iOS 11 μ΄μμμλ§ μ‘΄μ¬νλ μ½λμ΄λ€.
    - iOS 11 μ΄μμμλ§ ν΄λΉ μ‘°κ±΄λ¬Έμ΄ μ€νλλ€. (νμ¬ λ‘μ§μΌλ‘λ μ ν©νμ§λ μλ€.)

```swift
// First Release
protocol MyProtocol {
    // definition
}

protocol MyRenamedProtocol {
    // definition
}

@available(*, unavailable, renamed: "MyRenamedProtocol")
typealias MyProtocol = MyRenamedProtocol
```
