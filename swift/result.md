# Result


### Result type

<aside>
💡 에러가 발생하는 경우, 에러를 외부로 던지지 않고, Result 타입에 함수 실행 결과를 담아 리턴할 수 있다.

</aside>

- 내부적으로 열거형으로 구현되어있다.
    - `enum **Result<Success, Failure>** where Failure: Error`
    - 성공과 실패 정보를 함께 담아서 리턴한다.
    - Failure의 경우, Error 프로토콜을 채택해야한다.

### throwing과의 비교

```swift
// 1단계: 에러타입 정의

enum HeightError: Error {
    case maxHeight
    case minHeight
}

// 2단계: throwing 함수 정의
func checkingHeight(height: Int) -> throws Bool {
    if height > 190 {
        throw HeightError.maxHeight
    else if height < 130 {
        throw HeightError.minHeight
    } else {
        if height >= 160 {
            return true
        } else {
            return false
        }
    }
}

// 3단계: 에러처리
do {
    let _ = try checkingHeight(height: 200)
    print("가능")
} catch {
    print("불가능"
}

// 2단계: Result type 함수 정의
func CheckingHeight(height: Int) -> Result<Bool, HeightError> {
    if height > 190 {
        return Result.failure(HeightError.maxHeight)
    } else if height < 130 {
        return Result.failure(HeightError.minHeight)
    } else {
        if height >= 160 {
            return Result.success(true)
        } else {
            return Result.success(false)
        }
    }
}

// 3단계: 에러처리
let result = CheckingHeight(height: 200)

switch result {
case .success(let data):
    print("가능")
case .failure(let error):
    print("불가능")
}
```

### Result Type의 활용

```swift
do {
    let data = try result.get()
    print("결과 = \(data)")
} catch {
    print(error)
}
```

- 다양한 메서드가 존재한다.
    - `get()` 은 결과를 throwing하는 함수처럼 변환가능한 함수이다. (Success value를 리턴한다)



### Result 타입을 사용하는 이유는?

- 성공, 실패의 경우를 깔끔하게 처리할 수 있다.
- 기존 에러처리를 완전히 대체하려는 목적이 아니라, 에러 처리에 다양한 옵션을 제공하는것이다.
- 에러타입을 명시적으로 선언해서 타입 캐스팅이 불필요해진다.




### Network에서 Result 타입의 활용


```swift
enum NetworkError: Error {
    case .someError
}

func performRequest(with url: String, completion: @escaping (Result<Data, NetworkError> -> void) {
    
    guard let url = URL(string: url) else { return }

    URLSession.shared.dataTask(with: url) { (data, response, error) in 
        if let error {
            completion(.failure(.someError))
            return
        }

        guard let data else {
            completion(.failure(.someError)
            return
        }

        completion(.success(data))
    }.resume()
}

performRequest(with: "address") { result in
    switch result {
    case .failure(let error):
        print(error)
    case .success(let data):
        // data 처리
    }
}
```
