### 일반적인 함수의 return의 의미

1. 함수 결과 값의 리턴
2. CPU 제어권 리턴

```swift
func addTwoNumbers(a: Int, b: Int) -> Int {
    var c = a + b
    return c // 값의 리턴 뿐만 아니라 제어권을 돌려줌.
}

var num1 = 5
var num2 = 3

var num3 = addTwoNumbers(a: num1, b: num2)
print(num3)
```

### Never 타입

<aside>
💡 Never 타입을 반환하는 함수 또는 메서드란, 함수 또는 메서드가 제어권을 비반환(nonreturning)하는 함수이다. 이는 복구할 수 없는 에러를 발생시키거나 무한으로 작업을 시작한다.

</aside>

- 앱을 테스트, 검증하는 단계에서 사용한다.
- CPU의 제어권을 돌려주지 않는다.
    - 일부러 크래시 (에러)를 발생시킨다. (Nonretruning이라고 말한다)
- Never 타입 = 임시 타입
    - 내부적으로 비어있는 열거형으로 선언된다.
    - 실질적인 인스턴스를 생성할 수 없다. (**Uninhabited Type**)
    - 임시적인 타입으로 인스턴스 생성이 불가능하다.
        - `let some = Never() // 불가능`
        - 열거형이지만, `let day: Weekday = Weekday.monday` 식의 인스턴스 생성 불가능
- 활용 방법
    - 함수 내에서 무조건 앱을 종료시킨다.
    - 무조건 에러를 발생시켜 에러 처리하는 코드로 제어권을 전달한다.
- 활용 방법
    - 함수 내에서 앱을 종료시킨다.
    - 무조건 에러를 발생시켜서 에러 처리하는 코드로 제어권을 전달한다. ( 드문 케이스 )
        
        ```swift
        func crashAndBurn() -> Never {
            fatalError("앱의 해킹이 발견됨") // 특별한 경우에 사용하는 함수. Never타입을 가지고 있는 함수
        }
        
        print("1")
        crashAndBurn()
        print("2") // WARNING: - Will never be executed
        ```
        

### 사용하는 이유

- 런타임에 발생할 수 있는 에러를 미리 발견하고 검증 및 테스트하기 위해서 사용한다.
- 가벼운 에러라면, 출시할 때에는 해당 코드를 삭제해야한다. (해킹같은 경우는 종료시켜야한다)
- 출시 이전에 앱을 무한대로 테스트할 때, `fatal error`를 사용한다.
    - 검증함수
        - assert()
        - precondition()
    
    ```swift
    // 1) 에러 정의
    enum SomeError: Error {
        case aError
        case bError
    }
    
    // 2) 에러를 던지는 함수의 정의
    
    func someThrowingErrorFuncion() throws -> **Never** { // 실무에서는 에러를 던지지는 않음
        if true {
            throw SomeError.aError
        } else {
            throw SomeError.bError // WARNING: - Will never be executed
        }
    }
    
    // 3) 에러를 던지는 함수의 실행
    
    do {
        try someThrowingErrorFuncion()
    } catch {
        print(error)
    }
    ```
    

---
