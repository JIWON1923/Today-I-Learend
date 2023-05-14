# 로컬 호스트를 이용한 로그인 페이지 제작

#### Text 편집기를 통해 HTML 작성

* Text 편집기 클릭
* 포멧 -> 일반텍스트 선택
* HTML 작성

```html
<html>
    <head>
        <title>Main page</title>
    </head>
    <meta charset="UTF-8">
    <body>
        <h1>LOGIN</h1>
        ID <input type = "text" name="id" placeholder="아이디를 입력해주세요."><br/>
        PASSWORD <input type = "password" name="password" placeholder="비밀번호를 입력해주세요.">
        <p>
            <input value="LOGIN" type="submit" name="login">
        </p>
    </body>
</html>
```



### 로컬 호스트 설정

* Terminal로 index.HTML 있는 폴더로 이동 이동

```
python -m http.server 8080
```

![](<../../.gitbook/assets/image (7).png>)
