---
description: baseURL이 여러개인 상황에서, Network Manager 관리해보기
---

# Network Model

## 기초 설정

### Alarmofire Package 추가

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p><a href="https://github.com/Alamofire/Alamofire.git">https://github.com/Alamofire/Alamofire.git</a></p></figcaption></figure>

### 사용할 서비스

#### REST API 관련

{% embed url="https://jsonplaceholder.typicode.com" %}

#### 모델 변형

{% embed url="https://quicktype.io" %}

## 코드 작성

### Protocol 설계 (추후 수정 필요.. ㅠ )

{% hint style="info" %}
API 종류에 따라 base URL과 header에 차이가 존재한다. \
또한 API 내부에서 호출하는 데이터의 종류에 따라 path, method에서 차이가 발생한다.
{% endhint %}

```swift
protocol API {
    associatedtype RequestData: APIData
    
    var baseURL: URL { get }
    var header: [String: String]? { get }
}
```

```swift
protocol APIData {
    associatedtype Data: Codable
    
    var path: String { get }
    var method: HTTPMethod { get }
    var requestData: Data { get }
}
```

### Protocol 구현체

```
// Some code
```



### Network Error 정의 (TODO: Alarmofire에서 제공하는 에러로 수정)

```swift
enum NetworkError: Error {
    case urlError
    case dataError
    case parseError
    case networkError
}
```



### Network Manager 작성

```swift
final class NetworkManager {
    
    private let decoder: JSONDecoder = {
        let decoder = JSONDecoder()
        decoder.keyDecodingStrategy = .convertFromSnakeCase
        return decoder
    }()
    
    typealias NetworkCompletion<T: Decodable> = (Result<T, NetworkError>) -> Void
    
    func requestData<T: Decodable>(urlString: String, completion: @escaping NetworkCompletion<T>) {
        
        guard let url = URL(string: urlString) else {
            completion(.failure(.urlError))
            return
        }
        
        performRequest(url: url) { result in
            completion(result)
        }
    }

    private func performRequest<T>(url: URL, completion: @escaping NetworkCompletion<T>) {
        
        AF.request(url, method: .get)
            .responseData { response in
                switch response.result {
                case .success(let data):
                    if let parseData = self.parseJSON(data: data) as T? {
                        completion(.success(parseData))
                    } else {
                        completion(.failure(.parseError))
                    }
                case .failure(let error):
                    print(error.localizedDescription)
                    completion(.failure(.networkError))
                }
            }
        
    }
    
    private func parseJSON<T: Decodable>(data: Data) -> T? {
        do {
            let result = try decoder.decode(T.self, from: data)
            return result
        } catch {
            return nil
        }
    }
}
```
