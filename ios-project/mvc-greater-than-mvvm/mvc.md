# MVC

이제까지 해 왔던 방법으로 View Controller를 구성해보자.

(우선 UI는 단순한 TableView에 음악 제목을 띄우기만 할 것이다.)

## ViewController

```swift
class ViewController: UIViewController {
    
    private var musicData: [Music]?
    private let networkManager = NetworkManager.shared
    
    private let tableView: UITableView = {
        let table = UITableView()
        table.register(UITableViewCell.self, forCellReuseIdentifier: "cell")
        return table
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        setTableView()
        fetchData()
    }
    
    private func setTableView() {
        view.addSubview(tableView)
        tableView.frame = view.bounds
        tableView.dataSource = self
    }
    
    private func fetchData() {
        networkManager.requestData(term: "jazz") { result in
            switch result {
            case .success(let data):
                self.musicData = data
                DispatchQueue.main.async {
                    self.tableView.reloadData()
                }
            case .failure(let error):
                print(error.localizedDescription)
            }
        }
    }
}

extension ViewController: UITableViewDataSource {
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        musicData?.count ?? 0
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)
        cell.textLabel?.text = musicData?[indexPath.row].musicTitle
        return cell
    }
}

```

View Controller의 역할을 보자.

NetworkManager의 인스턴스를 이용해서 직접 request를 하고, 받아온 정보를 tableView에 업데이트 시켜주고 있다.

또한, 뷰에 대한 코드도 가지고 있다.

즉, 지금 View Controller의 역할은 뷰를 그리고, 데이터를 요청한 후 자기 자신의 테이블을 변경하고 있는 모습을 볼 수 있다.



만약, 사용자가 앱에서 검색을 하고, 그 결과를 받아와야한다면?

View controller는 사용자의 입력을 감지하고, Network Manager에게 검색어에 대한 결과를 요청하고, NetworkManager의 결과를 받아와 Table View를 업데이트 해야한다
