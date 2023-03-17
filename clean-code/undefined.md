---
description: 객체지향적으로 리팩토링하며 이해해보자
---

# 객체지향

## 리팩토링 전 코드

{% hint style="info" %}
사용자가 상점에서 상품을 구매하는 코드이다.

사용자와 상점은 돈을 가지고 있고, 거래가 일어나면 돈을 주고 받는다.
{% endhint %}

#### 상점 코드

```swift
import Foundation

class SoiStore {
    var money: Int = 0
    var name: String = "쏘이네 가게"
    var products: [Int: [String: Any]]
    
    init() {
        self.money = 0
        self.products = {
            [
            1: ["name": "키보드", "price": 30000],
            2: ["name": "모니터", "price": 50000]
            ]
        }()
    }
    
    func setMoney(_ money: Int) {
        self.money = money
    }
    
    func setProducts(_ products: [Int: [String: Any]]) {
        self.products = products
    }
    
    func getMoney() -> Int {
        return self.money
    }
    
    func getProducts() -> [Int: [String: Any]] {
        return self.products
    }
}
```

#### 손님 코드

<pre class="language-swift"><code class="lang-swift"><strong>import Foundation
</strong><strong>
</strong><strong>class User {
</strong>    var money: Int
    var store = SoiStore()
    var belongs: [Int: [String: Any]]
    
    init() {
        self.money = 0
        self.belongs = [:]
    }
    
    func setMoney(_ money: Int) {
        self.money = money
    }
    
    func setBelongs(belongs: [Int: [String: Any]]) {
        self.belongs = belongs
    }
    
    func getMoney() -> Int {
        self.money
    }
    
    func getBelongs() -> [Int: [String: Any]]{
        self.belongs
    }
    
    func getStore() -> SoiStore {
        self.store
    }
    
    func seeProduct(_ productID: Int) -> [String: Any]? {
        let products = self.store.getProducts()
        return products[productID]
    }
    
    func purchaseProduct(_ productID: Int) throws -> [String: Any] {
        
        guard let product = self.seeProduct(productID) else {
            throw NSError(domain: "상품이 없습니다", code: 0)
        }
        
        if self.money >= product["price"] as? Int ?? 0 {
            self.store.products.removeValue(forKey: productID)
            self.money -= product["price"] as? Int ?? 0
            self.store.money += product["price"] as? Int ?? 0
            self.belongs[productID] = product
            return product
        } else {
            throw NSError(domain: "잔돈이 부족합니다", code: 0)
        }
    }
}
</code></pre>

#### 실행 코드

```swift
let user = User()
user.setMoney(100000)
do {
    try user.purchaseProduct(1)
} catch {
    print(error.localizedDescription)
}
print(user.belongs)
print(user.store.getProducts())
```



## 리팩토링 1 - 추상화

{% hint style="danger" %}
지금은 SoiStore하나만 사용하고 있다. 그런데 JiwonStore, AppleStore 등 상점이 여러개가 될 수 있다.

상점이 여러 개가 생긴다면, 추상화와 의존성 주입이 필요하다.
{% endhint %}

#### 추상화

```swift
protocol Store {
    var money: Int { get set }
    var products: [Int: [String: Any]] { get set }
    
    func setMoney(_ money: Int)
    func setProducts(_ products: [Int: [String: Any]])
    func getMoney() -> Int
    func getProducts() -> [Int: [String: Any]]
}
```

#### 상점 코드

변경사항 없으며, JiwonStore가 새로 생겼다. (형채는 SoiStore와 동일)

#### 사용자 코드

```swift
class User {
    var money: Int
    var store: Store // 의존성 주입
    var belongs: [Int: [String: Any]]
    
    init(store: Store) {
        self.money = 0
        self.store = store
        self.belongs = [:]
    }
    
    func setMoney(_ money: Int) {
        self.money = money
    }
    
    func setBelongs(belongs: [Int: [String: Any]]) {
        self.belongs = belongs
    }
    
    func getMoney() -> Int {
        self.money
    }
    
    func getBelongs() -> [Int: [String: Any]]{
        self.belongs
    }
    
    func getStore() -> Store {
        self.store
    }
    
    func seeProduct(_ productID: Int) -> [String: Any]? {
        let products = self.store.getProducts()
        return products[productID]
    }
    
    func purchaseProduct(_ productID: Int) throws -> [String: Any] {

        guard let product = self.seeProduct(productID) else {
            throw NSError(domain: "상품이 없습니다", code: 0)
        }

        if self.money >= product["price"] as? Int ?? 0 {
            self.store.products.removeValue(forKey: productID)
            self.money -= product["price"] as? Int ?? 0
            self.store.money += product["price"] as? Int ?? 0
            self.belongs[productID] = product
            return product
        } else {
            throw NSError(domain: "잔돈이 부족합니다", code: 0)
        }
    }
}
```

#### 실행코드

```swift
let user = User(store: SoiStore())
user.setMoney(100000)
do {
    try user.purchaseProduct(1)
} catch {
    print(error.localizedDescription)
}
print(user.belongs)
print(user.store.getProducts())

let user2 = User(store: JiwonStore())

```

## 리팩토링 2 - 캡슐화

{% hint style="danger" %}
현재 store의 돈과 products를 User 클래스에서 직접 조작하고 있다. (Store의 속성을 User에서 접근하고 있는 상황)

User가 상품에 대한 정보를 알고 있을수록 결합도가 높아지게 된다.

중요한 속성을 숨기고, 메서드로 접근할 수 있도록 캡슐화해야한다.&#x20;

#### 해결방법

1. Store의 책임을 정의하고 캡슐화한다.
2. User의 결제 로직을 수정한다.
3. User를 캡슐화한다.&#x20;
{% endhint %}

#### Store의 책임 재정의

1. Store는 사용자가 요구한 제품을 보여줄 수 있어야한다.
2. Store는 사용자에게 물건을 줘야한다.
3. Store는 사용자에게 돈을 받아야한다.&#x20;

#### 상점 프로토콜

```swift
// 추상화
protocol Store {   
    func showProduct(_ productID: Int) -> [String: Any]?
    func giveProduct(_ productID: Int)
    func takeMoney(_ money: Int)
}
```



#### 상점 코드

```swift
class SoiStore: Store {
    
    private var money: Int = 0
    var name: String = "쏘이네 가게"
    private var products: [Int: [String: Any]] // 의존성 주입
    
    init(products: [Int: [String: Any]]) {
        self.money = 0
        self.products = products
    }
    
    func showProduct(_ productID: Int) -> [String : Any]? {
        products[productID]
    }
    
    func giveProduct(_ productID: Int) {
        if let _ = products[productID] {
            products.removeValue(forKey: productID)
        }
    }
    
    func takeMoney(_ money: Int) {
        self.money += money
    }
    // Money, Product는 외부에서 수정할 수 없어야한다. -> private으로 선언하며, protocol에는 기재하지 않는다.
}
```

#### 사용자 코드

```swift
class User {
    private var money: Int
    private var store: Store // 의존성 주입
    private var belongs: [Int: [String: Any]]
    
    init(money: Int, store: Store) {
        self.money = money
        self.store = store
        self.belongs = [:]
    }
    
    func getStore() -> Store {
        self.store
    }

    func seeProduct(_ productID: Int) -> [String: Any]? {
        return store.showProduct(productID)
    }
    
    func purchaseProduct(_ productID: Int) throws {

        guard let product = store.showProduct(productID) else {
            throw NSError(domain: "재고가 없습니다.", code: 0)
        }

        guard let price = product["price"] as? Int, price <= self.money else {
            throw NSError(domain: "돈이 부족합니다", code: 0)
        }
        self.money -= price
        store.takeMoney(price)

        store.giveProduct(productID)
        self.belongs[productID] = product
    }
    
    func getBelongs() -> [Int: [String: Any]] {
        self.belongs
    }
}
```

#### 실행코드

```swift
let user = User(money: 100000,
                store: SoiStore(products:  [
                    1: ["name": "키보드", "price": 30000],
                    2: ["name": "모니터", "price": 50000]
                ]))

do {
    try user.purchaseProduct(1)
} catch {
    print(error.localizedDescription)
}

print(user.getBelongs())
```

#### 헷갈렸던 부분

* 프로토콜 : 객체의 기본 규약
  * setMoney라는 함수가 있다고 가정한다면 이것은 프로토콜에 추가해서는 안 된다.
  * 객체의 기본 규약을 정의하는 것이기 때문에 당연히 적용되어야된다고 생각했었다. 하지만 프로토콜로 선언하면 private을 쓸 수 없다. 또한 외부 객체가 알 필요 없는 내부 로직이나 구현 방식에 대한 정보는 포함시키지 않는 것이 맞다. (캡슐화와 정보 은닉)



## 리팩토링 3 - 단일 책임

{% hint style="danger" %}
현재 User의 purchaseProduct에서 많은 행위를 책임지고 있다.

물건 판매 책임을 Store로 위임하여 결합도를 낮춰보자.

이에 상품을 판매하는 행위를 추상화하고, 구체적인 로직을 작성해야한다.&#x20;
{% endhint %}

#### 상점 프로토콜

<pre class="language-swift"><code class="lang-swift"><strong>// store의 역할은 물건을 보여주는 것과 판매하는 것이다.
</strong><strong>protocol Store {
</strong>    func showProduct(_ productID: Int) throws -> [String: Any]
    func sellProduct(_ productID: Int, money: Int) throws
}
</code></pre>

#### 상점

```swift
class SoiStore: Store {
    private var money: Int = 0
    var name: String = "쏘이네 가게"
    private var products: [Int: [String: Any]]
    
    init(products: [Int: [String: Any]]) {
        self.money = 0
        self.products = products
    }
    
    // 물건을 보여주는 역할
    func showProduct(_ productID: Int) throws -> [String : Any] {
        guard let product = products[productID] else {
            throw NSError(domain: "재고가 없습니다", code: 0)
        }
        return product
    }
     
    // 물건을 판매하는 역할
    func sellProduct(_ productID: Int, money: Int) throws {
        guard products[productID] != nil else {
            throw NSError(domain: "재고가 없습니다.", code: 0)
        }
        takeMoney(money)
        takeOutProduct(productID)
    }
    
    private func takeMoney(_ money: Int) {
        self.money += money
    }
    
    private func takeOutProduct(_ productID: Int) {
        products.removeValue(forKey: productID)
    }
}
```

#### 손님

```swift
class User {
    private var money: Int
    private var store: Store
    private var belongs: [Int: [String: Any]]
    
    init(money: Int, store: Store) {
        self.money = money
        self.store = store
        self.belongs = [:]
    }
    
    func getStore() -> Store {
        self.store
    }

    func getBelongs() -> [Int: [String: Any]] {
        self.belongs
    }
    
    func seeProduct(_ productID: Int) -> [String: Any]? {
        try? store.showProduct(productID)
    }
    
    // 판매의 역할은 store에서 처리, 구매만 user가 수행
    func purchaseProduct(_ productID: Int) throws {
        guard let product = try? store.showProduct(productID),
              let price = product["price"] as? Int
        else { return }
        
        guard money >= price else {
            throw NSError(domain: "잔액이 부족합니다", code: 0)
        }
        
        do {
            giveMoney(price)
            try store.sellProduct(productID, money: price)
            addBelong(productID, product: product)
        } catch {
            takeMoney(price)
        }
        
    }
    
    private func giveMoney(_ money: Int) {
        self.money -= money
    }
    
    private func takeMoney(_ money: Int) {
        self.money += money
    }
    
    private func addBelong(_ id: Int, product: [String: Any]) {
        self.belongs[id] = product
    }
    
}
```
