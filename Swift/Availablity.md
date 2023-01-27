<aside>
💡 특정 Swift 버전 또는 특정 플랫폼, 운영체제 버전과 관련된 라이프사이클을 나타낸다.

</aside>

### @available

- 타입, 속성, 메서드 앞에 선언
- iOS 버전 x 이상을 적용하고자할 때 사용한다.
- **컴파일러**가 API의 사용가능성을 결정

### #available

- 조건문에서 사용한다. (if, guard, while)
- **런타임**에 API의 사용 가능성을 결정한다

### [매개변수](https://bbiguduk.gitbook.io/swift/language-reference/attributes)

1. 플랫폼 이름
    
    `iOS`  `iOSApplicationExtension` `macOS` `macOSApplicationExtension` `macCatalyst` `macCatalystApplicationExtension` `watchOS`  `watchOSApplicationExtension` `tvOS` `tvOSApplicationExtension` `swift` 
    
2. 가용성
    
    `*`
    
3. 추가정보
    
    `unavailable` : 지정된 플랫폼에서 불가능하다. (`Swift`에서는 사용할 수 없다)
    
    `introduced` : 선언이 도입된 플랫폼이나 언어의 첫 번째 버전을 의미한다.
    
    `deprecated` : 선언이 사용되지 않은 지정된 플랫폼이나 언어의 첫 번째 버전
    
    등등.. 필요할 때 공식 문서를 참고해서 작성해보자!
    

### 예제

```swift
@available(iOS 11.0, *)
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        
    }
    
    @available(iOS 11.0, *)
    func doSomething() {
        // (if, while, guard문으로도 사용 가능)
        if #available(iOS 11.0, *) {
            // iOS 11버전이상인 경우 적용 시킬 코드를 작성
            
        } else {
            // iOS 11버전미만인 경우 적용 시킬 코드를 작성
        }
        
    }

}
```

- 해당 클래스는 iOS 11 이상에서만 해당 클래스를 읽을 수 있다.
- doSomething은 iOS 11 이상에서만 존재하는 코드이다.
    - iOS 11 이상에서만 해당 조건문이 실행된다. (현재 로직으로는 적합하지는 않다.)

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
