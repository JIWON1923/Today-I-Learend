# MVVM

### 들어가기 전

#### ViewModel이란?

이제 기존 MVC 프로젝트를 MVVM으로 리팩토링하기 전에, ViewModel에 대해서 간단하게 알아보자.

ViewModel은 View에서 일어난 변경을 감지해서 Model에게 그 변경을 전달한다.

예를 들어, 사용자가 음악을 검색하면, 입력할 때마다 또는 검색 버튼을 누를 때 ViewModel이 그 변경을 감지하는 것이다. 이후에 Model에 그 변경을 전달하기 위해 NetworkManager라는 계층에게 데이터 변경을 요청하는 역할을 담당한다.



기존 MVC의 ViewController와 MVVM의 ViewController의 차이를 살펴보자.

유저의 입력에 대한 변경을 ViewModel이 처리하게 되었고, 네트워크 요청의 역할도 ViewModel이 담당하게 되었다. 그래서 View(기존 ViewController)는 뷰를 보여주고, TableView를 reload하는 역할만 남게 되었다.



#### MVVM을 구현하기 위한 조건

ViewModel은 어떤 변화를 감지할 수 있어야한다. 사용자가 검색어를 입력할 때, 그 변화를 뷰모델이 감지해야하기 때문이다.&#x20;

하지만 UIKit은 그런 바인딩 엔진을 제공해주지 않는다. 따라서 UIKit에서 MVVM을 구현하기 위해서는 데이터 바인딩 엔진을 직접 구현해주어야한다.

(물론 Combine이나 RxSwift 등을 활용하여 상태의 변화를 바인딩할 순 있다. 채용 공고에 나오는 MVVM 대부분 Combine이나 RxSwift를 이용해서 구현하는 것 같더라. 하지만 나는 VM을 사용하는 이유조차.. 모르겠다..)

데이터 바인딩 엔진을 구현하는 방법은 많지만, 나는 제네릭 타입의 Observable 클래스를 만들어서 사용했다. (Boxing이라는 키워드로 검색하면 많은 자료가 나올 것이다)

{% hint style="info" %}
\[정리] MVVM을 구현하기 위한 조건

1. 관찰 가능한 객체
2. 모델
3. 뷰 모델
4. 뷰 컨트롤러 (View)
{% endhint %}

## ViewModel

### 관찰 가능한 객체 (Observable)

* UIKit은 시스템 엔진에서 observable한 형태를 제공해주지 않는다.
* 이에 UIKit에서 MVVM을 구축하기 위해서는 객체를 관찰가능하도록 설정해야한다.
* 여러가지 방법이 있지만, 나는 Observable 클래스를 만들어 구현했다.
* 네트워크를 통해 받아온 데이터를 Model에 주입한다.

### 코드

```swift
class Observable<T> {
    var value: T? {
        didSet {
            listener?(value)
        }
    }
    
    init(_ value: T?) {
        self.value = value
    }
    
    private var listener: ((T?) -> Void)?
    
    func bind(_ listener: @escaping (T?) -> Void) {
        listener(value)
        self.listener = listener
    }
}
```

Observable이라는 제네릭 클래스를 만들었다. 타입 T에 해당되는 값에 대해 변화를 감지하는 역할을 수행하고, 값이 변할 때마다 listener에게 알려주게 된다.

bind 함수가 이해가 어려울 수 있을 것 같다. 결론을 먼저 말하자면, 등록된 값이 변하고, listner에 등록된 함수가 끝난 후에 view에서 해야할 일을 작성해주면 된다. (이 프로젝트에서는 tableview를 reload하는 코드를 작성하면된다.)

직관적으로 설명하자면 listner 끝나고 해야할 일을 작성하는 공간이라고 이해하면 된다. 이 bind를 사용하면, viewModel의 변화를 감지할 수 있게 되는 것이다.

### 코드 (ViewModel)

```swift
struct UserListViewModel {
    
    init(){
        fetchMusicData()
    }
    
    let networkManager = NetworkManager.shared
    
    var users: Observable<[Music]> = Observable([])
    
    func fetchMusicData() {
        networkManager.requestData(term: "jazz") { result in
            switch result {
            case .success(let data):
                self.users.value = data
            case .failure(let error):
                print(error.localizedDescription)
            }
        }
    }
}

```

(지금은 검색에 대한 기능이 없어서 유저의 검색어를 관찰하는 기능이 없다)

NetworkManager와 통신하고, Model인 users를 관찰하고 있다.&#x20;

여기에는 나타나지 않았지만, 유저가 검색어를 입력할 때 fetchMusicData를 호출해주면 된다.





## View (ViewController.swift)

* 뷰모델의 변경을 감지해서 tableview를 다시 로드한다.

### 코드

```swift
final class ViewController: UIViewController {
    
    private let tableView: UITableView = {
        let table = UITableView()
        table.register(UITableViewCell.self, forCellReuseIdentifier: "cell")
        return table
    }()
    
    private var viewModel = UserListViewModel()

    override func viewDidLoad() {
        super.viewDidLoad()
        setTableView()
        setDataBinding()
    }
    
    private func setTableView() {
        view.addSubview(tableView)
        tableView.frame = view.bounds
        tableView.dataSource = self
    }
    
    private func setDataBinding() {
        viewModel.users.bind { _ in
            DispatchQueue.main.async {
                self.tableView.reloadData()
            }
        }
    }
}

extension ViewController: UITableViewDataSource {
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
        cell.textLabel?.text = viewModel.users.value?[indexPath.row].musicTitle
        return cell
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        viewModel.users.value?.count ?? 0
    }
    
}
```

ViewController는 이제 View를 그리는 역할만 가지고 있다.

그래서 ViewModel의 users 데이터를 bind하여 변화를 관찰하고, listner의 동작이 끝나면 tableView를 reload해주는 역할을 갖게 됐다.



### 결론

그래서 왜 MVVM을 사용해야할까? 언제 사용하면 좋을까?

........ 잘 모르겠다. 사실 Messive View Controller라는 이야기가 많지만, 뷰를 잘 분리하고, NetworkModel을 잘 설계하면 그 문제는 충분히 해결할 수 있을 것이라 생각한다..

구조변환은 해봤지만 장점은 잘 모르겠다

객체를 관찰하기위해 또 다른 객체 안에 담아 관리해야하고, ViewController마다 하나의 ViewModel이 필요할텐데(아닌가..?) 사람들은 이게 진짜 좋은가?

테스터블한 코드가 된다! 라고 하는데, 이 부분은 조금 더 고민해보겠다!

아직 많은 프로젝트를 해보지 않아서 이런 고민을 하는 것 같다



