# \[WWDC 19] Advances in UI Data Sources

#### KEY POINT! (요약)

* Diffabla DataSource의 등장
  * UICollectionView, UITableView에 적용이 가능하다. (iOS13)
  * snapshot이라는 새로운 개념이 등장하면서, performBatchUpdates를 사용할 필요가 없어졌다.
* Diffable DataSource의 사용 방법
  1. NSDiffableDataSource를 생성한다.
  2. Hashable Protocol을 채택한 식별자를 추가한다.
  3. snapshot을 적용한다.
     * 변경된 부분을 Diffable DataSource가 자동으로 처리해서 자연스러운 애니메이션을 적용한다.

***

#### WWDC 영상 목차

* 현재 CollectionView와 TableView에서 데이터를 어떻게 관리하고 있는가?
* 새로운 Data 관리 방법
* 앱을 통한 데모
* Diffable DataSource를 활용하는 방법

***

#### 현재 CollectionView와 TableView에서 데이터를 어떻게 관리하고 있는가?

개발할 때 TableView, CollectionView는 정말 빼놓을 수 없다. 어떤 앱이든, 기본적으로 개발해야하는 친구들 같다. (애플에서는 Collection View를 기반으로 설명하지만, 나는 Table View 기반으로 설명하고 이해할 예정이다)

**UITableView DataSource**

TableView를 구현할 때, 우리는 `UITableViewDataSource`라는 프로토콜을 채택하게 된다. 그리고 그 프로토콜은 항상 얘네들을 구현해줘야된다.

```swift
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	// 몇 개의 Cell을 보여줄거야?
}
   
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
	// 어떤 Cell을 보여줄거야?
}
```

굉장히 직관적이다. **몇 개의 Cell을, 어떤 모양으로 보여줄 지** 알려주기만 하면, TableView를 만들 수 있다.

***

**실제 앱에서 어떻게 사용하고 있는가?**

다음은 내가 진행하고 있는 프로젝트 ShortcutsZip이다.(~~얘는 SwiftUI임~~)

| ShortcutsZip 화면1                                                                                   | ShortcutsZip 화면2                                                                                   |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| ![](https://velog.velcdn.com/images/jwlee0610/post/0e364e09-8a77-4abb-860e-d3f7c05b668a/image.png) | ![](https://velog.velcdn.com/images/jwlee0610/post/40485651-b30f-4008-9d7f-88c4dcbe7cc4/image.png) |
| 단축어(Shortcut이라는 구조체)들을 다양한 형태로 분류해서, 사용자들이 더 쉽게 단축어를 찾을 수 있도록 구성 돼 있다.                             |                                                                                                    |

이렇게 다양한 정보를, 또는 같은 데이터 타입이지만 특정 카테고리로 나눠서 정보를 제공하고 싶을 때 우리는 **`Section`** 이라는 개념을 DataSource에게 제공해줘야한다.

```swift
func numberOfSections(in tableView: UITableView) -> Int {
    // 몇 개의 Section을 가지고 있어?
}

func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    // 섹션마다 데이터가 몇 개 들어있어?
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    // Cell의 모양은 어떻게 생김?
}
```

내가 진행했던 ShortcutsZip의 경우, 이런식으로 표현을 할 수 있다.

```swift
var shortcutDataList = [[Shortcut]]()

/* 생략 */

func numberOfSections(in tableView: UITableView) -> Int {
	shortcutDataList.count // 2차원 배열의 count 값이 Section의 개수
}

func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  shortcutDataList[section].count // 배열의 section 인덱스에 들어있는 데이터의 개수가 Cell의 개수
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
		// Section마다 Cell 모양을 지정해서 리턴
    let section = indexPath.section 
		switch section {
			let cell = UITableViewCell() 
			return cell
		}
}
```

그런데, 우리가 실제로 개발할 땐 1차원 배열로 표현하기 어려운 경우가 굉장히 많다. 무신사 앱을 봐 보자. (테이블 뷰는 아님)

| 무신사 메인화면1                                                                                                                     | 무신사 메인화면2                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| ![](https://velog.velcdn.com/images/jwlee0610/post/c6c9b988-12ac-4574-9e61-86a52627d292/image.png)                            | ![](https://velog.velcdn.com/images/jwlee0610/post/10003ab1-1565-4a15-bd35-6494c4b8c297/image.png) |
| ShortcutsZip과 다르게, 메인화면에서 다양한 정보를 보여주고 있다. 각 섹션마다 Clothes라는 Struct가 아니라, 광고, 카테로기, 라이브 편성표, 옷 등 다양한 Struct로 구성된 정보들을 보여주고 있다. |                                                                                                    |

그런데, 무신사와 같이 여러 개의 데이터를 DataSource에 어떻게 표현하게 될까를 생각해보자. (일차원적으로만) 대충 이렇게 생겼겠지??

```swift

var advertiseList: [AdverTise] = [] // 광고 탭
var categories: [Category] = [] // 카테고리 리스트
var liveContents: [Contents] = [] // 라이브 방송
/* 기타 정보들 .. */

/* (생략)서버 데이터 호출 -> 각 정보들 업데이트하는 로직들 */

func numberOfSections(in tableView: UITableView) -> Int {
	// section의 개수 return
    // 섹션 개수를 직접 리턴하거나 UI를 서버로 결정하면 그 데이터의 count를 return)
}

func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  switch section {
		case 0: 
			return advertiseList.count
		case 1:
			return categories.count
		case 2:
			return liveContents.count
		/* 생략 */
	}	
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let section = indexPath.section
		switch section {
			let cell = UITableViewCell() 
			return cell
		}

}
```

아직까지 dataSource는 굉장히 직관적이다. 내가 가진 Section 개수, Section 내부 데이터 개수, Cell의 모양만 알려준다면, 알아서 TableView를 그릴 수 있기 때문이다.

**그럼 어떤 문제가 있는데?**

우선 실제 앱은 단순히 1\~2차원의 배열로 표현할 수 없는 경우가 많다.

> 여기서 가장 큰 문제는 사용자의 입력으로 데이터가 변화했을 때, 개발자가 직접 UI에도 업데이트를 시켜줘야한다는 것이다. (데이터가 변화한다고, 알아서 TableView가 변하지 않는다. 우리는 `reloadData` 등을 사용해야한다는 것을 의미한다.)

조금 더 자세한 예로 무신사 앱에서 어떤 옷에 좋아요를 누른 상황을 생각해보자. 그럼 대충 이런 로직으로 동작 할 것이다.ㅋ

1. 사용자가 좋아요 버튼을 누른다.
2. 서버에 Post를 보냄 (ㅇㅇ가 좋아요 눌렀어)
3. 서버가 응답을 보내줌
4. 응답을 기반으로 데이터와 UI 업데이트한다.

우리는 이때, 자연스러운 애니메이션을 보여주기 위해서 reloadData()를 수행한 후에 scroll의 위치를 바꿔주기도 하고, insertRow(at:)을 통해서 데이터의 변화와 UI의 변화를 연결시켜주기도 한다. 즉, 이제까지 했던 작업들이 개발자들이 직접 변경된 데이터의 위치를 찾아서! 애니메이션을 보여줬다는 것이다. (맞겠지?)

그리고 가장 크리티컬한 문제! 개발하는 중, **synchronization**에러를 많이 경험했을 것이다. 간단한 예시로, Section이 3개인 TableView가 있다고 가정하자. 이때, 3번째 Section에 데이터가 1개 있었는데 마지막 남은 데이터 하나를 지운 것이다. 그럼 Section에는 아무것도 없게 된다. 이때, 너가 dataSource에서 알려준 값이랑, 내가 보여줘야되는 값이 다른데? 이러면서 앱이 죽게 되는것이다 ^^^^

```
Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: Invalid update: invalid number of sections. The number of sections contained in the collection view after the update (10) must be equal to the number of sections contained in the collection view before the update (10), plus or minus the number of sections inserted or
deleted (0 inserted, 1 deleted).'
```

애플은 이렇게 자연스러운 애니메이션을 보여주는게 어려운 상황, 그리고 synchronization 에러 등이 dataSource에 있다고 말했다. 즉, UI를 업데이트하는 주체 = Data! 그런데, Data가 변화해도 개발자가 알려주지 않으면, UI가 업데이트 되지 않는 이것들이 모여 저런 문제를 발생시킨다는 것이다.

그래서 데이터가 업데이트 되면, UI를 업데이트 시키면 되지 않을까? 에서 탄생한 것이 바로 **Diffable DataSource**인 것이다\~!~~!~~!\~!

***

#### 새로운 Data 관리 방법 - Diffable Data Source

> Diffable Data Source의 기본적인 개념은 데이터를 기억해 두었다가, 변경사항이 있으면, UI를 업데이트 시키는 것이다. 즉, 데이터의 관리와 UI 업데이트를 분리시키지 않겠다는 것이다.

그렇다면, 이전 데이터를 어떻게 기억할까? → 여기서 나타나는 개념이 `snapshot`이다.

우리가 `snapshot()`이라는 함수를 호출하는 그 순간, DataSource에 저장되어있던 데이터들을 commit 한다. 그다음 데이터가 변경된 후 다시 한 번 snapshot()을 호출하게 되면, 이전 snapshot과 비교해서 어떤 데이터가 달라졌는지 파악하고 그것을 애니메이션으로 보여주는 것이다. (option을 이용해서 애니메이션을 끌 수 있다)

dataSource는 데이터의 변경사항을 어떻게 인지할 수 있을까? → 이를 위해 우리는 Item에 반드시 Hashable 프로토콜을 채택해줘야한다. 즉, Diffable Data Source는 이전 데이터들의 ID를 기억해두고, 그 값들이 변하면 데이터가 변했음을 인식할 수 있다는 것이다!

이제 우리는 `performBatchUpdates` 없이 모든 변경사항에 대한 책임을 DataSource에게 위임하게 되었다. 그리고 DiffableDataSource는 아주 자연스럽고 예쁜 Animation을 보여주게 된다.

DiffableDataSource는 UITableView, UICollectionView 모두 지원하며, Mac, TVOS, iOS를 지원한다. (iOS 13부터 가능)

***

#### 앱을 통한 데모 (사용 방법)

💡 내 마음대로 설명할거임.. WWDC 영상 보는 거 추춴 💡

요약하자면 세 단계로 데이터 변경을 쉽게 처리할 수 있다.

1. Diffable DataSource 등록
2. Snapshot 만들기
3. Snapshot 적용

이제 자세하게 알아보자.

1.  Diffable DataSource 등록

    UITableVIew나 UICollectionView를 만들 때, 기본 DataSource를 등록하게 된다. 이때 DiffableDataSource에 기본 데이터 값을 넣어준다.
2.  변경 사항이 생긴 경우 Snapshot을 만들고, Snapshot을 적용한다.

    기존 PrefetchData와 비슷한 형태이다. 즉, 기존 데이터를 기준으로 snapshot을 생성하고, data의 변경사항을 처리한 후에 snapshot을 적용 (apply)해주기만 하면 UI가 알아서 변경되는 형태이다.

***

```swift
// 화면에 보여줄 Item - Hashable protocol 채택 필요
struct Shortcut: Hashable {
	var title: String
	var category: String
}

class ViewController: UIViewController {
    
    enum Section {
        case category
        case popular
        case latest
        
        var headerTitle: String {
            switch self {
            case .category:
                return "카테고리 별 단축어"
            case .popular:
                return "인기있는 단축어"
            case .latest:
                return "최신 단축어"
            }
        }
    }
    
    // diffable dataSource 선언
    private var dataSource: UITableViewDiffableDataSource<Section, Shortcut>!
    
    @IBOutlet weak var tableView: UITableView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupDataSource()
        setSnapshot()
        
    }
    
    // dataSource 등록
    private func setupDataSource() {
        
        dataSource = UITableViewDiffableDataSource<Section, Shortcut>(tableView: tableView) { tableView, indexPath, item in
            guard let cell = tableView.dequeueReusableCell(withIdentifier: ShortcutTableViewCell.id, for: indexPath) as? ShortcutTableViewCell else { return UITableViewCell() }
            cell.config(with: item)
            return cell
        }
    }
    
    private func setSnapshot()  {
        let category = [
            Shortcut(title: "단축어1", subTitle: "단축어를 써보세요"),
            Shortcut(title: "단축어2", subTitle: "쉽게 써 보자"),
            Shortcut(title: "단축어3", subTitle: "꿀도 좀 빨아보자")
        ]
        
        let popular = [
            Shortcut(title: "단축어4", subTitle: "단축어를 써보세요"),
            Shortcut(title: "단축어5", subTitle: "쉽게 써 보자"),
            Shortcut(title: "단축어6", subTitle: "꿀도 좀 빨아보자")
        ]
        
        let latest = [
            Shortcut(title: "단축어7", subTitle: "단축어를 써보세요"),
            Shortcut(title: "단축어8", subTitle: "쉽게 써 보자"),
            Shortcut(title: "단축어9", subTitle: "꿀도 좀 빨아보자")
        ]
        
        // snapshot 적용
        var snapshot = dataSource.snapshot()
        snapshot.appendSections([.category, .latest, .popular])
        snapshot.appendItems(category, toSection: .category)
        snapshot.appendItems(popular, toSection: .popular)
        snapshot.appendItems(latest, toSection: .latest)
        dataSource.apply(snapshot)
    }
}
	
```

***

#### Diffable DataSource를 활용하는 방법

> * snapshot 생성 방법에는 2가지가 있다. - 기존 dataSource를 기반으로 snapshot을 생성하는 방법 - 새로운 snapshot을 생성하는 방법

우리는 여기서 왜 두가지 생성방법이 있을까? 를 반드시 생각해봐야한다. 여기서 젤 중요한 개념은 이거다.

> 💡 diffableDataSource는 snapshot을 만들 때, **복사본을 return** 한다.

분명 아까 설명할 땐, diffable data source를 만들게 되면, viewController에는 원본 데이터를 가지고 있을 필요가 없다고 설명했었다. 그런데, snapshot을 만들 때 복사본을 return 한다고? 말도 안되는 상황인거다. (이거 때문에 진짜 삽질 엄청 많이 했다)

이해가 안될 수도 있기 때문에 조금 더 설명을 해보자면 다음과 같다.

무신사에서 어떤 옷에 대해 좋아요를 눌렀다고 생각해보자. 좋아요는 ID 값이 아니다. 데이터가 없어지거나 생성되는 조건이 아니고, 속성의 일부분이 **수정**된 경우가 되는 것이다.

다시 생각해보자. 우리는 dataSource의 snapshot을 가지고 와서 그 snapshot에서 변경된 데이터를 처리했었다. 근데 이게 원본 데이터가 아니라 “복사본”이기 때문에, 아무리 복사본을 변화시킨다고 해도, 그 변경이 보이지 않는 것이다.

즉, 기존 dataSource의 snapshot을 기반으로 아무리 속성을 변경해봐도, 데이터가 변하지 않는다는것이다.

그럼 어떻게하면되는데\~!~~!~~!~~!~~!

데이터를 삭제하고, 다시 넣어라\~! 라는게 Apple의 답변인 듯 하다 ^^

이를 위해 `insertItem(from: )` 이라는 함수가 존재한다.

즉, 사용자가 A라는 옷에 좋아요를 눌렀다면, A데이터를 삭제했다가 insertItem을 기반으로 원위치에 삽입시킨다면, 애플이 알아서 애니메이션을 보여주겠다\~ 라는 것이다.

그 이외에도 insert(before)이라는 함수도 있으니 데이터 순서 고려해서 너네가 알아서 작성해\~ 대신 insert(before: )는 데이터가 없을 때 append로 작동하게 만들어뒀어\~\~\~\~ 라는게 요약\~

**indexPath**

이제 우리는 ViewController에 데이터를 가지고 있지 않다. diffableDataSource가 그 정보를 모두 가지고 있기 때문이다. 그래도 아직 우리는 indexPath에 대한 정보를 알아야한다. 왜냐! 어떤 Cell을 탭 했을 때, 다른 뷰로 이동해야되기 때문이다. 즉, **UITableViewDelegate**를 구현할 때, indexPath정보가 필요하단 것이다.

이전에 Item에 Hashable 프로토콜을 지정했고, 그렇기 떄문에 그 Item을 찾는데 선형시간의 탐색 시간이 걸린다. 코드는 다음과 같다. (optional로 return 하기 때문에 guard let 사용 추천!)

```swift
let item = dataSource.itemIdentifier(for: indexPath)
```

***

#### 개발하며 고려해야할 점 (그냥 자랑인 듯)

* apply()는 BackgroundQueue에서 동작하기 때문에, mainQueue로 가져와서 실행하자
* AirDrop도 DiffableDataSource로 만든거야\~ 애니메이션 멋지지?

***

#### 영상

[Advances in UI Data Sources - WWDC19 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2019/220/)
