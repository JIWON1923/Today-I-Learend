# MVVM

{% hint style="info" %}
MVVM을 구현하기 위한 조건

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

##

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





