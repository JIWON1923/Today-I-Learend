---
description: 주간 날씨를 보여주는 Table View를 만들어보자.
---

# TableView 연결하기

#### 현재 뷰 상태

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

![](<../../.gitbook/assets/Simulator Screenshot - iPhone 14 Pro - 2023-05-06 at 21.58.16.png>)



## TableView 설정방법

### 1. TableView Cell 생성

(Cocoa Touch Class를 이용해서 생성하며, xib도 같이 생성하자.

![](<../../.gitbook/assets/image (7) (1) (1).png>)



**class file과 xib 파일 outlet 연결**

```swift
import UIKit

class WeaklyWeatherTableViewCell: UITableViewCell {

    @IBOutlet weak var weatherIconImageView: UIImageView!
    @IBOutlet weak var dateLabel: UILabel!
    @IBOutlet weak var weatherLabel: UILabel!
    @IBOutlet weak var maximumTeperatureLabel: UILabel!
    @IBOutlet weak var minimumTemperatureLabel: UILabel!
    
    
    override func awakeFromNib() {
        super.awakeFromNib()
        // Initialization code
    }

    override func setSelected(_ selected: Bool, animated: Bool) {
        super.setSelected(selected, animated: animated)

        // Configure the view for the selected state
    }
    
}

```



**xib의 Attribute Controller에서 id 설정**

![](<../../.gitbook/assets/image (12) (1).png>)



**Tableview datasource, delegate 설정**

[애플 공식 문서](https://developer.apple.com/documentation/uikit/uitableviewdatasource)

```swift
import UIKit

class ViewController: UIViewController {
    
    // delegate, datasource 설정
    @IBOutlet weak var weaklyWeatherTableView: UITableView! {
        didSet {
            weaklyWeatherTableView.dataSource = self
            weaklyWeatherTableView.delegate = self
        }
    }
    
    let weatherData = ["16", "24", "17", "34", "16", "24", "17"]

    override func viewDidLoad() {
        super.viewDidLoad()
        
        // xib 설정
        weaklyWeatherTableView.register(UINib(nibName: "WeaklyWeatherTableViewCell", bundle: nil), forCellReuseIdentifier: "weatherTableViewCell")
        
    }
}


extension ViewController: UITableViewDelegate, UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        weatherData.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        let cell = weaklyWeatherTableView.dequeueReusableCell(withIdentifier: "weatherTableViewCell", for: indexPath) as! WeaklyWeatherTableViewCell
        
        cell.maximumTeperatureLabel.text = weatherData[indexPath.row]
        
        return cell
    }
}

```



![](<../../.gitbook/assets/Simulator Screenshot - iPhone 14 Pro - 2023-05-06 at 22.25.58.png>)
