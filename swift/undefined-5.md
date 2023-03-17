# 접근제어 (Access Control)


### 접근제어가 필요한 이유

<aside>
💡 코드의 세부 구현 내용을 숨기는 것이 가능하도록 만드는 개념으로, 객체지향의 은닉화가 가능해진다.

</aside>

```swift
class SomeClass {
    private var name = "soi"

    func changeName(name: String) {
        if name == "ios" { return }
        self.name = name
    }
}

let object1 = SomeClass()
object.name = "ios"
```

- 타 개발자가 잘못 사용할 수 있는 경우를 제한할 수 있다.

### Swift의 접근 수준(Access Levels)

1. `open` : 다른 모듈에서 접근 가능, 상속 및 재정의 가능
2. `public` : 다른 모듈에서 접근 가능, 상속 및 재정의 불가능
3. `internal` : Default 수준, 같은 모듈 내에서 접근 가능
4. `fileprivate` : 같은 파일 내에서만 접근 가능
5. `private` : 같은 scope 내에서 접근 가능

<aside>
💡 모듈이란?

- import 해서 사용할 수 있는 외부 코드라고 생각하자.
- 개발자가 만든 모든 코드로, 하나의 단위를 의미한다.
    - Foundation도 framework지만, 하나의 모듈로 볼 수 있다.
    - 즉, framework나 라이브러리, 또는 내가 만든 코드!
- 만약, 모듈을 만들어 배포하고자 한다면, `public` 이상으로 선언되어야한다.
</aside>

### 접근 제어를 가질 수 있는 요소 (엔티티 / 독립된 객체)

1. 타입 ( 클래스, 구조체, 열거형 등 Swift 기본 타입)
    - 구조체가 가질 수 있는 가장 넓은 제한 수준은 public이다. (상속이 없기 때문에)
    - 타입 자체도 접근 제어 수준을 가질 수 있다.
2. 변수 / 속성
3. 함수 / 메서드 (생성자, 서브스크립트 포함)
4. 프로토콜 (특정 영역으로 제한)

### 접근제어가 필요한 이유

- 정보의 은닉화가 가능하다.
    - 외부에서 private으로 선언된 속성, 메서드에 접근이 불가능하다. 이에 코드 내부의 세부 구현 내용을 숨기는 것이 가능하다.
- 애플이 자신들이 원하는 코드를 감출 수 있다.
- 코드의 영역을 분리시켜 효율적인 관리가 가능하다.
- 컴파일 시간이 줄어든다.
    - 타입이 오픈되어있다면, 모든 곳에서 사용할 수 있어야하므로 오래걸린다.
    - 접근제어를 사용하면 해당 변수가 어떤 범위에서 적용되는지 확실히 알 수 있기 때문이다.

### 원칙

<aside>
💡 타입은 타입을 사용하는 변수(속성)나, 함수보다 높은 수준으로 선언되어야한다!

</aside>

```swift
// 변수
//public variable에 속한 타입은 더 낮은 접근 수준을 가질 수 없다.
//(internal, fileprivate, privae)
var some: String = "hello"

//함수
// 파라미터, 리턴 ㅇ타입이 더 낮은 접근 수준을 가지지 못함
// internal, fileprivate, private
internal func someFunction(a: Int) -> Bool {
    print(a)
    return true
}

// 자신보다 내부에서 더 낮은 타입을 사용하면 접근을 못해서 사용하지 못할 수도 있다.
```

- 변수와 타입 모두 접근 수준이 설정되어있음
    - 변수는 internal, 타입은 public이 기본 설정!
- 함수도 마찬가지
    - 함수 internal, 파라미터, 리턴형은 public

### 실무에서 사용하는 관습적 패턴

- 실제 프로젝트에서 많이 사용하는 관습적 패턴
- 언더바를 붙인 속성 이름은 언더바를 사용해서 이름을 짓는다. (c언어)

```swift
class SomeOtherClass {
    private var **_name** = "name" // 쓰기 - private

    var **name**: String { // 읽기 - internal
        return _name
    }

    func changeName(name: String) { // 이렇게 수정도 가능하기 ㄴ함!
        self._name = name
    }
}
```

### setter의 제한을 높이는 방법 (접근 수준 낮추기)

```swift
class SomeClass {
    private var _name = "이름"

    var name: String {
        return _name
    }
}

class SomeAnotherClass {
    internal private(set) var name = "이름" // 읽기 - internal / 쓰기 - private, internal은 생략가능
}

let a = someAnotherClass()
a.name // 접근 가능
a.name = "hello" // 불가능, private이므로

```

- Swift에서 사용할 수 있는 키워드!

```swift
class SomeAnotherClass {
    private(set) var name = "이름" // 읽기 - internal / 쓰기 - private
}

let ab = SomeAnotherClass()
ab.name // 가능 
ab.name = "길동 // error, 애는 private
```

### 커스텁 정의 타입의 접근 제어

<aside>
💡 1. 타입의 내부 멤버는 타입 자체의 접근 수준을넘을 수 없다.

    ⇒ 타입의 접근 제어 수준은, 해당 타입 멤버의 접근 제어 수준에 영향을 미친다.

1. 내부 멤버가 명시적 선언을 하지 않는다면, Internal로 유지된다.

    ⇒ 타입의 접근수준이 높다고, 내부 멤버의 접근 수준이 무조건 높아지는 것은 아니다. 

</aside>

```swift
// 타입의 접근 제어 수준은 (해당 타입)멤버의 접근 제어 수준에 영향을 미침
// (예시) 타입이 internal 로 선언 되면 내부의 멤버는 internal이하(internal / fileprivate / private)로 설정됨

public class SomePublicClass {                         // 명시적인 public 선언
    open var someOpenProperty = "SomeOpen"             // open 이라고 설정해도 public으로 작동 ⭐️
    public var somePublicProperty = "SomePublic"
    var someInternalProperty = "SomeInternal"          // 원래의 기본 수준
    fileprivate var someFilePrivateProperty = "SomeFilePrivate"
    private var somePrivateProperty = "SomePrivate"
}

let somePublic = SomePublicClass()
somePublic.someOpenProperty
somePublic.somePublicProperty
somePublic.someInternalProperty
somePublic.someFilePrivateProperty                    // 같은 파일 안이기 때문에 접근 가능
//somePublic.somePrivateProperty
```

- 타입 자체를 private으로 선언한다면, file private으로 동작하게 된다.
    - 타입 자체가 private이라면, 아무곳에서도 사용할 수 없기 때문이다.

---

### 내부 멤버의 접근 제어 수준 (Nested Type)

- 내부 멤버가 명시적 선언을 하지 않는다면, 접근 수준은 internal로 유지한다.
- 타입의 접근 수준이 높다고, 내부 멤버의 접근 수준이 무조건 높아지는 것이 아니다.

```swift
open class SomeClass {
    var someProperty = "SomeInternal" // internal, open으로 사용하고 싶다면 명시적 선언 필요
}
```


### 상속 관계의 접근제어 (Subclassing)

<aside>
💡 1. 타입 : 상속해서 만든 서브클래스는, 상위클래스보다 더 높은 접근 수준을 가질 수 없다.
2. 멤버 : 동일 모듈에서 정의한 클래스의 상위 멤버에 접근 가능하다면, 접근 수준을 높여서 재정의 할 수 있다.

</aside>

```swift
public class A {
    fileprivate func someMethod() { }
}

// public 이하의 접근수준만 가질 수 있다.
internal class B: A {
    override internal func someMethod() { // 접근 수준을 올려서 재정의
        super.someMethod() // 접근 수준이 더 낮아도, 모듈에서 접근 가능하므로 호출 가능.
    }
}
```

### 확장의 접근 제어(Extension)

<aside>
💡 본체와의 동일한 접근 수준을 유지하며, 본체의 모든 멤버에 기본적인 접근이 가능하다.

</aside>

```swift
public class SomeClass {
    private var somePrivateProperty = "somePrivate"
}

extension SomeClass { // public과 동일함.
    func somePriateControlFunction() {
        somePrivateProperty = "접근가능"
    }
}
```

---

## 속성과 접근제어

### getter, setter의 접근 제어

<aside>
💡 속성의 읽기 설정과 속성의 쓰기 설정임. 계산 속성이 아니다!

</aside>

- 저장, 계산 속성의 읽기와 쓰기의 접근 제어 수준을 구분해서 구현할 수 있다.
    - 일반적으로, 밖에서 쓰는 것(setter)를 불가능하도록 구현하는 경우가 많다.
    
    ```swift
    struct TrackedString {
        //private var numberOfEdits = 0           // 이렇게 선언하면, 읽기도 불가능해짐
        private(set) var numberOfEdits = 0        // setter에 대해서만 private 선언
        //internal private(set) var numberOfEdits = 0
        
        // 속성 관찰자
        var value: String = "시작" {
            didSet {
                numberOfEdits += 1
            }
        }
    }
    
    var stringToEdit = TrackedString()
    stringToEdit.value = "첫설정"
    stringToEdit.value += " 추가하기1"
    stringToEdit.value += " 추가하기2"
    print("수정한 횟수: \(stringToEdit.numberOfEdits)")
    print(stringToEdit.value)
    
    // 외부에서 직접설정하는 것은 불가능 (읽는 것은 가능) ⭐️
    //stringToEdit.numberOfEdits = 2
    ```
    
- 변수 및 속성, 서브스크립트에 쓰기 수준을 읽기 수준보다 낮은 접근 수준으로 설정 가능

---

### 읽기 쓰기 중 쓰기의 제한을 높이는 방법

- 읽기의 수준보다 쓰기의 수준이 더 높을 수는 없다
- 쓰기는 데이터를 바꾸는 동작이므로 더 높은 위험성을 가진다. (다른 코드에 영향을 주니까)

```swift
// 읽기: internal, 쓰기: private
private(set)
internal private(set)

// 읽기 public, 쓰기 internal
public internal(set)
```
