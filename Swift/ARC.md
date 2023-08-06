# ARC

### ARC 목차

* [Swift의 메모리 관리 모델](ARC.md#Swift의-메모리-관리-모델)
  * 값 형식과 참조 형식의 비교
  * 메모리 구조 (코드, 데이터, 힙, 스택)
  * 언어 별 메모리 관리 기법
  * ARC 작동 원리
  * ARC와 MRC
* [강한 참조 사이클과 메모리 누수](%EA%B0%95%ED%95%9C-%EC%B0%B8%EC%A1%B0-%EC%82%AC%EC%9D%B4%ED%81%B4%EA%B3%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%88%84%EC%88%98/)
  * 강한 참조 사이클이란?
  * 해결 방안
  * 예제 코드

## Swift의 메모리 관리 모델

### 복습) 값 형식과 참조 형식

1. 값 형식 (Value Type)
   * Baisic Type, 튜플, 구조체, 열거형, 컬렉션 등이 해당된다.
   * Stack 영역에 값이 저장된다.
   * 필요시에는 항상 메모리의 값이 **복사**되어 전달된다.
   * 사용이 끝나면, Stack프레임에서 알아서 제거되므로 **별도로 메모리 관리를 할 필요 없다.**
2. 참조 형식 (Reference Type)
   * 클래스, 클로저가 해당된다.
   * 필요시에 항상 **메모리의 주소를 전달**한다. (**Heap의 주소를 Stack에 저장**)
   * **Heap 영역에 실제 값들이 저장**된다.
   * \*\*RC(Reference Counting, 참조 카운팅)\*\*을 통해 메모리를 관리한다.
     * Swift에서 사용하는 **ARC 모델**



### 복습) 메모리 구조 - 코데힙스

1. 코드영역
   * CPU가 실행할 수 있는 실질적 명령어들이 모여있는 공간 (모든 코드, 프로그램이 존재)
   * get Only
   * 코드영역에 필요한 데이터들이 **데이터, 힙, 스택** 영역에 저장되어있다.
     * 데이터 = 전역 변수, 타입 속성 등 공통으로 공유되는 데이터
     * 힙 = 동적할당으로 크기가 크고, 관리할 필요가 있는 데이터
     * 스택 = 함수 실행을 위한 임시 공간
2. 데이터 영역
   * 공통으로 공유하기 위한 데이터를 저장한다.
     * 전역 변수, 타입속성을 저장한다.
     * 붕어빵 틀이 저장되어 있다.
   * 앱이 실행되는 동안은 불변의 성질을 갖는다.
3. 힙 영역
   * 인스턴스를 동적으로 할당한다. (**동적할당**)
     * 일반적으로 긴 시간동안 저장한다.
   * 필연적으로 느릴 수밖에 없다.
   * 관리하지 않으면 메모리 누수 현상이 발생해 앱이 종료될 수 있다.
     * 메모리 누수 현상(**Memory Leak**): 메모리가 제거되지 않는 현상
     * 개발자가 직접 관리해주어야한다.
4. 스택 영역
   * 함수 실행을 위한 임시적 공간이다.
   * 크기가 작고 빠르게 사용하기 위한 데이터가 저장되어 있다.
   * 자동으로 메모리 관리가 이루어진다.



### 언어 별 메모리 관리 기법

* 자바의 GC (Garbage Collector)
  * 런타임에 힙 영역을 자동으로 스캔해서 알아서 관리해줌
  * 스캔하는 속도가 있어서 느려짐! (언제 어느 시점에 이루어지는지는 알 수 없음)
* objective - c
  * 참조 숫자(RC)를 세는 것을 통해 메모리 관리 / 컴파일 시 메모리 해제시점 결정
  * 수동으로 숫자 세는 것이 MRC (개발자가 수동으로 계산)
  * 자동으로 숫자 세는 것이 ARC
    * 컴파일러가 알아서 코드를 심어줌 → 메모리 관리를 알아서 해주는게 아니고 숫자를 제대로 세 준다는것임! 메모리 해제가 잘 되도록, 이론을 잘 알고 사용해야함.
* Swift
  * ARC (Automatic RC)
    * 메모리를 수동으로 관리할 수 없다.



### ARC 작동 원리

* 클래스의 인스턴스가 생성될 때마다 ARC는 인스턴스에 대한 정보를 저장한다.
  * 인스턴스, 관련 프로퍼티 값, 인스턴스 타입에 대한 정보를 저장한다.
* 인스턴스가 더이상 필요하지 않으면, ARC는 사용된 메모리를 할당 해제한다. → 클래스 인스턴스가 필요하지 않을 때 메모리 공간을 차지하지 않는다.
  * 인스턴스에 참조가 하나라도 존재한다면, ARC는 메모리를 할당 해제하지 않는다. → 이를 위해 클래스 인스턴스를 할당할 때마다 강한 참조를 만들게 된다. 참조는 해당 인스턴스를 유지하고, 강한 참조가 남아있는 한 할당 해제를 허용하지 않기 때문에 강한 참조라고 부른다.



### ARC와 MRC

1. ARC(Automatic RC)

```swift
class Point {
    var x, y: Double
    func draw() {}
}

let point1 = Point(x: 0, y: 0)
let point2 = point1
point2.x = 5
//use point1
//use point2

```

2. MRC

```swift
class Point {
    **var refCount: Int**
    var x, y: Double
    func draw() {}
}

let point1 = Point(x: 0, y: 0)
let point2 = point1
**retain(point2)**
point2.x = 5
//use point1
**release(point1)**
//user point2
**release(point2)**
```

* 이전 언어들은 MRC와 같이 메모리를 수동으로 관리했다.
* 현대 언어의 경우 자동 메모리 관리 모델을 사용한다.
  * 컴파일러가 retain() ⇒ release() 해제 코드를 삽입한다고 생각
* 이전 언어들은 메모리를 수동 관리했음 (MRC)
* 현대 언어의 경우 자동 메모리 관리 모델 사용
  * Swift는 컴파일러가 `retain()`할당 코드 → `release()` 해제 코드를 삽입한다
  * 메모리 관리에 대한 실수를 방지하여 프로그램의 안정성이 증가된다.



### ARC 기반

* 나를 가리키는 소유자가 없으면 해제된다는 원칙을 가지고 있다.

1. 소유 정책
   * 하나 이상의 소유자가 있는 경우 메모리에 유지됨
2. 참조 카운팅
   * 나를 가리키는 소유자 카운팅



### ARC 동작

* 어떻게 자동 참조 카운팅이 동작하는가?

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}

var soi: Person? = Person(name: "지원") // retain(soi), RC: 1
var reference: Person?
var reference = Person(name: "John") // retain(reference), RC: 2
soi = nil // release(soi), RC: 1
reference = nil // release(reference), RC: 0
```

* 예전 언어의 경우 모든 메모리를 개발자들이 수동 관리했다. (retain, release)
* 현대적 언어는 대부분 자동 메모리 관리 모델을 사용하며, Swift의 경우 컴파일러가 retain() → release()코드를 삽입한다고 보면된다.
  * 프로그램 메모리 관리에 대한 안정성이 증가했다.

## 참조 사이클과 메모리 누수

### 강한 참조 사이클이란?

* 클래스의 저장속성에 특정 인스턴스를 **서로 참조**하는 것이다.
  * Stack 영역에서는 사라졌지만, RC가 1로 저장되어있어 **메모리 누수**가 발생한다.
* 즉, 객체가 서로를 참조하는 강한 참조 사이클
  * 변수의 참조에 nil을 할당해도, 해제가 되지 않는 메모리 누수가 발생한다.

```swift
class Dog {
    var name: String
    var owner: Person

}

class Person {
    var name: String
    var pet: Dog?
}

var bory: = Dog? = Dog(name: "보리")
var gildong: Person? = Person(name: "길동)

// 서로가 서로를 가리킴: **강한 참조 사이클 (Strong Reference Cycle) 발생**
bori?.owner = gildong
gildong?.pet = bori

// 이렇게 하면 해결할 수 있음, but 생각하기 쉽지 않음
// bori?.owner = nil
// gildong?.pet = bory 

// **메모리 누수 발생**
bori = nil
gildong = nil
```



### 해결 방안

* 가리키는 인스턴스의 RC의 숫자를 올라가지 않게 하는 방법을 사용할 수 있다.
  * 내가 가리키는 서로의 값을 RC에 적용시키지 않는다.
  * 강한 참조 사이클을 만들지 않고도 서로를 참조할 수 있다.

1. **약한참조 (`Weak Reference`)**
   *   소유자에 비해 보다 **짧은 생명주기를 가진 인스턴스를 참조**할 때 주로 사용

       (다른 인스턴스의 수명이 짧은 경우. 즉, 다른 인스턴스가 먼저 할당 해제될 수 있을 때)
   *   값이 없어지면 nil을 자동 할당

       인스턴스가 할당 해제되면, 자동으로 nil로 약한 참조로 설정한다.

       참고) 이 때는 속성감시자가 호출되지 않는다.
   *   상수로 선언할 수 없다.

       런타임에 값을 nil로 변경하는 것을 허락해야하기 때문에 항상 변수로 선언된다.
2. **미소유 참조 (`Unowned Reference`)**
   * RC를 고려하여 참조 해제 순서를 주의해야함.
   * Swift 5.3 버전 이후, **옵셔널 선언이 가능**
   * 값이 없어져도 nil 자동할당 하지 않음
   * 소유자보다 인스턴스의 생명주기가 더 길거나, 같은 경우 사용
     * 항상 A 객체가 더 짧게 살 때 A는 비소유 참조 사용
     * 참조가 항상 할당 해제되지 않는 인스턴스를 참조한다고 확신하는 경우에 사용하는 것
     * 인스턴스가 할당 해제된 후에 미소유 참조의 값에 접근하려고하면, 런타임 에러 발생

* 공통점
  * 가리키는 인스턴스의 RC를 올라가지 않게 함 (인스턴스 사이의 강한 참조를 제거한다.)
  * 인스턴스에 접근은 가능하지만, 인스턴스를 유지시키는 것은 불가능하다.
* 차이점
  * weak : 소유자에 비해 보다 짧은 생명주기를 가진 인스턴스를 참조할 때 사용한다.
  *   unwoned : 소유자보다 인스턴스의 생명주기가 더 길거나 같은 경우에 사용한다.

      → 실제로는 weak를 더 많이 사용한다. (비소유참조는 한번 더 고려해야하니까)
* `unowned`가 조금 더 빠름!
  * 실제로는 unowned는 코드를 더 작성해야하기때문에 weak을 더 많이 사용하기는 함

### 예제 코드

1.  약한참조

    ```swift
    class Dog {
        var name: String
        **weak** var owner: Person? // weak 키워드 -> 약한 참조
    }

    class Person {
        var name: String
        **weak** var pet: Dog
    }
    ```

    * **약한참조**는 참조하고 있던 인스턴스가 사라지면, 해당 변수를 nil을 자동으로 할당한다.
2.  비소유 참조 (Unowned reference)

    ```swift
    class Dog {
        var name: String
        **unowned** var owner: Person? // swift 5.3 이전에는 옵셔널 타입이 불가능했었음.
    }

    class Person {
        var name: String
        **unowned** var pet: Dog? // 한 쪽만 Unowned로 선언해도 됨
    }

    gildong = nil
    **bori.owner = nil** // 이렇게 할당해줘야돼

    ```

    *   비소유 참조의 경우, 참조하고 있던 인스턴스가 사라지면 nil로 초기화되지 않음.

        → nil로 할당하는 코드를 추가해야한다. (안 하면 앱이 종료됨)
    * 참조하고 있던 인스턴스가 사라지면, nil로 초기화되지 않는다.

    ```swift
    // 에러 발생하는 케이스
    gildong = nil
    bori.owner // nil로 초기화되지 않아서 에러가 발생함

    // 해결하는 방법
    gildong = nil
    bori.owner = nil // nil로 재설정 필요
    bori.owner // 접근 가능
    ```

## 객체 내에서 클로저의 사용

💡 (요약) 클로저에 대한 강한 참조 사이클

*   클래스 인스턴스 속성에 클로저 할당 후, 클로저에서 인스턴스 캡처하는 경우 발생

    → self를 캡처하여 강한 참조 사이클을 생성하게 된다.

    → 클로저도 클래스와 같은 참조 타입이기 때문이다!!

### 일반적인 클로저의 사용

* 클로저 내에서 객체의 속성 및 메서드에 접근할 때 `**self**` 키워드 반드시 사용해야한다.
  * 강한 참조를 하고 있다는 것을 표시하기 위해서이다.
  * 구조체의 경우, self 생략이 가능하다.
  *   Swift5.3 이후 \[self]로 할 수 있음

      ```swift
      self.name
      [self]
      ```
* 쓰레드를 여러개 쓰는 경우, 새로운 Stack을 만들게 된다.
  * 연관된 일을 처리할 수 있는 것들을 Stack 내에서만 존재! 각 쓰레드 별 Stack
  * 그런데, code, data, heap은 Stack끼리 다같이 공유한다!

```swift
class Dog {
    var name = "choco"
    
    func doSomething() { // **클래스 내 클로저**
    
        // 2번 CPU에서 동작하는 코드라고 생각, 일의 분리, 동시 일 처리 (비동기적으로 실행되는 클로저)
        // 2번 쓰레드는 오랫동안 저장해야함 (새로운 스택을 만들어서 실행하므로)
        DispatchQueue.global().async { // 강한참조 사이클이 일어날 수 있음.
            print("my name is \(**self.name**)")
        }
    
        // DispatchQueue.global().async { **[self]** in 
        //   print("my name is \(**name**)")
        // }
    }
}

var choco = Dog()
choco.doSomething()

//만약 강한참조 사이클이 일어난다면
DispatchQueue.global().async { [weak self] in // 강한참조 사이클이 일어날 수 있음.
    print("my name is \(**name**)")
}
```



### 클로저의 캡처리스트 - 강한 참조 사이클 해결 (strong reference cycle)

* 클로저는 기본적으로 캡처 현상이 발생한다.
* 클로저와 인스턴스가 강한 참조로, 서로 가리키고 있는 것 = Strong Reference Cycle
  * 메모리에서 정상적으로 해제되지 않고, 메모리 누수 현상이 발생한다.
* 캡처 리스트 내에서 약한 참조, 또는 비소유 참조를 선언해서 문제를 해결한다.
  * 코드 간 관계에 따라 선택해야한다. (보통 weak과 사용한다)
