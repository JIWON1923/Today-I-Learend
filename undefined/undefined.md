# 그래프

### 그래프이론

#### 정점과 간선

* 정점 (vertex)
  * 노드라고 불리며 그래프를 형성하는 기본 단위이다.
  * 분할할 수 없는 객체이자, 점으로 표현되는 점, 위치, 사람 등이 될 수 있다.
* 간선 (Edge)
  * 정점을 잇는 선으로, 관계 경로 등이 될 수 있다.
  * 간선에 방향에 따라 단방향간선, 양방향 간선으로 구분할 수 있다.
* 그래프 (Graph)
  * 정점과 간선의 집합

#### indegree와 outdegree

* indgree : 정점으로 들어오는 간선
* outdegree : 정점으로 나가는 간선
* 보통 정점은 약자로 V 또는 U라고 표시하며 U(From)로부터 V(To)로 간다고 표현한다.

#### 가중치

* 노드에서 노드까지의 비용
  * 즉, 가중치 = 간선의 비용의 합

### 트리

#### 트리자료구조

* 자식 노드와 부모노드로 이루어진 **계층 구조**
* **무방향** 그래프의 일종이자 **사이클이 없다.**

#### 트리의 특징

* 그래프의 일종이다.
  * 그래프는 정점과 간선의 집합
* 부모, 자식 계층 구조를 가진다.
  * 같은 경로 상, 특정 노드보다 위에있으면 부모, 아래 있으면 자식 노드가 된다.
* V - 1 = E라는 특징이 있다.
  * 정점 : Vertex
  * 간선 : Edge
  * 즉, 간선 수 = 노드 수 - 1
* 임의의 두 노드 사이의 경로는 유일무이하게 존재한다.
  * 즉, 트리 내 특정 노드까지의 경로는 반드시 존재하며, 하나만 존재한다.
* 루트, 내부, 리프노드로 구성된다.
  * 문제를 풀 땐 항상 루트노드가 무엇인지 생각하고, BFS, DFS를 판단하자.

#### 트리의 높이와 레벨

* 트리의 높이
  * 루트노드부터 최하단 리프노드까지의 길이
* 서브트리
  * 트리 내에 있는 트리 (부분 집합)
* 깊이
  * 각 노드마다 다르며, 로트 노드에서 특정 노드까지 최단 거리로 갔을 때의 거리
* 레벨
  * 보통 깊이와 같은 의미를 지닌다.
  * 루트노드의 시작 레벨에 따라 상대적으로 결정된다.
    * 1번 노드가 0레벨이라면, 2번노드는 1
    * 1번 노드가 1레벨이라면, 2번노드는 2
* 숲
  * 트리로 이루어진 집합

### 이진트리와 이진탐색트리

#### 이진트리 (BT, BinaryTree)

* 각 노드의 **자식 노드 수가 2개 이하**로 구성되어있는 트리이다.
  * 정이진 트리(full binary tree) : 자식노드가 0개 또는 2개
  * 완전 이진 트리 (complete binary tree) : 왼쪽부터 채워져있는 트리로, 마지막 레벨을 제외하고는 완전히 채워져있으며, 마지막 레벨은 왼쪽부터 채워진다.
  * 변질 이진 트리 (degenerate binary tree) : 자식 노드가 하나밖에 없는 이진 트리
  * 포화 이진 트리 (Perfect binary tree) : 모든 노드가 가득 차 있는 이진트리
  * **균형 이진 트리** (balanced binary tree) : 모든 노드의 **왼쪽 하위 트리와 오른쪽 하위 트리의 차이가 1 이하인 트리**이다. Map, set을 구성하는 레드블랙트리는 균형 이진트리 중 하나이다.

#### 이진 탐색 트리 (BST, Binary Search Tree)

* 이진 트리의 일종으로, 오른쪽 하위 트리에는 노드의 값보다 큰 값이 있는 노드만 포함되고, 왼쪽 하위트리에는 노드의 값보다 작은 값이 들어있는 트리이다.
* 검색하기 굉장히 유리하다.
  * 10을 찾는 과정에서 25보다 10이 작으므로, 왼쪽 노드를 탐색한다. → 20보다 작으므로 왼쪽 노드로 탐색한다 → 10을 찾을 수 있다.
  * Up and down도 binary search tree에 들어간다.
* 이진탐색트리의 시간복잡도
  * 위 사진처럼 균형잡히게 분포되었다면 탐색, 삽입, 삭제, 수정 모두 O(logN)이다.
* 이진 탐색 트리의 모양은 노드 삽입 순서에 따라 달라진다. 1, 2, 3 순서로 삽입이 된다면 선형적 자료구조가 된다.
* 삽입 순서에 상관 없이 트리의 노드를 회전시키는 방법 등을 통해 균형 잡게 만드는 것을 **AVL 트리, 레드블랙트**리가 있다.
  * map 자료구조는, 삽입 탐색 삭제 수정의 시간복잡도가 O(logN)임을 보장받는데, 그 이유가 균형잡힌 트리인 레드블랙트리를 기반으로 구현되어있기 때문이다.

### 그래프 구현과 탐색

💡 그래프를 컴퓨터가 이해할 수 있도록 표현하는 방법

#### 인접행렬

* 인접해있다 = 연결되어있다라고 생각하자.
* 인접행렬
  * 그래프에서 정점과 간선의 관계를 나타내는 Bool 타입의 정사각형 행렬이다.
  * 정사각형의 행렬의 요소가 0 또는 1이라는 값으로 가짐을 의미한다.
    * 0 : 두 정점 사이의 경로가 없다.
    * 1 : 두 정점 사이의 경로가 있다.

💡 \*\*Q. 인접행렬을 기반으로 탐색하기\*\*

1번. 정점은 0번 부터 9번까지 10개의 노드가 있다. 1 - 2 / 1 - 3 / 3 - 4 라는 경로가 있다. (1번과 2번, 1번과 3번, 3번과 4번은 연결되어있다.) 이를 이를 인접행렬로 표현한다면?

2번. 0번부터 방문안한 노드를 찾고 해당 노드부터 방문, 연결된 노드를 이어서 방문해서 출력하는 재귀함수를 만들고 싶다면 어떻게 해야할까? 또한, 정점을 방문하고 다시 방문하지 않게 만드려면 어떻게 해야할까?

**\[출처]** [\[알고리즘 강의\] 2주차. 그래프이론, 인접행렬, 인접리스트, DFS, BFS, 트리순회](https://blog.naver.com/jhc9639/222289089015)|**작성자** [큰돌](https://blog.naver.com/jhc9639)

```swift
// 1. 0번부터 9번까지 10개의 노드가 있다.
var graph = [[Bool]](repeating: [Bool](repeating: false, count: 10), count: 10)
var visited = [Bool](repeating: false, count: 10)

// 2. 1-2 / 1-3 / 3 -4 라는 경로가 있다. 이를 인접 행렬로 표현한다면?
graph[1][2] = true
graph[2][1] = true
graph[1][3] = true
graph[3][1] = true
graph[3][4] = true
graph[4][3] = true

//4. 0번부터 방문 안 한 노드를 찾고, 해당 노드부터 방문한다.
for i in 0..<10 {
    for j in 0..<10 {
        if graph[i][j] && !visited[i] {
                visiteNode(from: i)
        }
    }
}

func visitNode(from: Int) {
    
    //5. 연결된 노드를 이어 방문하여 출력하는 재귀함수를 만들자.
    //6. 한 번 방문한 정점에는 다시 방문하지 안게 하려면 어떻게해야할까?
    visited[from] = true
    print(from)
    for i in 0..<10 {
        if visited[i] { continue }
        if graph[from][i] {
            visitNode(from: i)
        }
    }
    return
}
```

#### 인접리스트 (adjancency list)

* 연결리스트를 여러개 만들어서 표현하는 방법이다.
  * 보통 adj라고 나타낸다.
* 각 정점마다 연결리스트를 만들고, 해당 리스트에 연결된 정점만 넣는 방식이다.

💡 \*\*Q. 인접행렬을 기반으로 탐색하기\*\*

1번. 정점은 0번 부터 9번까지 10개의 노드가 있다. 1 - 2 / 1 - 3 / 3 - 4 라는 경로가 있다. (1번과 2번, 1번과 3번, 3번과 4번은 연결되어있다.) 이를 이를 인접리스트로 표현한다면?

2번. 0번부터 방문안한 노드를 찾고 해당 노드부터 방문, 연결된 노드를 이어서 방문해서 출력하는 재귀함수를 만들고 싶다면 어떻게 해야할까? 또한, 정점을 방문하고 다시 방문하지 않게 만드려면 어떻게 해야할까?

**\[출처]** [\[알고리즘 강의\] 2주차. 그래프이론, 인접행렬, 인접리스트, DFS, BFS, 트리순회](https://blog.naver.com/jhc9639/222289089015)|**작성자** [큰돌](https://blog.naver.com/jhc9639)

```swift
var adj: [Int: [Int]] = [:]
var visited = [Bool](repeating: false, count: 10)

// 인접리스트로 표현
adj[1] = [2, 3]
adj[2] = [1]
adj[3] = [1, 4]
adj[4] = [3]

for i in 0..<10 {
    guard let _ = adj[i] else {
        continue
    }
    
    if visited[i] { continue }
    visitNode(index: i)
}

// 0번부터 방문 안 한 노드부터 탐색하는 재귀함수, 한 번 방문한 노드는 재방문하지 않음

func visitNode(index: Int) {
    print(index)
    visited[index] = true
    if let data = adj[index] {
        for i in data {
            if visited[i] { continue }
            visitNode(index: i)
        }
    }
}
```

#### 인접행렬과 인접리스트의 차이

1. 공간복잡도
   * 인접행렬 : O(V^2)
   * 인접리스트 : O(V + E)
     * 연결된 정점만 데이터가 들어간다.
     * 정점이 여러개가 있더라도 연결된 정점만 들어가므로, 간선의 개수만 들어가게 되는것이다
2. (최악) 시간복잡도
   1. 간선 한 개 찾기
      *   어떤 i에서 j까지 가는 간선이 있는가?

          * 인접행렬 = O(1)
          * 인접리스트 = O(V)

          ```swift
          // 인접행렬
          if a[i][j]

          // 인접리스트
          if adj.values.contains(j)
          ```
   2. 모든 간선 찾기
      * 인접행렬 : O(V^2)
      * 인접리스트 : O(V + E)
3. 그래서 언제 인접 리스트, 인접 행렬을 써야할까?

* 그래프가 희소한 경우(sparse) 인접리스트, 조밀할 때(dense)에는 인접 행렬을 쓴다.
  * 연결된 정점이 많이 없을 때에는 인접 리스트를 쓰는 것이 더 빠르고 좋다.
    * 인접행렬의 경우 무식하게 이차원 배열을 만들게 된다.
    * 연결되지 않은 노드들을 위해서도 이차원 배열을 만들게 된다.
  * 그래프가 조밀할 때 (간선이 너무 많을 때) 인접 행렬이 더 좋다.
    * 어차피 노드들이 모두 연결되어있다면 메모리적 효율성이 동일하므로, 하나의 간선을 찾는데에 속도가 더 빠른 인접 행렬이 더 빠르다.
* 보통 sparse한 그래프가 나오기 때문에, 인접 리스트로 사용하되, 문제에서 인접 행렬로 주어진다면 인접행렬 그대로 풀자

#### 맵과 방향벡터

💡 문제가 \*\*지도\*\*로 주어지는 경우에 푸는 방법

\[ 1, -1] \[ 1, 0 ] \[ 1, 1 ]

\[ 0, -1] \[ 0, 0 ] \[0, 1]

\[-1, -1] \[-1, 0] \[-1, 1]

1.  현 좌표를 기준으로 **상, 하, 좌, 우**로 탐색할 수 있는 경우!

    ```swift
    // dy: direction Y, dx: direction X -> 보통 방향 벡터는 dy, dx로 정의한다.

    let dy = [-1, 0, 1, 0]
    let dx = [0, 1, 0, -1]

    for i in 0..<4 {
        let ny = dy[i]
        let nx = dx[i]
        print(ny, nx)
    }
    ```
2.  대각선 방향으로도 이동할 수 있는 경우 (8방위)

    ```swift
    let dy = [0, 1, 1, 1, 0, -1 ,-1, -1]
    let dx = [1, 1, 0, -1, -1, -1, 0, 1]
    let (y, x) = (0, 0)

    for i in 0..<8 {
        let ny = y + dy[i]
        let nx = x + dx[i]
        print("\(ny), \(nx)")
    }
    ```

💡 \*\*Q. 3 \* 3 맵을 입력받아야 함. 이 맵은 1과 0으로 이루어져있고 {0, 0}은 무조건 1임을 보장한다. {0, 0}부터 4방향을 기준으로 한칸씩 탐색해나가며 방문한 정점은 다시 방문하지 않으며 방문하는 좌표를 출력하는 코드. 0은 갈 수 없는 지역. 1은 갈 수 있는 지역을 구현하시오.\*\*

**\[출처]** [**\[알고리즘 강의\] 2주차. 그래프이론, 인접행렬, 인접리스트, DFS, BFS, 트리순회**](https://blog.naver.com/jhc9639/222289089015)**|작성자** [**큰돌**](https://blog.naver.com/jhc9639)

```swift
let dy = [-1, 0, 1, 0]
let dx = [0, 1, 0, -1]
let map = [[1, 0, 1], [1, 0, 1], [0, 1, 1]]
var visited = [[Bool]](repeating: [Bool](repeating: false, count: 3), count: 3)

for y in 0..<map.count {
    for x in 0..<map[y].count {
        if !visited[y][x] && map[y][x] == 1 {
            visitNode(y: y, x: x)
        }
    }
}

func visitNode(y: Int, x: Int) {
    print("\(y), \(x)")
    **visited[y][x] = true // 방문처리**
    
    for i in 0..<4 {
        let ny = y + dy[i]
        let nx = x + dx[i]
        
        **if ny < 0 || nx < 0 || nx >= 3 || ny >= 3 { continue } // underflow, overflow**
        if map[ny][nx] == 0 || visited[ny][nx] { continue }
        visitNode(y: ny, x: nx)
    }
}
```

#### 연결된 컴포넌트(Connected component)

* 그래프 안에 하위그래프를 말하며, 하나의 덩어리라고 볼 수 있다.
*   연결된 컴포넌트에 속한 **모든 정점을 연결하는 경로**가 있다는 특징이 있다.

    ![connected component = 3](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/68aaca3a-4f0b-4724-871d-13af3e76d128/Untitled.png)

    connected component = 3
* 컴포넌트들에 번호를 붙여가며 색칠하는 알고리즘을 \*\*풀르드필(floodfill)\*\*이라고 한다.
  * 첫번재 연결된 컴포넌트에서 연결된 모든 노드는 1번
  * 두 번째 연결된 컴포넌트에서 연결된 모든 노드는 2번 이런 식으로
  * 해당 번호는 연결된 덩어리의 개수만큼만 나온다.
* 만약 맵으로 주어지는 상황에서도 갈 수 있는 지점을 통해 정점의 집합을 만들 수 있다. 이를 기반으로 connected component로 만들어보자.

#### 깊이 우선 탐색 (DFS, Depth First Search)

* 그래프를 탐색할 때 쓰는 알고리즘이다.
* 인접한 노드들을 재귀적으로 방문하며, 방문한 정점은 다시 방문하지 않는다.
  * 각 분기마다 가장 멀리 있는 노드까지 탐색하는 알고리즘이다.
*   수도코드

    ```swift
    DFS(u, adj)
        u.visited = true
        for each v ∈ adj[u] // 인접 리스트를 조회. ∈: 포함
            if v.visited == false // 방문하지 않았다면
                DFS(v, adj) // 재귀함수
    ```
*   DFS 코드

    ```swift
    var graph: [Int: [Int]] = [:]
    var visited = [Bool](repeating: false, count: 10)

    graph[1] = [2, 3]
    graph[2] = [4, 5]
    graph[3] = [1]
    graph[4] = [2]
    graph[5] = [2]
    dfs(u: 1) // 1 2 4 5 3

    func dfs(u: Int) {
        print(u)
        visited[u] = true
        for v in graph[u]! {
            if !visited[v] {
                dfs(u: v)
            }
        }
    }
    ```
* DFS 구현 방법 (자유롭게 구현할 줄 알아야한다.)
  1.  방문되어있는지 확인 후 호출하는 방법

      ```swift
      func dfs(u: Int) {
          print(u)
          visited[u] = true
          for v in graph[u]! {
              **if !visited[v] {**
                  **dfs(u: v)
              }**
          }
      }
      ```
  2.  무조건 방문하는 방법

      ```swift
      func dfs(u: Int) {
              **if visited[u] { return }**
          print(u)
          visited[u] = true
          for v in graph[u]! {
              dfs(u: v)
          }
      }
      ```

💡 종화는 21세기 유명한 방구쟁이다. 종화는 방구를 낄 때 "이러다... 다 죽어!!" 를 외치며 방구를 뀌는데 이렇게 방귀를 뀌었을 때 방귀는 상하좌우 네방향으로 뻗어나가며 종화와 연결된 "육지"는 모두 다 오염된다. "바다"로는 방구가 갈 수 없다. 맵이 주어졌을 때 종화가 "이러다... 다 죽어!!"를 "최소한" 몇 번외쳐야 모든 육지를 오염시킬 수 있는지 말해보자. 1은 육지며 0은 바다를 가리킨다

**\[출처]** [\[알고리즘 강의\] 2주차. 그래프이론, 인접행렬, 인접리스트, DFS, BFS, 트리순회](https://blog.naver.com/jhc9639/222289089015)|**작성자** [큰돌](https://blog.naver.com/jhc9639)

→ 그래프가 퍼져나간다 → 그래프 탐색 DFS 또는 BFS

```swift
let input = readLine()!.split(separator: " ").map { Int($0)! }
let (n, m) = (input[0], input[1])
let dy = [-1, 0, 1, 0]
let dx = [0, 1, 0, -1]
var map: [[Int]] = []
var visited = [[Bool]](repeating: [Bool](repeating: false, count: m), count: n)
var result = 0

for _ in 0..<n {
    map.append(readLine()!.split(separator: " ").map { Int($0)! })
}

for y in 0..<n {
    for x in 0..<m {
        if map[y][x] == 0 || visited[y][x] { continue }
        dfs(y: y, x: x)
        result += 1
    }
}

print(result)

func dfs(y: Int, x: Int) {
    visited[y][x] = true
    
    for i in 0..<4 {
        let ny = dy[i] + y
        let nx = dx[i] + x
        
        if ny < 0 || nx < 0 || ny >= n || nx >= m { continue }
        if visited[ny][nx] || map[ny][nx] == 0 { continue }
        dfs(y: ny, x: nx)
    }
}
```

#### 너비 우선 탐색 (BFS, Breadth First Search)

* 그래프 탐색 알고리즘
* 다음 깊이의 정점으로 이동하기 전, **현재 깊이의 모든 정점을 탐색**하며, **방문한 정점은 다시 방문하지 않는** 알고리즘이다.
  * **같은 가중치**를 가진 그래프에서 **최단 거리 알고리즘**으로 쓰인다.
  * Layer별, **Level 별로 탐색**한다는 의미이다.
* BFS 구현 방법
  *   자료구조 큐(Queue)를 사용하면 레벨 별로 탐색이 가능하다.

      ```swift
      // Queue 구현

      struct Queue<T> {
          private var queue: [T] = []
          
          var count: Int {
              queue.count
          }
          
          var isEmpty: Bool {
              queue.isEmpty
          }
          
          var peek: T? {
              queue.first
          }
          
          mutating func enqueue(_ element: T) {
              queue.append(element)
          }
          
          mutating func dequeue() -> T? {
              queue.isEmpty ? nil : queue.removeFirst()
          }
      }
      ```
  *   수도코드 1

      ```swift
      BFS(G, u)
          u.visited = true
          q.push(u);
          while(q.size()) 
              u = q.front() 
              q.pop()
              for each v ∈ G.Adj[u]
                  if v.visited == false
                      v.visited = true
                      q.push(v) 
      ```
  *   수도코드 2 - 최단거리 배열로 쓸 수 있다.

      ```swift
      BFS(G, u)
          u.visited = 1
          q.push(u);
          while(q.size()) 
              u = q.front() 
              q.pop()
              for each v ∈ G.Adj[u]
                  if v.visited == false
                      **v.visited = u.visited + 1**
                      q.push(v) 
      ```
*   Swift 구현

    ```swift
    func bfs(v: Int) {
        var queue: [Int] = [v]
        **visited[v] = 1**
        
        while !queue.isEmpty {
            let u = queue.removeFirst()
            
            for node in graph[u]! { // 인접리스트니까 리스트가 항상 존재
                if visited[node] > 0 { continue }
                queue.append(node)
                visited[node] = visited[u] + 1 // 가중치가 같을 때, **최단거리 계산**
            }
        }
    }
    ```
* 시작지점이 다수인 경우
  * 큐에 푸시하는 지점도 다수가 되어야한다
  * 해당 지점들의 visited를 모두 1로 만들면서 시작해야한다.
* BFS가 가중치가 같은 그래프 내에서만 최단거리로 쓰이는 이유
  * Level로 최단거리를 측정하기 때문이다.
  * 현재 방법으로는 각 간선마다 다른 가중치를 줄 수 없다.
  * 가중치가 다른 경우의 최단거리를 찾기 위해서는 다익스트라, 벨만포드 등을 써야한다.

💡 \*\*문제\*\* 승원이는 당근을 좋아해서 당근마킷에 엔지니어로 취업했다. 당근을 매우좋아하기 때문에 차도 당근차를 샀다. 이 당근차는 한칸 움직일 때마다 당근을 내뿜으면서 간다. 즉, "한칸" 움직일 때 "당근한개"가 소모된다는 것이다. 승원이는 오늘도 아침 9시에 일어나 당근마킷으로 출근하고자 한다. 승원이는 최단거리로 당근마킷으로 향한다고 할 때 당근몇개를 소모해야 당근마킷에 갈 수 있는지 알아보자. 이 때 승원이는 육지로만 갈 수 있으며 바다로는 못간다. 맵의 1은 육지며 0은 바다를 가리킨다. 승원이는 상하좌우로만 갈 수 있다.

**입력**

맵의 세로길이 N과 가로길이 M 이 주어지고 이어서 N \* M의 맵이 주어진다. 그 다음 줄에 승원이의 위치(y, x)와 당근마킷의 위치(y, x)가 주어진다.

**출력**

당근을 몇개 소모해야 하는지 출력하라.

**범위**

1 <= N <= 100

1 <= M <= 100

**예제**

5 5 0 0 4 4 1 0 1 0 1 1 1 1 0 1 0 0 1 1 1 0 0 1 1 1 0 0 1 1 1

**정답** 9

```swift
var input = readLine()!.split(separator: " ").map { Int($0)! }
let (n, m) = (input[0], input[1])
input = readLine()!.split(separator: " ").map { Int($0)! }
let (personX, personY) = (input[0], input[1])
input = readLine()!.split(separator: " ").map { Int($0)! }
let (marketX, marketY) = (input[0], input[1])
var graph: [[Int]] = []

var visited = [[Int]](repeating: [Int](repeating: 0, count: m), count: n)
let dy = [-1, 0 , 1, 0]
let dx = [0, 1, 0, -1]

for _ in 0..<n {
    graph.append(readLine()!.split(separator: " ").map { Int($0)! })
}

bfs(personX, personY)
print(visited[marketY][marketX])

func bfs(_ y: Int, _ x: Int) {
    var queue = [(y, x)]
    visited[y][x] = 1
    
    while !queue.isEmpty {
        let (y, x) = queue.removeFirst()
        
        for i in 0..<4 {
            let ny = y + dy[i]
            let nx = x + dx[i]
            
            if ny < 0 || nx < 0 || ny >= n || nx >= m { continue }
            if visited[ny][nx] > 0 { continue }
            queue.append((ny, nx))
            visited[ny][nx] = visited[y][x] + 1
        }
    }
}
```

#### DFS와 BFS 비교

* 시간복잡도 (동일하다)
  * 인접리스트: O(V + E)
  * 인접행렬 : O(V^2)
* 차이점
  * DFS
    * 메모리를 덜 사용한다.
    * 절단점 등을 구할 수 있다. (고급 알고리즘)
    * 코드가 간단하며, 완전탐색의 경우 많이 사용한다.
  * BFS
    * 큐를 구현해야하므로 메모리를 더 쓴다.
    * 가중치가 같은 그래프 내에서 최단거리를 구할 수 있다.

💡 문제에서 “퍼져나간다”, “탐색한다”가 있다면, DFS와 BFS를 떠올리자! 최단거리를 구해야할 때 BFS를 써보자.

#### 트리순회

💡 트리 구조에서 각각의 노드를 정확히 한 번만 체계적으로 방문하는 과정이다. 방문 순서에 따라 순회 방법이 결정된다.

1. 후위순회(postorder traversal)
   *   자식들 노드 방문 후 자신을 방문한다. (왼쪽부터)

       ```swift
       postorder( node )
           if (node.visited == false) 
               postorder( node->left ) 
               postorder( node->right )
               node.visited = true
       ```
2. 전위순회 (preorder traversal)
   * 자신 노드 방문 후 자식 노드를 방문한다.
   *   DFS와 동일하다.

       ```swift
       preorder( node )
           if (node.visited == false)
               node.visited = true
               preorder( node->left )
               preorder( node->right )
       ```
3. 중위순회 (inorder traversal)
   *   왼쪽 자식 노드 → 자신 노드 → 오른쪽 자식 노드

       ```swift
       inorder( node )
           if (node.visited == false) 
               inorder( node->left )
               node.visited = true
               inorder( node->right )
       ```
4. 레벨 순회
   * 같은 레벨을 탐색한다
   * BFS와 동일하다.

```swift
var graph: [Int: [Int]] = [:]

graph[1] = [2, 3]
graph[2] = [4, 5]

print("\n후위 순회:", terminator: " ")
postOrder(v: 1)

print("\n전위 순회:", terminator: " ")
preOrder(v: 1)

print("\n중위 순회:", terminator: " ")
inOrder(v: 1)

func postOrder(v: Int?) { // 후위
    guard let v else { return }
    postOrder(v: graph[v]?[0])
    postOrder(v: graph[v]?[1])
    print(v, terminator: " ")
}

func preOrder(v: Int?) { // 전위
    guard let v else { return }
    print(v, terminator: " ")
    preOrder(v: graph[v]?[0])
    preOrder(v: graph[v]?[1])
}

func inOrder(v: Int?) { // 중위
    guard let v else { return }
    inOrder(v: graph[v]?[0])
    print(v, terminator: " ")
    inOrder(v: graph[v]?[1])
}
```
