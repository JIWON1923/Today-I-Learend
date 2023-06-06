---
description: JWCarousel 라이브러리 만들기
---

# 코코아팟 배포하는 방법

### &#x20;Pod Libary를 생성하기

1. 터미널에서 작업할 디렉토리로 이동한다.
2. `pod lib create JWCarousel`을 입력한다.
3.  라이브러리 정보를 입력한다.

    해당 정보를 입력하면, Xcode 프로젝트가 열린다. (안 열린다면 해당 디렉토리 이동해서 직접 열자)

    <div align="left">

    <figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

    </div>



### 코드 작성하기

1.  아까 만든 Pod 프로젝트에서 코드를 작성한다.

    Pod/DevelopmentPods/JWCarousel/ReplaceMe.swift 파일을 수정해주면 된다. **(접근제어 주의!)**
2. 나는 SwiftUI 프로젝트이기 때문에 UIKit으로 되어있는 데모를 수정해줘야했다.
   1. App Delegatedml `UIHostingController`을 이용해서 rootView 수정하기
   2. plist에서 Main Storyboard 정보 삭제하기
   3. Main Storyboard 삭제하기
   4.  target iOS 버전 수정하기. (SwiftUI는 iOS 13부터 사용가능하다)

       Demo와 Pod target에 모두 적용해주자.

### Pod Spec 수정하기

1. 데모 프로젝트 최상단 혹은 Pods/Pod에 JSCarousle.podspec이라는 파일에 들어간다.
2.  이미 엄청난 주석과 함께 적혀져 있겠지만, 문서를 보고 다시 작성하는게 마음 편할 것이다^^;;

    그냥 코드 지워줘도 되고, 터미널에서 새로 만들어도 된다. (나는 그냥 코드 지우고 다시 썼다)

    ```
    pod spec create [NAME|https://github.com/USER/REPO]
    ```
3.  내 pod spec이다.

    <pre><code><strong>Pod::Spec.new do |spec|
    </strong><strong>
    </strong><strong>  # 라이브러리 이름
    </strong><strong>  spec.name          = 'JWCarousel' 
    </strong><strong>  # 라이브러리 버전
    </strong><strong>  spec.version       = '0.1.0'
    </strong><strong>  # 라이센스
    </strong>  spec.license       = { :type => 'MIT', :file => 'LICENSE' }
      # 깃허브 페이지
      spec.homepage      = 'https://github.com/JIWON1923/JWCarousel'
      # 만든 사람 이름 및 메일 주소
      spec.authors       = { 'Jiwon Lee' => 'zest1923@gmail.com' }
      # 라이브러리 요약 설명
      spec.summary       = 'The JWCarousel library for SwiftUI provides a highly customizable carousel view for iOS apps.'
      # 깃 소스
      spec.source        = { :git => 'https://github.com/JIWON1923/JWCarousel.git', :tag => spec.version }
      # Swift 버전 (이거 안 바꾸면 경고 나온다)
      spec.swift_version = '5.0'
      # 최소 버전
      spec.ios.deployment_target  = '15.0'
      # 소스 파일 위치
      spec.source_files = 'JWCarousel/Classes/**/*'
    end
    </code></pre>

    여기서 중요한 부분은 소스파일 위치! (기존 정보는 JWCarousel/이렇게 써져있지 않을거다! 여기 꼭 수정해주자)

    [공식 문서](https://guides.cocoapods.org/syntax/podspec.html)를 참고하여 작성하자

### Pod 검증하기

1. 터미널에서 `pod lib lint`를 입력한다.
2.  나는 오류가 발생했다.



    <figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
3.  오류 내용을 확인하고 하나씩 해결해주면 된다.

    xcodebuild: Returned an unsuccessful exit code. You can use `--verbose` for more information.

    error: extra trailing closure passed in call

    나는 이런 오류가 발생했다. 당황하지 말고 Pod 프로젝트에 들어간다. (코드는 문제가 전혀 없었다)
4.  Pod이 저장된 프로젝트에 들어가보자.



    <figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

    나는 `View+JWcarousel.swift`, `FramePreferenceKey+JWCarousel`이 Class 폴더 내부에 없어서 발생한 문제였다. (코드의 위치를 몰랐던 것임!)



    <figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

    인식 못했던 코드를 여기로 옮겨주고, Xcode에 들어가서 파일 위치를 재설정해준다.
5.  다시 `pod lib lint` 수행한다.



    <figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 깃에 프로젝트 등록하기

1. 프로젝트를 Push 해준다

```
git add .
git commit -m "커밋메시지"
git branch -M main
git remote add origin 주소.git
git push -u origin main
```

2. tag를 push 해준다. (spec에 적은 version과 동일하게 적어준다)

```
git tag 0.1.0
git push origin 0.1.0
```



### pod에 등록하기

1. 명령어 입력

```
pod trunk register 이메일
```

2.  이메일에서 확인하기

    링크 클릭하기!
3. 마지막! spec 푸시하기

```
pod trunk push JWCarousel.podspec
```

### 완성

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/스크린샷 2023-03-19 오전 8.52.25.png" alt=""><figcaption></figcaption></figure>

근데 아직 코코아팟에 검색해도 나오지는 않는다!! 그래도 넘나 뿌\_\_\_듯
