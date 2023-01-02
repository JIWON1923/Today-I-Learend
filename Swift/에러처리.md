# 에러처리

### 에러처리 목차
- (에러처리의 개념)[#에러처리의-개념]
    - 에러의 종류
    - 에러처리
- (에러 처리 방법)[#에러-처리-방법]
    - try catch (try 문)
- (Defer문)[#Defer문]

## 에러처리의 개념

### 컴파일 오류

- 문법적 오류 (에러)
    - Swift 문법과 관련된 에러이다. (컴파일러가 알려줌)

### 런타임 오류

- 앱 실행 중 다양한 이유로 발생 → 앱의 종료
    - 발생 가능한 에러는 미리 처리해야함.
    
    참고: 크래시 (앱의 강제 종료)
    

### 에러 처리가 필요한 이유

- 와이파이 이슈, 서버 이슈 등 다양한 오류가 발생할 수 있음 → 처리를 해 줘야돼!
- 앱이 무작정 꺼지지 않도록 처리하는 것.
    - 사용자 경험과 관련해서 중요함. (알려주든지, 재시도를 요청하든지)

---

## 에러처리

- 어떤 상황이 발생했을 때, 비정상적인 경우  (에러를 던짐) 를 정의하는 것.

### 에러 처리의 과정 (3단계)

1. 에러 타입 정의
    - Error 프로토콜을 채택한 에러 열거형을 정의한다.
        - Error 프로토콜을 채택하면 정의한 타입 열거형이 Swift의 에러 프로토콜 내로 통합된다.
    - 어떤 에러가 발생할지 미리 정의하는 것이다.
2. 에러가 발생할 수 있는 함수를 정의한다.
    - 각 에러의 종류 (비정상적인 경우)를 분류하여 리턴
    - `throw` 키워드로 던짐
3. 에러를 처리한다. (함수의 실행)
    - `try`와 `do`-`catch`문으로 처리한다.
        
        ```swift
        do { // 정상적인 경우의 처리 상황
        
            var result = try functionName() // return 값이 있는 경우
            
        } catch { // 위 함수가 에러를 던졌을 때 (에러가 발생한 경우) 처리하는 실행 블럭
        
        }
        ```
        

---

### 예제

```swift
// 에러의 정의 (열거형)
enum SomeError: **Error** { // **Error 프로토콜** 반드시 채택 필요
    case aError
    case bError
    case cError
}

// 일반 함수
func doSomethine(num: Int) -> Bool {
    if num >= 7 { 
        return true
    } else {
        return false
    }    
}

// 에러를 던지는 함수
// 정상적인 경우는 Bool, 비정상적인 경우에는 error를!
func doSomethine(num: Int) **throws** -> Bool {
    if num >= 7 { 
        return true
    } else {
        if num <0 { // 에러가 나는 조건
            **throw SomeError.aError**
        }
        return false
    }    
}

```

## try문

### 정석적인 트라이 `try`

- try , do - catch로 사용
    - 모든 에러 발생의 예외적인 경우를 디테일하게 처리 가능
    - 해당 사용에 익숙해져야한다.
    
    ```swift
    do {
        let data = try parsing()
    } catch {
        // 에처처리
    }
    ```
    

### 옵셔널 트라이 `try?`

- 옵셔널 타입으로 리턴
    - 정상적인 경우, 리턴 타입으로 리턴
    - 에러 발생 시, nil 리턴
- 값 사용 시 옵셔널 값을 unwrapping해서 사용해야함.
- `let data = **try?** parsing()`

### forced 트라이 `try!`

- 에러가 날 수 없는 경우에만 사용
    - 정상적인 경우 리턴 타입으로 리턴
    - 에러가 발생하면 런타임 에러
- 오류가 없다고 확신할 수 있을 때에만 사용한다. (제한적 사용)
- `let data = **try!** parsing()`

---

### catch 블럭의 구체적인 처리 법

- catch 옆에 case를 쓸 수 있다. (에러를 따로 처리하는 경우)
1. 패턴을 모두 정의 (경우를 모두 따로 처리)
    
    ```swift
    do {
        let isChecked
    
    catch HeightError.maxHeight { // 모든 열거형에 대한 처리를 해 주어야 한다.
        print("")
    } catch HeightError.minHeight { 
    } catch { // 아무것도 없으면 **defualt 블럭**이 된다.
    }
    ```
    
    ```swift
    // Swift 5.3부터 catch 뒤 케이스를 여러개 나열할 수 있다.
    do {
        let isChecked
    
    catch case1, case2 { // case 나열 가능
    
        
    } 
    ```
    

1. 패턴 없이 에러를 받아서 처리 (다운캐스팅  후 사용)
    - catch블럭 내에서 error 상수를 제공한다.
    - error 상수(error 프로토콜 타입)을 구체적으로 정의한 타입으로 캐스팅하여 switch문 등으로 다시 처리한다.
    
    ```swift
    do {
        let isChecked
    
    catch { // error 상수를 제공 (모든 에러가 넘어옴)
    
        print(error) 
        print(error.localizedDescription) // error 프로토콜에 있는 속성, 언어별로 출력해줌
    
        if let error = error as? HeightError { // 다운캐스팅하여 구체적으로 분류할 수 있다.
            switch error {
            case .aError:
            case .bError:
            }
    } 
    ```

## Defer문

### defer문?

- 어떤 동작을 마지막으로 미루는 방법이다.
    - 일반적으로 어떤 동작의 마무리 동작을 특정하기 위해 사용한다.
    - 코드의 실행을 스코프가 종료되는 시점으로 연기시키는 문법이다.

### 예시

```swift
func deferStatement1() {
    defer {
        print("나중에 실행하기")
    }
    print("먼저 실행하기")
}
```

```swift
func deferStatement2() {
    if true {
        print("먼저 실행하기")
        return
    }

    defer {
        print("나중에 실행하기") // 실행되지 않는다. defer문이 호출되어야, 실행  예약이 된다.
    }
}
```

```swift
func deferStatement() {
    defer {
        print(1)
    }
    defer {
        print(2)
    }
    defer {
        print(3)
    }
}
// 3, 2, 1로 실행됨
// 역순으로 출력된다.
// 실 사용에서는 보통 하나만 사용한다.
```

- 일반적으로 어떤 동작의 마무리 동작을 특정하기 위해 사용한다. (정리 개념)

### defer문을 왜 사용할까?

- 동작의 마무리를 까먹을 수 잇어서..?
- 함수에서만 쓸 수있는 것이 아니고, for문에서도 사용할 수 있따.
    
    ```swift
    for i in 1...3 {
        defer { print ("Defer된 숫자?: \(i)") } // 다음 주기로 넘어가기 전에 실행된다. (총 3번)
        print ("for문의 숫자: \(i)")
    }
    
    /*
    for문의 숫자: 1
    Defer된 숫자?: 1
    for문의 숫자: 2
    Defer된 숫자?: 2
    for문의 숫자: 3
    Defer된 숫자?: 3
    */
    ```
    

### DEFER

- 어떤 동작을 마지막으로 미루는 방법
- 일반적으로 어떤 스코프 내 동작을 마무리하기 위해 사용된다.
- defer문은 한 번은 호출되어야지만, 해당 구문이 실행된다.
