### 메타타입

- 타입의 타입이다.
    - 클래스, 구조체, 열거형, 프로토콜 타입을 포함하여 모든 타입의 타입을 의미한다.
- `self` 표현식을 사요해서 타입의 값으로 접근할 수 있다.
    - SomeClass.self는 SomeClass의 인스턴스가 아닌 타입 자체를 반환한다.
    - 즉, 힙이나 스택 영역이 아닌 **데이터 영역**에 있는 타입 인스턴스를 이야기한다.
    - `type(of: )`함수를 호출하여 해당 인스턴스의 런타임 타입의 값으로 접근할 수 있다.

### 예시

```swift
class Dog {
    **static let species = "Dog" // 타입 저장 속성**
    var name: String = ""
    var weight: Double = 0.0
}

let dog1: Dog = Dog()
let dog: Dog.Type = Dog.self
let dogSelf: Dog.Type = **type(of**: dog1) // 인스턴스의 타입을 알려준다.

Dog.species     // "Dog", 타입 속성에 접근하는 방법
Dog.self.species    // "Dog", 정식 방법

// Int.self // 타입 인스턴스를 의미한다.
// Int.self.max
```

 

### 메타타입 선언 방법

- 커스텀타입의 경우
    - 클래스이름.Type
    - 구조체이름.Type
    - 열거형이름.Type
- 프로토콜의 경우
    - 프로토콜이름.Protocol
