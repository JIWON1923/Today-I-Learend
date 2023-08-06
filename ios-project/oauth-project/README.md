# OAuth Project

#### OAuth?

💡 OAuth는 Third party app에게 유저의 비밀번호를 제공하지 않고, 인증하기 위해 나타난 개념이다. 즉, Third party app는 유저의 비밀번호를 알지 못해도, 사용자가 허가한 자원에 대해 접근할 수 있도록 도와준다.

OAuth에는 4개의 참여자가 존재한다. Third party App에 해당하는 **Client**, 실제 앱 사용자에 해당하는 **Resource Owner**, 요청 데이터에 대해 응답을 주는 **Resource server**, 인증을 수행하는 **Authorization Server**가 존재한다.

#### 참여자

1.  Resource Owner

    보호된 리소스에 대해서 액세스를 부여할 수 있는 주체이다. (end user라고 부른다)

    앱으로 설명하자면, 작고 소중한 내 앱의 사용자이다. 이들이 자원에 대한 접근 권한을 부여하는 역할을 한다. ShortcutsZip에서 애플로 로그인을 기능을 제공하고 있는데, 처음 회원가입할 때 이름, 이메일, 기타 정보를 제공할지 말지를 결정하는 주체이다.
2.  Resource Server

    보호된 리소스를 호스팅하는 서버로, access token을 통해 요청을 받고, 응답하는 역할을 수행한다.

    ShortcutsZip에서는 따로 유저의 정보를 요구하지 않지만, 예를 들어 사용자의 프로필 정보, 사진, 이메일 주소 등과 같은 리소스를 요청할 때, 그 정보를 호스팅해주는 서버라고 이해하면 된다.
3.  Client

    내가 개발한 앱이다. Resource server에서 제공하는 정보를 사용하고자하는 계층이다.

    예를 들어 사용자 A의 깃허브 잔디 정보를 가지고 오고 싶다면, Github Authorization Server에서 인증을 받은 정보를 기반으로 Resorce server에게 잔디 정보를 요청할 수 있는 것이다.
4.  Authorization Server

    Resource Owner를 인증하고, 권한을 얻은 후에, Client에게 Access Token을 발급한다.

    즉, 사용자가 Apple 아이디로 로그인을 하면, 사용자가 제공한다고 체크한 정보를 자신의 서버에 저장한다. 이후에 이를 기반으로 Access Token을 발급하여 Resource Server에게 제공하게 된다.

정리하자면, Resource Owner가 Client를 사용하고자 한다. 이때 Resource Owner는 Authorization Server를 통해 자신을 인증하고, 어떤 권한을 허용할지 선택한다. 그러면 Authorization Server는 이 데이터를 기반으로 Access Token을 Client에 제공한다. 이후 Client는 Access Token을 통해 Resource Server에게 데이터를 요청할 수 있고, Resource server는 토큰과 요청을 비교하여 데이터를 제공하게 된다.

(아직은 단어가 익숙하지 않아서 헷갈릴 수 있지만, 용어가 익숙해지면 굉장히 쉬울 것이다.)



#### OAuth 과정

**서비스 등록** → **Resource Server의 인증** → **Authorization Server의 승인** → **Access Token 발급**의 단계로 이루어지게 된다. (refresh token은 별개) 그럼 이 단계에 대해 차근차근 알아보자.

1.  서비스 등록 (`**Client Registration**`)

    Authorization Server에서 Client를 식별할 수 있도록 redirection URI와 함께 등록해야한다. 이 과정을 거치면 Client ID, Client Secret을 발급받을 수 있다.

    **client ID**는 클라이언트의 식별자로, Authorization server가 client를 식별할 때 사용하게 된다. **client secret**은 신원을 확인하고, 통신의 신뢰성에 중요한 역할을 하는 값으로 절대 외부로 노출되어서는 안 된다.**redirection URI**는 사용자가 인증 후에 redirection 시킬 주소이다. 즉, Authorization Code를 전달해 줄 주소를 의미한다.
2.  Resource Owner의 인증

    서비스 등록이 완료되면 Resorce Server는 Client의 ID, Secret, Redirect URL을 알고 있다. 이때 Resource owner가 우리 앱에서 Resource server의 정보가 필요한 경우가 있을 것이다.

    그러면 Client에서는 그 기능을 사용하기 위해 로그인 URL의 파라미터로 **Client ID,** **Scope**, **Redirect URI**를 작성한다. 그럼 Authorization Server에서 로그인을 통해 유저는 자신임을 인증한다.

    Authorization Server는 자신의 서버에서 해당 Client ID가 있는지, 요청이 온 URI와, 서버에 저장된 URI 값이 동일한지 확인한다. 이후 Resorce Owner에게 **Scope**에 대한 접근권한을 허용할 것인지를 보여주게 되는 것이다. 이후 사용자가 권한을 허용한 Scope 값을 Resorce Owner의 ID와 함께 저장하게 된다.
3.  Authorization Server의 승인

    Authorization Server는 바로 Access Token을 제공해주지 않는다!! 임시 비밀번호인 **Authorization code**를 Resource Owner에게 전달하게 된다. 이후 이 값을 Client가 받아서 다시 Authorization Server에 **authorization code**, redirect URI, Client ID, **Client Secret**를 제공하여 인증하게 된다.

    내가 Resource Owner라고 가정하고 앱을 생각해보자. 내가 구글로 로그인 버튼을 누르면, WebView를 통해 구글사이트가 나올 것이다. 내 아이디를 입력하고, 어떤 권한을 제공할 것인지 확인한 후에 연동 버튼을 눌렀다. 그러면 Redirect URI를 통해 다시 앱으로 돌아오게 된다. 이때, 우리의 앱인 Client는 WKWebKit의 `decidePolicyFor navigationAction` 를 통해서 Authorization code 값을 캐치할 수 있는 것이다. 이 값과 client secret 등 정보를 다시 Authorization server에게 제공하는 단계인 것이다.
4.  마지막! Acess Token 발급

    Authorization Server는 아까 Client에게 Client ID, Secret, URI, Authorization code까지 모두 받았다. 그러면 이제 Authorization Server가 인증한 유저, 인증한 클라이언트가 된 것이다. 이제 Authorization code는 삭제한 후에, Access Token을 발급해준다.

    이제 Resource Server에게 데이터를 요청할 때, Access Token을 통해 요청을 하게 되면, 해당 Scope에 권한이 있는지 확인하고, 그에 대한 결과를 제공해주게 되는 것이다.

전체적인 흐름은 다음과 같다. (client registration은 해당 명세 과정에서 제외되어있다.)

```
  +--------+                                           +---------------+
  |        |--(A)------- Authorization Grant --------->|               |
  |        |                                           |               |
  |        |<-(B)----------- Access Token -------------|               |
  |        |               & Refresh Token             |               |
  |        |                                           |               |
  |        |                            +----------+   |               |
  |        |--(C)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(D)- Protected Resource --| Resource |   | Authorization |
  | Client |                            |  Server  |   |     Server    |
  |        |--(E)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(F)- Invalid Token Error -|          |   |               |
  |        |                            +----------+   |               |
  |        |                                           |               |
  |        |--(G)----------- Refresh Token ----------->|               |
  |        |                                           |               |
  |        |<-(H)----------- Access Token -------------|               |
  +--------+           & Optional Refresh Token        +---------------+
```



#### Refresh token

Access Token은 수명이 정해져있다. 이 수명이 끝나면, 해당 Access Token으로는 Client는 요청을 보내면, 인증 에러라는 응답만 받게 될 것이다. 그렇다면 이 수명이 끝날 때마다 사용자는 로그인을 해야할 수도 있다. 우리는 이때 Refresh Token을 이용해서 사용자 로그인 없이 Access Token을 새로 발급받을 수 있다.

보통 Access Token을 발급받을 때, Refresh Token을 같이 제공해준다. Resource Server와 AccessToken으로 통신을 하던 중 Invalid token error가 발생하면, Client는 Authorization Server에게 Refresh Token을 보내서 다시 Access Token을 발급받게 된다.

Refresh Token으로 Access Token을 재발급 받을 수 있기 때문에, Refresh Token은 안전하게 저장되어야하며, Refresh Token이 만료되면 사용자는 재로그인해야한다.

```
	+--------+                                           +---------------+
  |        |--(A)------- Authorization Grant --------->|               |
  |        |                                           |               |
  |        |<-(B)----------- Access Token -------------|               |
  |        |               & Refresh Token             |               |
  |        |                                           |               |
  |        |                            +----------+   |               |
  |        |--(C)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(D)- Protected Resource --| Resource |   | Authorization |
  | Client |                            |  Server  |   |     Server    |
  |        |--(E)---- Access Token ---->|          |   |               |
  |        |                            |          |   |               |
  |        |<-(F)- Invalid Token Error -|          |   |               |
  |        |                            +----------+   |               |
  |        |                                           |               |
  |        |--(G)----------- Refresh Token ----------->|               |
  |        |                                           |               |
  |        |<-(H)----------- Access Token -------------|               |
  +--------+           & Optional Refresh Token        +---------------+
```

#### Oauth는 그래서 왜 쓰는건데?

Client 입장에서는 Resource server를 통해 owner의 신원은 인증할 수 있다. 또한 resource owner는 client에게 구글의 ID나 PW를 제공하지 않고도, 구글의 서비스를 client에서 사용할 수 있게 되는 것이다.

즉, 훨씬 더 안전하고 간편한 방법으로 서로를 인증할 수 있기 때문이다 !

#### 출처

[WEB2 - OAuth 2.0 : 1.수업소개](https://www.youtube.com/watch?v=hm2r6LtUbk8)

[RFC 6749: The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749#section-1.1)
