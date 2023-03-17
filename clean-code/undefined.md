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
