---
description: UICollectionView의 레이아웃 클래스
---

# Compositional Layout

## 내 마음대로 요약해보기

### 짧은 요약

**UICollectionViewCompositionalLayout**는 복잡한 UI를 구현할 때, 각 UI 형태를 하나의 Section으로 설정해서 뷰를 구성할 수 있게 도와주는 Layout이다. 즉, 복잡한 UI를 더 직관적이고 유연하게 작성할 수 있는 Layout class이다. 또한 **UICollectionViewDiffableDataSource**를 사용하면, 데이터 관리와 업데이트 작업을 더 편하게 관리할 수 있다.  (단, 둘 다 iOS 13부터 가능하다)

&#x20;UICollection View Compositional Layout의 구성요소를 조합해서 레이아웃을 구성할 수 있다. Layout은 Item -> Group -> Section으로 최종적으로 Layout을 구성한다.

<figure><img src="../.gitbook/assets/image (6).png" alt="" width="338"><figcaption><p>apple 공식 문서 참조</p></figcaption></figure>

### Group이랑 Section이 뭐가 다른데?

나는 Compositional layout을 설명하는 이미지를 보고, Section과 Group에 대한 차이점을 이해하기 어려웠다. 왜 나눠져야하는지 이해가 잘 가지 않아서 조금 더 알아보기로 했다

<details>

<summary>숏컷집의 메인 화면</summary>

![](<../.gitbook/assets/image (9).png>)

</details>

1.  Section

    섹션은 컬렉션 뷰에서 데이터를 구분하는 논리적인 단위이다. "논리적"이라는 것이 개념적으로 다가오지 않을 수 있다. SwiftUI의 Navigation Stack을 기준으로 생각해보자. 우리는 View를 보여줄 때 data의 Type으로 보여줄 뷰를 결정한다. 이처럼 하나의 섹션이 보여주고자 하는 Type이 무엇인가를 결정하는 것이 Section이다.\
    \
    숏컷집의 메인 화면으로 설명하자면 "**다운로드 순위**"라는 타입, "**라이프 스타일**"이라는 타입, "**카테고리**"라는 타입로 나눌 수 있다. 이렇게 개념적으로 보여주고자 하는 데이터를 나누는 단위를 section이라고 한다.\
    \
    이 섹션은 UICollectionViewCompositionlayLayoutSection으로 정의되며, Header 및 FooterView를 포함할 수 있다. \

2. Group\
   그룹은 Section 안에서 Item들을 배치하는 단위이다. 숏컷집의 메인 화면을 기준으로, 셀 간 간격이나 셀의 정렬 방향들을 결정할 때 사용함을 의미한다.

결론적으로 아이템을 논리적으로 분리하기 위해 Section을 사용하는 것이고, 실제로 배치하는 역할을 Group이 담당한다고 이해할 수 있을 것 같다.&#x20;



## 공식문서 개념 기반 이해&#x20;

### Layout 설정 기본 코드

```swift
func createBasicListLayout() -> UICollectionViewLayout { 
    let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0),                                  
                                         heightDimension: .fractionalHeight(1.0))    
    let item = NSCollectionLayoutItem(layoutSize: itemSize)  
  
    let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0),                                          
                                          heightDimension: .absolute(44))    
    let group = NSCollectionLayoutGroup.horizontal(layoutSize: groupSize,                                                   
                                                     subitems: [item])  
  
    let section = NSCollectionLayoutSection(group: group)    


    let layout = UICollectionViewCompositionalLayout(section: section)    
    return layout
}
// 출처: 애플 공식 문서
```

이 코드는 애플에서 제공하는 compositional layout을 설정하는 코드이다. 여기서 눈에 띄는 부분은 당연 size를 정하는 부분이 아닐까 싶다. 각 치수를 정할 때 [fractional](https://developer.apple.com/documentation/uikit/nscollectionlayoutdimension/3199059-fractionalwidth/) 또는 absolute를 통해서 결정하고 있다. \
fractional은 상대적인 치수, absolute는 절대적인 치수이다. fractional 1.0인 경우, 자신이 차지할 수 있는 부분만큼 차지하겠다는 의미가 된다. 즉, itemSize를 정할 때, group Size를 기반으로 자신이 차지할 수 있는 부분만큼 꽉 채워서 설정하고 있다는 의미이다.



























#### 출처

{% embed url="https://developer.apple.com/documentation/uikit/uicollectionviewcompositionallayout/#overview" %}

{% embed url="https://developer.apple.com/videos/play/wwdc2020/10097" %}
WWDC 20 - Advances in UICollectionView
{% endembed %}
