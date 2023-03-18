### 목차
- [복잡도](#복잡도)
    - 시간복잡도(#시간복잡도)
    - 빅오표기법(#빅오표기법)
    - 공간복잡도(#공간복잡도)

---

### 시간복잡도

- 시간복잡도는 입력크기에 대해 어떤 알고리즘이 실행되는데에 걸리는 시간이며, 주요 로직의 반복횟수를 중점으로 측정된다.

- 실제 코드의 실행시간은 컴퓨터 사양 등 여러가지 요소에 영향을 받는다.
    - 이에 주어진 입력 크기를 기반으로, 어떤 로직이 몇 번 반복되었는지를 중점으로 계산한다.

- 시간복잡도는 왜 있는것인가? → 효율적인 코드로 개선하는데 쓰이는 척도가 된다.
    - 코테 문제를 보고, 조금 더 작은 시간복잡도의 알고리즘을 써야겠구나를 인지할 수 있어야함! (생각의 기준점)



### 빅오 표기법

- 복잡도에 가장 영향을 많이 끼치는 항의 상수 인자를 빼고, 나머지 항을 없애서 복잡도를 나타내는 표기법이다.
- 상수 시간 시간복잡도 O(1)
    - 입력 크기와 상관 없이 일정한 시간 복잡도를 갖는 것이다.
    - 입출력 / 사칙연산 / 조건문 / 인덱스 참조 등이 해당한다.

- 재귀함수의 시간복잡도
    - 함수 내에서 함수가 몇 번 호출했는지에 따라 결정된다. (n^3)
    
    
    ```swift
    var count = 0
    
    func solve(n: Int) {
        count += 1
        print(count)
    
        if n == 0 { return }
        for i in range 0..<3 {
            solve(n - 1)
        }
        return
    }
    
    solve(n)
    ```


### 공간복잡도
- 입력 크기에 대해 어떠한 알고리즘이 실행되는데 필요한 메모리 공간이 양으로, 재귀함수로 인해 공간을 지속적으로 필요한 경우도 포함된다.