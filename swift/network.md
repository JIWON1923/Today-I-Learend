# Network

### ARC 목차
- [네트워크 통신의 간단한 이해](#네트워크-통신의-간단한-이해)
    - www를 구성하는 기술
    - HTTP란 무엇인가?
    - HTTP 요청 메서드 종류
    - HTTP 응답 상태 코드
    
- [iOS의 네트워킹](#iOS의-네트워킹)
    - iOS에서의 데이터 요청 단계
    - URL Session


## 네트워크 통신의 간단한 이해

### www (world wide web)을 구성하는 기술

1. HTML
2. **HPPT**
3. URL

### HTTP (HyperText Transfer Protocol)

- 하이퍼 문서를 전송하는 것에서 시작되었다.
    - 현재는 이미지, 영상, 음성, json 등 모든 형태의 데이터를 전송할 수 있다.
- 인터넷의 모든 것은 HTTP로 구성 돼 있다.
- RFC 문서 : HTTP에 관련된 내용이 기술 돼 있다.
- HTTP : Request
    - 형식이 있는 편지지
    - 앱단에서 이루어진다.
        - 각 어플에서 HTTP 룰에 맞춰 편지 작성 → 앱에서 편지지를 던지고, iOS에 전달한다.
        - iOS는 편지를 받아 각각 다른 Port 번호를 붙인다.
        - 편지를 보낼 곳의 주소를 붙인다.(IP) → 전달한다.
        - LAN : MAC 주소를 추가한다 → 서버로 전달한다.
- HTTP: 데이터를 어떻게 주고 받을지에 대한 약속(요청: request, 응답: response)
- TCP : 주고받을 때 상태 확인 및 검증 / PORT : 어떤 앱과 통신하는지

---

**클라이언트가 Request (요청)을 보내면, 서버에서 Response(응답)을 준다.**

1. HTTP 요청 메시지
    
    ---
    
    시작라인 : 메소드, 요청 대상, (경로) , HTTP 버전
    **header** (헤더 필드) : 메타 데이터 (모든 부가 정보)
    공백라인
    **message body** (메세지 본문) : 실제 전송할 데이터

---

1. HTTP 응답 메세지 : HTTP 버전, 상태코드, 문구
    
    ---
    
    시작라인
    header (헤더 필드)
    공백라인
    message body (메세지 본문) : 실제 전송할 데이터

---

### HTTP 요청 메서드 종류 (CRUD)

1. GET (Read)
    - 조회 (게시판 글 읽어오기)
        
2. POST (Create)
    - 등록하기 (게시판 글 쓰기, 댓글 달기, 새로운 주문 생성)
        
3. PUT (Update)
    - 데이터 대체, 없으면 생성 (게시글 수정)
        
4. PATCH
    - PUT에서 필요한 부분 변경 (좋아요 누르기)
        
5. DELETE
    - 삭제 (게시글 삭제하기)
        
---

### HTTP 응답 상태 코드

1. 2xx (success)
    - request 정상 처리 완료
2. 4xx (Client Error)
    - client error
    - 잘못된 요청 (서버가 request를 이해할 수 없음)
3. 5xx (Server Error)
    - server error
    - 서버 내부의 문제 (서버 다운, 일시적으로 사용 불가)

## iOS의 네트워킹

### iOS에서의 데이터 요청 단계

1. 주소를 이용해서 URL 구조체를 만든다.
2. URL Session을 만든다. (네트워킹을 하는 객체)
3. 세션에 작업을 부여한다. (일시정지 상태의 작업)
4. 작업을 시작한다. (Resume) → 비동기적으로 동작한다.

```swift
// 1. URL 구조체를 만든다.
let url = "https://~주소"
let structURL = URL(string: url)

// 2. URL Session을 만든다.
let sesson = URLSession.shared // 싱글톤 객체

// 3. 세션에 작업을 구현한다.
let task = session.dataTask(with structURL) { data, response, error in
    if let error {
        print(error)
        return
    }
    if let data {
        print(String(decoding: data, as UTF8.self) // JSON
    }
}

// 4. 작업을 시작한다.
task.resume()
```

이후 우리가 사용 할 class나 struct로 바꿔서 사용하면 된다.


### URL Session

1. URL 구조체 
2. URL Session
3. dataTask
4. 시작 (resume)

→ 결과를 얻음

→ JSON/DATA 파일을 우리가 쓰려는 Class, Struct로 변형시켜야한다.
