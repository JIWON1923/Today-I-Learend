## RestAPI

### API의 정의

- Application Programming Interface의 약자로, 앱이 서버에게 정보를 요청하는 형식이다.
- 즉 S/W가 다른 S/W에게 데이터를 요청하기위한 수단이다.
- 네트워크 뿐만 아니라 로컬 프로그램에서도 존재한다. (브라우저의 webAPI)

### RestAPI의 정의

- SOAP의 복잡한 형식을 개선하기 위해 rest란 형식의 요청 폼이다.
- 요청의 형태만 보고, 어떤 동작인지 어떤 정보를 위한 것인지 추론이 가능하다.
    - restful한 API는 주소만 보고도, 어떤 요청인지 파악이 가능하므로 개발자간 협업에 좋은 역할을 한다.
- HTTP 규약에 따라 신호를 전송하게 된다.
    - GET, DELETE, POST, PUT, PATCH를 사용하게 된다.
    - POST, PUT, PATCH의 경우 각각 Body가 존재해서 비교적으로 정보를 감춰서 전송할 수 있다.
    - 기능이 특정 용도에 제한되어있지는 않지만 Restful하게 API를 만들기 위해서는 목적에 따라 구분해서 사용해야한다.
- 신호
    - GET : 조회
    - POST : 생성, Body에 데이터를 넣어서 보낸다. (데이터가 저장된 후 인덱스가 생성되므로, 인덱스는 선언하지 않는다.)
    - PUT, PATCH: 업데이트, 업데이트할 인덱스 번호를 삽입한다. BODY에 새로운 정보를 넣는다.
        - PUT : 전체 정보를 업데이트 한다.
        - PATCH : 특정 정보를 업데이트한다.
        - 같은 역할이며, 그룹에서 결정해서 사용한다.
    - DELETE : 삭제, 특정 인덱스 정보를 삭제한다.
- “형식”이기 때문에 특정 기술이나 프레임워크에 구애받지 않는다.
    - HTTP로 정보를 주고받는다면, restful한 서비스를 만들 수 있다. (`restful api design guidelines`를 공부해보자)
- 용도에 따라 GraphicQL을 사용하기도 한다.
- 출처 : [얄팍한 코딩사전 유튜브 영상](https://www.youtube.com/watch?v=iOueE9AXDQQ)
