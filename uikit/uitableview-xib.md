# UITableView xib으로 만들어보기

> 프로젝트를 하다가, 여러 화면에서 같은 TableView를 사용하게 되었다. 재사용 가능한 TableView Component를 사용해보자! (xib을 이용할 거지만, viewController를 이용할 수도 있다)

#### 순서

1. UIView 생성 - 재사용 할 TableView
2. Nib 생성 - TableView에 register할 Cell
3. TableView 코드 작성
4. ViewController에서 UIView 호출하기

***

#### 1단계) UIView 생성

> 재사용할 TableView를 담아줄 UIView를 만들고, UIViewClass와 연결해준다.

**1. UIView 선택하기**

![](https://velog.velcdn.com/images/jwlee0610/post/af778ea3-1415-4f9b-8230-1baaf8395c79/image.png)

**2. TableView 넣고, AutoLayout 잡아주기**

UIView 안에 TableView constriant를 0, 0, 0, 0으로 잡아주었다. (safeArea 기준)

![](https://velog.velcdn.com/images/jwlee0610/post/2c8fd2c6-c2fc-40b4-ab35-bddc997f6cc4/image.png)&#x20;

그럼 대충 이런 모양이 될 것이다.

여기서 TableView를 선택하고, Attribute Inspector를 확인해보자.

| Storyboard 내부에 있는 Inspector                                                                        | UIView 내부에 있는 Inspector                                                                            |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| ![](https://velog.velcdn.com/images/jwlee0610/post/89bdf3ed-dd31-49bc-9441-e4097138898c/image.png) | ![](https://velog.velcdn.com/images/jwlee0610/post/9964fa17-2ce0-4189-a99b-f7f37e29fca5/image.png) |

prototype cell을 등록할 수 있는 부분이 없다....!!! ~~(복붙 하면 된다는데, 조금 더 찾아봐야겠다)~~ 그렇기 떄문에 우리는 TableViewCell을 반드시 Nib 파일로 만들어줘야한다.

#### 2단계) Nib 생성 - TableView에 register할 Cell 만들기

귀찮으니까 CocoaTouchClass에서 xib파일과 swift 파일을 한 번에 생성하자!

**1. New File - CocoaTouch Class 클릭**

![](https://velog.velcdn.com/images/jwlee0610/post/f23fcc8f-e478-4ea3-8d72-918a8fada05c/image.png)

**2. 속성 변경**

Sub class를 UITableViewCell로 바꿔주고, Also create XIB File을 선택해준다! ![](https://velog.velcdn.com/images/jwlee0610/post/f0457c70-a0e2-436c-896f-1598567417dd/image.png)

**3. Cell꾸하기**

새로 만들어진 TableViewCell.xib 파일에 들어가서 UI를 만들어준다.

![](https://velog.velcdn.com/images/jwlee0610/post/31f3bb53-20ff-4c6c-9db0-e358c3126c8d/image.png)

**4. xib 파일과 .swift 파일 연결해주기**

아까 Also create XIB file을 해줬기 때문에, 추가로 작업해줄 필요는 없다. 그냥 cell에 있는 요소들을 IBOutlet으로 연결만 해 주자! ![](https://velog.velcdn.com/images/jwlee0610/post/2e5ba350-60f1-4082-9991-3515935c1828/image.png)

#### 3단계) TableView 코드 작성

**1. TableView Swift 파일 만들기**

1단계에서 만들었던 `CustomTableView.UIView`와 연결할 Swift class를 만들어준다. ![](https://velog.velcdn.com/images/jwlee0610/post/8686248d-9db4-4d06-be58-ee2396df0440/image.png) ![](https://velog.velcdn.com/images/jwlee0610/post/5bc252e2-c00c-4657-87ad-389941e42ee7/image.png)

&#x20;나는 TableViewComponent.swift라고 이름을 지었다. 이제 이 곳에서 공통으로 쓸 TableView에 대한 Delegate와 Datasource 코드를 작성해주자!

**2. UIView.xib파일과 .swift 파일 연결하기**

1. UIView를 선택한다.

![](https://velog.velcdn.com/images/jwlee0610/post/4a4fc658-db4b-427f-a78a-6088bee5107a/image.png)

2. Custom class에 만들어준 TableView.swift 파일을 연결한다. custom class를 연결하면, IBOutlet을 연결할 수 있다! (안 되면 Xcode를 껐다 켜거나, 코드로 작성한 후에 코드에서 UIView로 연결해주자)

![](https://velog.velcdn.com/images/jwlee0610/post/6df00821-3bd8-402e-ab05-226517df726b/image.png)

3. tableView를 IBOutlet으로 연결해준다.

**2. TableView 코드 작성하기**

이제 각자의 데이터와 셀 모양에 맞춰서 dataSource를 만들어주면 된다. 나는 Cell에 String 데이터만 넣을 것이기 때문에, data를 \[String] 형태로 만들어줬다. 또한 외부에서 데이터를 주입한 후에 tableView를 새로고침해줘야하기 때문에 data는 private으로 설정하고, setData를 통해 데이터를 주입할 수 있도록 설정했다!

```swift
import UIKit

final class TableViewComponent: UIView {
    
    private var data: [String] = []
    @IBOutlet weak var tableView: UITableView!
    
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
    
    func setupTableView() {
        tableView.delegate = self
        tableView.dataSource = self
        tableView.register(UINib(nibName: CustomTableViewCell.id, bundle: .main), forCellReuseIdentifier: CustomTableViewCell.id)
    }
    
    /// view controller에서 data 주입
    func setData(with data: [String]) {
        self.data = data
        tableView.reloadData()
    }
}

extension TableViewComponent: UITableViewDataSource, UITableViewDelegate {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        data.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        guard let cell = tableView.dequeueReusableCell(withIdentifier: CustomTableViewCell.id, for: indexPath) as? CustomTableViewCell else {
            return UITableViewCell()
        }
        cell.titleLabel.text = data[indexPath.row]
        return cell
    }
}

```

#### 4단계) ViewController에서 UIView 호출

1. UIView 만들기 Main.storyboard 등에 이동해서 tableview를 넣고 싶은 위치에 UIView를 넣어주자. 우리는 이걸 viewDidLoad에서 우리가 만들어준 TableView를 넣어 줄 것이다.

![](https://velog.velcdn.com/images/jwlee0610/post/9800f075-865e-43c1-a777-feb845d334ba/image.png)&#x20;

나는 background에 하얀색 배경을 깔아두었고, 하얀색 View에 내가 만든 TableView를 넣을 것이다.

2. UIView에 custom view 주입

```swift
import UIKit

class ViewController: UIViewController {

    @IBOutlet weak var customView: TableViewComponent!
    
    private var data = ["1", "2", "3", "4", "5"]
    override func viewDidLoad() {
        super.viewDidLoad()
        
        setupCustomTableView()
    }
    
    private func setupCustomTableView() {
        guard let customTableView = Bundle.main.loadNibNamed("TableViewComponent", owner: nil)?.first as? TableViewComponent else { return }
        customTableView.frame = customView.bounds
        customTableView.setupTableView()
        customTableView.setData(with: data)
        customView.addSubview(customTableView)
    }
}

```

![](https://velog.velcdn.com/images/jwlee0610/post/63e6d2f7-85a1-456b-b478-24a945ad6d98/image.png)
