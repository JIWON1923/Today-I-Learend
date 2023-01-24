### KeyPath

- 문자열 방식으로 속성에 간편하게 접근하기 위해서 사용한다.
    - 간접적으로 접근하기 위해서
- Person.name의 값이 바뀌는 순간을 알고 싶을 때!
    - 속성감시자로 하지 않는 이유
        - 객체 내부가 아닌, 객체의 외부에서만 관찰해야하는 경우가 존재한다.
    - `\Person.name` → 경로를 만들고, 인스턴스 속성에 접근하는 기술이다.

### Selector

- 메서드의 주소를 통해 메서드를 가리키기 위한 기술
    - `#selector(Person.walk)` → 메서드가 구현된 주소를 간접적으로 뽑아낼 수 있다.

---

## KeyPath의 시작

### 어떻게 만들어지게 되었을까?

- 간접적으로 속성을 접근하고 싶다.
    - 특별한 문자열을 만들어놓고, 해당 속성에 접근하고 싶어!
    - 속성에 조금 더 쉽게 접근하고 싶어! (서브스크립트처럼)

### 코드

```swift
class School {
    var name: String
    **var affiliate: SmallSchool**
    
    init(name: String, affiliate: SmallSchool) {
        self.name = name
        self.affiliate = affiliate
    }
}

class SmallSchool {
    **var classMember: Person**
    init(classMember: Person) {
        self.classMember = classMember
    }
}

class Person {
    **var name: String**
    init(name: String) {
        self.name = name
    }
}

// 직접 접근
let person1 = Person(name: "홍길동")
let smallSchool1 = SmallSchool(classMember: person1)
let school1 = School(name: "슈퍼고", affiliate: smallSchool1)

let gildong = school1.affilate.classMember.name

// Swift5에서의 간접 접근
let namePath = \School.affiliate.classMember.name // keyPath 경로 지정
school1[keyPath: namePath] // 딕셔너리 방식(서브스크립트)로 접근 가능
```

- KeyPath = 경로
- 대괄호를 쓰면, 간접적으로 접근할 수 있다.

---

### 경로 추가

```swift
let namePath = \School.affiliate.classMember     // 미리 경로를 지정 (**keyPath**)
let newNamePath = namePath.appending(path: \.name)
school1[keyPath: newNamePath]      // appending 메서드로 경로를 추가할 수 있다.
```
