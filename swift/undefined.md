# 검증함수

#### fatalError()

```swift
// 함수 원형, 무조건 앱을 종료시키는 에러
func fatalError(
    _ message: @autoclosure () -> String = String(),
    file: StaticString = #file,
    line: UInt = #line
) -> **Never**
```

* message: 에러 발생시 표기하고자하는 메시지
* file: 에러가 발생한 파일 이름
* line: 에러가 발생한 라인 번호

```swift
func someCloseAppSituation() {
    fatalError("앱 해킹시도 흔적 발견")
}
```

* 개발자가 필요한 경우 조건에 따라서 앱을 종료시키는 에러를 직접 만드는 방법이다.



#### 디버깅 함수

* 디버깅 (테스트/검증)을 위해 일부러 앱을 중지시키는 함수이다.
* 디버깅 함수 종류
  * `assert()`
  * `assertionFailure()`
  * **`precondition()`**
  * **`preconditionFailure()`**
  * `fatalError()`
* 앱을 테스트하여 런타임 중 발생할 수 있는 버그의 가능성을 확인하고, 잘못된 코드의 검증을 수행하는 함수이다.
* 이상한 유저들
  * 숫자만 입력할 수 있도록 다 하는데도 복사했던 것들을 붙ㅇ여넣는 등의 사람들이 있음
  * 출시 이전 앱 (디버그 모드) 앱에서 무조건 많은 오류들을 찾아 해결해야한다.
  * 이상한 행동에 대해서 assert 함수를 넣어서 어디서 오류가 발생했는지 확인할 수 잇므
    * 버그 발견하고, 선 조치할 수 있도록 도와주느 ㄴ함수
    * 디버그 모드에서 assert
      * 디버그 모드에서는 앱이 종료됨
    * 출시 모드에서 assert 함수는 실행되지 않는다.
      * 주석처리하지 않아도, 자동으로 제거되는 효과가 있다.
  * precondition
    * 출시버전에서도 종료시킨다.
    * 에러 발생시 심각한 상황이 발생하기 때문임



#### assert() / assertionFailure()

* 실제 앱을 출시할 때 일부러 앱을 종료시킬 필요는 없는 경미한 에러. 디버그 모드에서 검증이 가능하다.
  * 예상하는 범위는 벗어났지만, 아주 유의미하지는 않으므로 앱을 종료시키지는 않는다.
  *   예시) 양수만 나타나야하는데, 음수가 나타난 경우

      ```swift
      func enterWrongValue1() {
          let someWrongInput = -1
          assert(someWrongInput > 0, "유저가 값을 잘못 입력")
      }

      enterWrongValue1() // error 발생
      ```
*   `**assertionFailure**` assert의 형제

    * 항상 거짓의 경우에만 실행한다.
    * 이 경우가 나오면 안 될 때 사용한다.

    ```swift
    func enterWrongValue2() {
        let someWrongInput = -1
        
        if someWrongInput > 0 {
            // 정상적으로 처리하는 코드
            
        } else {
            **assertionFailure**("유저가 값을 잘못 입력")
        }
    }
    ```



#### precondition 함수

* 앱 출시 이후에도 **앱을 일부러 종료**한다.
* 앱의 업데이트로 인해, **버전이 없데이트 되었는데, 이전 버전을 사용하고 있을 때**
  * 서버 통신에 에러가 발생할 수도 있기 때문에 일부러 종료시켜야할 수도 있다.

```swift
func appUpdateCheck1() {
    let update = false
    precondition(update, "앱을 업데이트 하지 않음")
}
```

```swift
func appUpdateCheck2() {
    let update = false
    
    if update {
        // 앱을 업데이트 했을때, 정상적으로 실행할 코드
        
    } else {
        preconditionFailure("앱을 업데이트 하지 않음")
    }
}
```
