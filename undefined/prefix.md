# 누적합(Prefix)

#### 누적 합

* 요소들의 누적된 합으로, 어떠한 배열을 기반으로 앞에서부터 요소들의 누적된 합을 저장한 배열을 만들어서 활용하는 것이다.

**예시문제**

승철이는 뇌를 잃어버렸다. 학교에 갔더니 선생님이 자연수로 이루어진 N개의 카드를 주며 M개의 질문을 던진다. 그 질문은 나열한 카드 중 A번째부터 B번째까지의 합을 구하는 것이다. 뇌를 잃어버렸기 때문에 승철이는 이 문제를 풀 수 없다. 문제를 풀 수 있는 프로그램을 작성해보자.

**입력**

수의 개수 N, 합을 구해야 하는 횟수 M, 그 이후 N개의 수가 주어진다. 수는 100 이하의 자연수. 그 이후 M개의 줄에는 합을 구해야 하는 구간 A, B가 주어진다.

**출력**

M개의 줄에 A부터 B까지의 합을 구하라.

**범위**

1 <= N <= 100,000

1 <= M <= 100,000

1 <= A <= B <= N

**\[출처]** [\[알고리즘 강의\] 1주차. 시간복잡도, 빅오표기법, 공간복잡도, 누적합, 구현](https://blog.naver.com/jhc9639/222283814653)|**작성자** [큰돌](https://blog.naver.com/jhc9639)

```swift
var cards = [0]
var prefix = [0]
let input = readLine()!.split(separator: " ").map { Int($0)! }
let (n, m) = (input[0], input[1])
cards.append(contentsOf: readLine()!.split(separator: " ").map { Int($0)! })

for i in 1...n {
    prefix.append(cards[i] + **prefix[i-1]**)
}

for _ in 1...m {
    let index = readLine()!.split(separator: " ").map { Int($0)! }
    let (a, b) = (index[0], index[1])
    print(prefix[b] - prefix[a-1])
}
```

#### Swift의 prefix 내장함수

```swift
print([1, 2, 3, 4, 5, 6].prefix(3))
```

* Collection이 Random access collection을 준수하면, O(1)이고 그렇지 않으면 O(k), k는 컬렉션의 시작부분에서 선택할 요소의 개수
