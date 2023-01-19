# 메모리 안전

### 메모리 안전의 개념

- 멀티 쓰레드 환경에서만 메모리 충돌이 발생하는 것은 아니다.
    - 싱글 쓰레드 환경에서도 하나의 메모리에 동시적 접근이 발생할 수 있다.
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f479dec-6e0d-462a-94b7-4aef1728dad9/Untitled.png)
        
    - 초록색 : 함수가 실행되는 약간의 시간 (조금 오랫동안 접근하고 있다고 가정)
    - 함수를 실행하는동안, 특정 메모리 하나를 사용하는데, 함수 내부에서 다시 그 메모리에 접근할 수 있다.
    
    ```swift
    var stepConflict = 1
    
    func increment(_ number: inout Int) {
        number += stepConflict
    }
    
    increment(&stepConflict) // 동시 접근으로 인한 문제
    ```


- 동일한 함수 내에서 다른 종류의 매개변수를 단일 변수로 전달하는 경우
    
    ```swift
    func balance(_ x: inout Int, _ y: inout Int) {  // 평균값 설정하는 함수
        let sum = x + y
        x = sum / 2
        y = sum - x
    }
    
    var playerOneScore = 42
    var playerTwoScore = 30
    
    // 입출력 파라미터로 동일한 변수를 전달하고 있음
    //balance(&playerOneScore, &playerOneScore)   // 에러 발생 ⭐️, 하나의 메모리에 동시에 접근할 수도 있음.
    
    balance(&playerOneScore, &playerTwoScore)   // 에러 발생하지 않음
    ```
    
    
### 값타입 인스턴스
- 값 타입 인스턴스는 속성 하나가 아니라 인스턴스 전체에 대해 읽기와 쓰기를 수행한다.
- 동일한 인스턴스에 접근해서 저장 속성의 값을 나누는 것은 메모리 안전하지 않다.
