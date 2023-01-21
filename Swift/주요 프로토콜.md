# 주요 프로토콜


### 동일성 비교 (Equatable)

- `==`, `!=`
    - 연산자 (메서드)
    - 동일한지 동일하지 않은지를 비교할 수 있었던 이유가 Equatable이라는 자격증을 채택했기 때문이다.

### 크기 비교 (Comparable)

- 작은지 큰 지(순서, 정렬)을 하겠다는 말이다.
- 동등함에서 조금 더 나아간 개념이다.
- 내부적 매커니즘
    - 애플이 만들어놓은 Comparable을 채택하고, 미리 구현해놓았기 때문에 기본적인 타입에는 구현이 되어있었던 것이다.

### Hashable

- 유일한 값을 갖도록한다.
    - Dictionary의 키 값 또는 Set의 요소가 될 수 있다.

---

## Equatable

### Equatable Protocol

- 동일성 판단
- 내부적인 구현
    - 타입메서드를 구현해야한다.
        
        `**static func == (lhs: Self, rhs: Self) -> Bool**`
        
- 스위프트에서 제공하는 기본 타입은 모두 다 채택하고 있다.
    - Int, Double, String 등
- 구조체, 열거형의 경우 Equatable 프로토콜 채택하면 비교 연산자 메서드를 자동으로 구현한다.
    - 단, 모든 저장속성이 Equatable 프로토콜을 채택한 타입이어야한.
- 클래스는 인스턴스 비교 연산자 (===)가 존재하므로, 비교 연산자 구현 방식은 개발자가 구현해야한다.
- 열거형은 연관값이 없다면, 기본적으로 Equatable / Hashable하므로 해당 프로토콜을 채택하지 않아도 된다.

### 예시1 - Equatable을 채택하면, 비교 연산자가 자동으로 구현된다. (열거형과 구조체)

- 구조체, 열거형의 경우 Equatable 프로토콜 채택 시, 모든 저장 속성이 모두 Equatable을 채택했다면, 자동구현된다.
    - 저장속성까지 동일해야만 ==하다고 볼 수 있다. ⇒ 각 저장 속성이 Equatable하다면, 인스턴스가 동일한지 확인할 수 있다.
    
    ```swift
    enum SuperComputer: **Equatable** { // == 함수를 구현하지 않아도 사용할 수 있다.
        case cpu(core: Int, ghz: Double)
      case ram(Int)
      case hardDisk(gb: Int)
    }
    
    SuperComputer.cpu(core: 8, ghz: 3.5) == SuperComputer.cpu(core: 16, ghz: 3.5)
    ```
    

### 예시2 - 연관값이 없다면, 프로토콜 채택 없이 동일성 비교가 가능하다.

```swift
enum Direction {
    case east
    case west
    case south
    case north
}

Direction.north == Direction.east    // false
Direction.north != Direction.east    // true
```

### 예시3 - 클래스의 Equatable

- 클래스는 원칙적으로 동일성 비교가 불가능하다.
- 항등 연산자가 존재하므로, 비교 연산자 구현방식은 개발자에게 위임한다.
    - 타입 메서드를 구현해야한다.
    
    ```swift
    class Person {
        var name: String
        var age: Int
        
        init(name: String, age: Int) {
            self.name = name
            self.age = age
        }
    }
    
    extension Person: Equatable {
        **static func ==(lhs: Person, rhs: Person) -> Bool** {  // 특별한 이유가 없다면 모든 속성에 대해, 비교 구현
            return lhs.name == rhs.name && lhs.age == rhs.age
            //return lhs.name == rhs.name     // 이름만 같아도 동일하다고 보려면 이렇게 구현, 개발자 마음대로
            //return lhs.age == lhs.age       // 나이만 같아도 동일하다고 보려면 이렇게 구현
        }
    }
    
    let person1: Person = Person(name: "홍길동", age: 20)
    let person2: Person = Person(name: "임꺽정", age: 20)
    
    person1 == person2
    person1 != person2
    ```


## Comparable

### Comparable Protocol

- 값의 크기나 순서의 비교, 정렬이 필요할 때 채택한다.
- 요구사항 `static func <(lhs: Self, rhs: Self) → Bool`
    - 일반적으로 lessthan 연산자만 구현하면, 나머지는 자동으로 구현된다.
- Equatable 프로토콜을 상속하고 있어서 필요한 경우 비교 연산자도 구현해야한다.
- Swift의 기본 숫자 타입 및 String은 해당 프로토콜을 모두 채택하고 있다.
- Equatabler과 달리, 메서드를 자동으로 구현해주지 않는다.
    - 순서 정렬 방식에 대해서는 개발자가 구체적인 구현을 해야한다.
    - 원시 값이 없는 열거형의 경우(연관값은 상관 없음), 프로토콜 채택만하면, < 연산자를 자동으로 제공한다.
        - 원시값이 있다면, 개발자가 대응하는 값을 제공하므로, 정렬 방식도 직접 구현해야한다.

### 예시 - 열거형의 비교 연산자 구현

1. 원시값이 있는 경우 - 메서드 직접 구현 필요
    
    ```swift
    enum Direction: **Int** {
        case east
        case west
        case south
        case north
    }
    
    extension Direction: Comparable {
        static func < (lhs: Direction, rhs: Direction) -> Bool {
            return lhs.rawValue < rhs.rawValue
        }
    }
    ```
    

1. 원시 값이 있는 경우
    
    ```swift
    enum SuperComputer: Comparable {
        case cpu(core: Int, ghz: Double)
        case ram(Int)
        case hardDisk(gb: Int)
    }
    
    SuperComputer.cpu(core: 8, ghz: 3.5) < SuperComputer.cpu(core: 16, ghz: 3.5)
    SuperComputer.cpu(core: 8, ghz: 3.5) > SuperComputer.cpu(core: 8, ghz: 3.5)
    ```
    

### 구조체

- 순서 정렬 방식에 대해서는 구체적인 구현이 반드시 필요하다.
    - 단, 모든 저장속성이 equatable 프로토콜을 상속하고 있다면 == 메서드는 컴파일러가 자동으로 구현한다.
    
    ```swift
    struct Dog {
        var name: String
        var age: Int
    }
    
    extension Dog: Comparable {
        // static func ==(lhs: Dog, rhs: Dog) -> Bool // 자동구현
    
        static func <(lhs: Dog, rhs: Dog) -> Bool {
            return lhs.age < rhs.age
        }
    }
    ```
    

### 클래스

- 구조체와 동일하게 less than을 직접 구현해야한다.
- 클래스는 == 연산자도 직접 구현해주어야한다.
    
    ```swift
    class Person {
        var name: String
        var age: Int
    
        init(name: String, age: Int) {
            self.name = name
            self.age = age
        }
    }
    
    extension Person: Comparable {
    
        static func ==(lhs: Person, rhs: Person) -> Bool {
            return lhs.name == rhs.name && lhs.age == rhs.age
        }
    
        static func <(lhs: Person, rhs: Person) -> Bool {
            return lhs.age < rhs.age
        }
    }
    ```

## Hashable

### Hash 함수

- 고정된 길이의 숫자, 글자이면서 유일한 값의 결과를 주는 함수
- 딕셔너리의 키 값이 될 수 있거나, 집합의 요소가 될 수 있다.

### Hashable Protocol

- `**func hash(into hasher: inout Hasher)**`
- Swift의 기본 숫자 타입은 모두 채택하고 있다.
- 구조체, 열거형의 경우 Hashable 프로토콜 채택 시, hash(into:) 메서드가 자동구현된다.
    - 단, 모든 저장속성 (연관 값)이 Hashable 프로토콜을 채택한 경우만 해당된다.
- 클래스 인스턴스의 유일성을 위해서는 hash(into: )를 직접 구현해야햔다. (원칙적으로 Hashable 지원이 불가능하다)
- 열거형의 경우 연관 값이 없다면, Equatable / Hashable하므로 프로토콜을 채택하지 않아도 된다.

### 예시1 - 열거형

1. 연관 값이 있는 경우
    - 모든 연관값이 hashable 프로토콜을 채택한 타입이라면, 메서드를 자동으로 구현해준다.
    - 저장속성들이 이미 Hashable하므로, 해당 정보들도 유일성 판별이 가능하기 때문
    
    ```swift
    enum SuperComputer: Hashable {
            case cpu(core: Int, ghz: Double)
        case ram(Int)
        case hardDisk(gb: Int)
    }
    
    let superSet: Set = [SuperComputer.cpu(core: 8, ghz: 3.5), SuperComputer.cpu(core: 16, ghz: 3.5)]
    ```
    

1. 연관 값이 없는 경우 (원시값 여부는 상관 없음)
    - 기본적으로 열거형은 Equable / Hashable하다.
    
    ```swift
    enum Direction {
        case east
        case west
        case south
        case north
    }
    
    let directionSet: Set = [Direction.north, Direction.east]
    ```
    

### 예시2 - 구조체

- 구조체, 열거형의 경우 Hashable 프로토콜을 채택했다면 메서드가 자동으로 구현된다.
    - 단, 모든 저장속성이 Hashable를 채택한 경우만 해당한다.
    
    ```swift
    struct Dog {
        var name: String
        var age: Int
    }
    
    extension Dog: Hashable {}
    
    // 구현할 필요 없음
    //extension Dog: Hashable {
    //    func hash(into hasher: inout Hasher) {
    //        hasher.combine(name)
    //        hasher.combine(age)
    //    }
    //}
    ```
    

### 예시3 - 클래스

- 인스턴스가 유일성을 갖기 위해서는 hash메서드를 직접 구현해야한다.
    
    ```swift
    class Person {
        var name: String
        var age: Int
    
        init(name: String, age: Int) {
            self.name = name
            self.age = age
        }
    }
    
    extension Person: Hashable {
        func hash(into hasher: inout Hasher) {
            hasher.combine(name)
            hasher.combine(age)
        }
    
        // Equatable을 상속하므로, 연산자도 구현해주어야한다.
        static func ==(lhs: Person, rhs: Person) -> Bool {
            lhs.name == rhs.name && lhs.age == rhs.age
        }
    }
    ```
    
## CaseIterable (Swift 5.2~ )

### CaseIterable

- 열거형에서 사용하는 프로토콜
    - 해당 프로토콜을 채택하면, 타입 계산 속성이 자동으로 구현된다.
    - `static var allCases: Self.AllCases { get }`
    - 모든 Case를 배열로 리턴하며, 연관값이 없는 경우만 채택이 가능하다.

```swift
enum Color: CaseIterable {
    case red, green, blue
}

Color.allCases // [Color.red, Color.green, Color.blue]

// 반복문 사용 가능
for color in Color.allCases {
    print(color
}

// 편리한 선언
struct SomeView {
    let colors = Color.allCases
```
