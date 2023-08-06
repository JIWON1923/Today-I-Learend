# 기초 알고리즘

#### 목차

* [수학](undefined-1.md#수학)
  * 팩토리얼
  * 피보나치
  * 순열
  * 조합
  * 최대공약수
  * 최소공배수
  * 소수찾기



#### 수학

*   팩토리얼

    ```swift
    // 재귀함수
    func factorial(_ n: Int) -> Int {
        if n <= 1 { return 1 }
        return n * factorial(n - 1)
    }

    // 반복문
    func factorial(_ n: Int) -> Int {
        var result = 1
        for value in 1...n {
            result *= value
        }
        return result
    }
    ```
*   피보나치 수열

    * 반복문으로 구현하기 어렵다. (DP로 하면 반복문으로 구현 가느)

    ```swift
    func fibo(_ n: Int) -> Int {
        if n <= 1 { return n }
        return fibo(n-1) + fibo(n-2)
    }
    ```
*   순열

    ```swift
    var v = [1, 2, 3]

    func permutation(n: Int, r: Int, depth: Int) {
        if r == depth {
            print( v.map { String($0) }.joined(separator: " "))
            return
        }
        
        for i in depth..<n {
            v.swapAt(i, depth)
            permutation(n: n, r: r, depth: depth + 1)
            v.swapAt(i, depth)
        }
        return
    }

    permutation(n: 3, r: 3, depth: 0)
    ```
*   조합

    1. 재귀를 이용한 조합

    ```swift
    let n = 5, r = 3, a = [1, 2, 3, 4, 5]

    func combi(start: Int, list: [Int]) {
        if list.count == r {
            print(list.map { String($0) }.joined(separator: " "))
            return
        }

        var tempList = list
        for i in start + 1 ..< n {
            tempList.append(i)
            combi(start: i, list: tempList)
            tempList.removeLast()
        }
        return
    }

    combi(start: -1, list: []) // for문이 start + 1부터 시작함!
    ```

    2. 반복을 이용한 조합

    ```swift
    let n = 5, r = 3, a = [1, 2, 3, 4, 5]

    // r=3이므로 3중 for문 -> r이 작을 때에만 반복문을 사용하자
    for i in 0..<n {
        for j in i+1 ..< i {
            for k in j+1 ..< j {
                print("\(i), \(j), \(k)
            }
        }
    }

    // 순서만 다를 뿐
    for i in 0..<n {
        for j in 0..<i {
            for k in 0..<j {
                print("\(i), \(j), \(k)
            }
        }
    }
    ```

#### 최대공약수

*   유클리드 호제법 사용 : gcd(a, b) = gcd(b, r)

    ```swift
    func gcd(_ a: Int, _ b: Int) -> Int {
        if b == 0 { return a }
        return gcd(b, a%b)
    }
    ```

#### 최소공배수

*   최소공배수 = num1 \* num2 / 최대공약수

    ```swift
    func gcd(_ a: Int, _ b: Int) -> Int {
        if b == 0 { return a }
        return gcd(b, a%b)
    }

    func lcm(_ a: Int, _ b: Int) -> Int {
        return a * b / gcd(a, b)
    }
    ```

#### 소수 찾기

*   에라토스테네스의 체 → n이 백만 이하

    ```swift
    func primeNumbers(_ n: Int) -> [Int] {
      var numbers = [Bool](repeating: true, count: n+1)
      var result = [Int]()

      for i in 2...n {
          if numbers[i] {
              for j in stride(from: i*2, to: n, by: i) {
                  numbers[j] = false
              }
          }
      }

      for i in 2...n {
          if numbers[i] { result.append(i) }
      }

      return result
    }
    ```

    ```swift
    func primeNumbers(_ n: Int) -> Set<Int> {
      var numbers = Set(2...n)

      for i in 2...n {
          if numbers.contains(i) {
              numbers.subtract(Set(stride(from: i*2, to: n, by: i)))
          }
      }
      return numbers
    }
    ```
*   제곱근 이용

    ```swift
    func isPrimeNumber(_ n: Int) -> Bool {
        if n < 2 { return false }
        if n == 2 { return true }
        if n % 2 == 0 { return false }
        
        for i in 2...n {
            **if !(i * i <= n) { break }**
            if n%i == 0 { return false }
        }
        
        return true
    }
    ```

    ```swift
    **import Foundation**

    func isPrimeNumber(_ n: Int) -> Bool {
        if n < 4 { return n >= 2 }
        if n % 2 == 0 { return false }
        
        for i in 2...**Int(sqrt(Double(n)))** {
            if n % i == 0 { return false }
        }
        return true
    }
    ```
