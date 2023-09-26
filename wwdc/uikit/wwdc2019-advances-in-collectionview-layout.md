# \[WWDC2019] Advances in CollectionView Layout

#### CollectionView Layout의 발전 목차

1. **CollectionView의 현재 기술** (current state of the art)
2. (iOS, tvOS, MacOS) **새로운 접근 방식** (a new approach)
3. **Demo**
4. **고급 기능** (advanced layouts)

***

### 1. CollectionView의 현재 기술 (current state of the art)

#### UICollectionViewFlowLayout

1. CollectionViewLayout
   * Layout을 정의하기 위해 두 가지 역할을 가진 클래스로 추상화되어있다.
     * rendering을 하는 class
     * collectionView Layout이 어디로 가는지?에 대해 책임을 지는 class
     *   CollectionView Layout은 추상화되어있기 때문에 사용하려면 항상 Subclassing을 해야한다.

         이것을 Concrete type으로 정의한 것이 iOS 6에서 발표했던 CollectionView Flow Layout이다.
2. CollectionViewFlowLayout
   * line based layout system이었기 때문에, iOS6을 사용했던 환경에서 매우 유용하게 사용됐다.
     * (2018 WWDC 영상 참고) 단순히 Line들을 배치하고, 축을 기반으로 배치해서 Layout을 구성할 수 있다.
     * 매우 간단하고, 추론이 쉬우며, 빠르다는 장점이 있다.
     * 하지만 요즘에 이종화되는 기기와, 다양한 custom layout이 나오면서 UI가 매우 복잡해졌다.

***

*   복잡해진 UI로 인해 발생하는 문제점을 살펴보자.

    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/d74f14bc-8851-4ea5-b86d-88e5c0523949/Untitled.png)
* AppStore의 메인화면을 구성하기 위해 우리는 다양한 컴포넌트를 고민할 것이다.
  *   다양한 모양의 Cell이 존재하기 때문에, CollectionView를 선택했다고 가정하자.

      이때, “Flow”를 표현할 수 있을지 고민하기 시작하자마자 CollectionView 사용을 포기하게 된다. (이유는 몰까)
*   Custom Layout을 설계할 때 발생할 수 있는 문제점

    * Boilerplate code
    * Performance considerations
    * Supplementary and decoration view challenges
      * CollectionView에서 관리할 수 있는 View, DecorationView는 CustomLayout에서 까다롭게 작용한다.
    * Self-sizing challenges

    ⇒ 이 문제들을 해결하기 위해 Flow와 동일한 Concrete class인 새로운 플랫폼을 도입했으며, 이것이 바로 Compositional Layout이다.

***

### 2. **새로운 접근 방식** (a new approach)

#### Compositional Layout의 개념

* Compositional Layout의 중심 개념 3가지
  1.  Composable

      간단한 것을 이용해서 복잡한 것을 만든다.
  2.  Flexible

      유연하게 설계되어, 어떤 레이아웃이든 작성할 수 있다.
  3.  Fast

      자체 최적화를 수행하여 빠르다.
* Compositional Layout은 Layout을 설명하거나, 정의하는 선언형 API이다.

#### Compositional Layout의 활용

*   Composing small layout groups together

    작은 레이아웃을 만들고, 이들을 조합해서 더 큰 레이아웃을 구성한다.
*   Layout groups are line-based

    FlowLayout의 장점을 이어가기 위해 line based에 따라 레이아웃을 그릴 수 있다. (굉장히 많은 UI)

    작은 레이아웃 그룹을 한 줄로 Item을 배치할 수 있다.
*   composition instead of subclassing

    Subclassing을 하지 않아도 된다. (Concrete type)

    추론 가능한 레이아웃으로 구성할 수 있기 때문에 매우 단순하다.

#### Compositional Layout Code 및 개념

*   Core Concepts

    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/dd8f7ba3-c04d-411d-b343-acceff60a6f7/Untitled.png)
* Type(유형)에 대해 자연스러운 Flow가 존재한다.
  *   5가지 유형이 존재한다. (Item, Group, Section, Layout)

      ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/9e8a8131-d82f-4344-8dfd-5c8ec61db257/Untitled.png)

#### Layout Types

1.  Size

    * 모든 것들은 명백한 사이즈를 가지고 있다.
    *   사이즈를 쉽게 추론할 수 있는 방법 (각 Type이 얼마나 큰지 확인)

        Size = Width + Height dimension

    ```swift
    class NSCollectionLayoutSize {
    	init(widthDimension: NSCollectionLayoutDimension,
    			 heightDimension: NSCollectionLayoutDimension)
    }
    ```

    * NSCollectionLayoutDimension
      *   축과 무관한 방법으로, 폭과 높이, 차원에 대한 정보만을 이용해서 축의 크기를 설명한다.

          이때, 폭과 높이의 경우, NSCollectionLayoutDimension이라는 새로운 유형으로 정의한다.
      *   NSCollectionLayoutDimension의 4가지 유형

          ```swift
          // 축의 크기를 나타내는 "축 독립적 방법"
          class NSCollectionLayoutDimension {
          	class func fractionalWidth(_ fractionalWidth: CGFloat) -> Self 
          	class func fractionalHeight(_ fractionalHeight: CGFloat) -> Self 
          	class func absolute(_ absoluteDimension: CGFloat) -> Self
          	class func estimated(_ estimatedDimension: CGFloat) -> Self
          }
          ```
      *   특정 Item의 width = Container 폭의 50%를 차지한다.

          ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/5768595e-34cf-476a-9605-6ed64e5693b8/Untitled.png)
      *   Item의 Height = Container 폭의 30% 차지

          ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/a0b17d76-3643-444c-874e-c1753ca8dec1/Untitled.png)
      *   특정 종횡비를 갖도록 정의 (Container의 25%씩 aspect ratio)

          ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/efc51bb4-0d76-4e8e-a794-39272ed08436/Untitled.png)
      *   정확한 치수를 갖도록 정의 (point based value)

          ![스크린샷 2023-09-26 오후 12.43.37.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/3424070a-e439-42c3-abb2-37673f38e8d0/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2023-09-26\_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE\_12.43.37.png)
2. Item
   *   기존의 Cell의 개념과 동일

       ```swift
       class NSCollectionLayoutItem {
       	convenience init(layoutSize: NSCollectionLayoutSize)
       	var contentInsets: NSDirectionalEdgeInsets
       }
       ```
3.  Group

    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a1e5f0d-7aa9-46a7-9110-f762ee7e5b77/896c488c-129c-475e-b569-abead6d76ebd/Untitled.png)
4. Section (`NScollectionLayoutSection`)
   * CollectionView의 단면을 기준으로 한 Layou의 정의
   * Section에 몇 개의 항목이 있는지에 대해 `DataSource`에 직접 매핑되는 개체
   *   Initializer가 Group을 허용한다.

       ```swift
       class NSCollectionLayoutSection {
       	convenience init(layoutGroup: NSCollectionLayoutGroup)
       	var contentInsets: NSDirectionalEdgeInsets
       }
       ```
5. Layout

* 최상위 Layout class
* iOS, tvOS의 경우, UICollectionViewCompositionalLayout 사용
* MacOS의 경우, NSViewCompositionalLayout 사용
*   플랫폼과 상관 없이, 모든 정의가 동일하다.

    ```swift
    class UICollectionViewCompositionalLayout: UIcollectionViewLayout {
    	init(section: NSCollectionViewSection)
    	init(sectionProvider: @escaping SectionProvider)
    }
    ```
