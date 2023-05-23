---
description: MVVM은 무엇이고, 왜 사용해야할까?
---

# MVC -> MVVM으로 구조 변경해보기

많은 글들을 보고, 코드를 봤지만 나는 아직도 왜 MVVM이 필요한지 모르겠다..

그래서 MVC를 MVVM으로 리팩토링해보며 왜 필요한 지 공부해보고자 한다.

우선 내가 만들고자 하는 앱은 iTunes API를 이용해 음악 정보를 받아오는 아주 단순한 앱이다.

MVVM, MVC에서 공통적으로 사용하는 코드는 다음과 같다.

## 공통 코드

### 모델

```swift
import Foundation

struct MusicData: Decodable {
    let resultCount: Int
    let results: [Music]
}

struct Music: Decodable {
    
    let albumImage: String
    let musicTitle: String
    let artist: String
    let albumName: String
    let releaseDate: String
    
    enum CodingKeys: String, CodingKey {
        case albumImage = "artworkUrl100"
        case musicTitle = "trackName"
        case artist = "artistName"
        case albumName = "collectionName"
        case releaseDate = "releaseDate"
    }
}

```

### 네트워크 통신

```swift
import Foundation

enum NetworkError: Error {
    case urlError
    case networkError
    case dataError
    case parseError
}

final class NetworkManager {
    
    static let shared = NetworkManager()
    private init() {}
    
    func requestData(term: String, completion: @escaping (Result<[Music], NetworkError>) -> Void) {
        
        let baseURLString = "https://itunes.apple.com"
        
        guard var urlComponents = URLComponents(string: baseURLString) else {
            completion(.failure(.urlError))
            return
        }
        
        let mediaQueryItem = URLQueryItem(name: "media", value: "music")
        let termQueryItme = URLQueryItem(name: "term", value: term)
        
        urlComponents.path = "/search"
        urlComponents.queryItems = [mediaQueryItem, termQueryItme]
        
        guard let url = urlComponents.url else {
            completion(.failure(.urlError))
            return
        }
        
        fetchData(url: url) { result in
            completion(result)
        }
    }
    
    private func fetchData(url: URL, completion: @escaping (Result<[Music], NetworkError>) -> Void) {
        
        let session = URLSession(configuration: .default)
        
        let task = session.dataTask(with: url) { data, response, error in
            
            if let error {
                print(error.localizedDescription)
                completion(.failure(.networkError))
                return
            }
            
            guard let data else {
                completion(.failure(.dataError))
                return
            }
            
            if let musics = self.parseJSON(data) {
                completion(.success(musics))
            } else {
                completion(.failure(.parseError))
            }
        }
        
        task.resume()
    }
    
    private func parseJSON(_ data: Data) -> [Music]? {
        do {
            let musicData = try JSONDecoder().decode(MusicData.self, from: data)
            return musicData.results
        } catch {
            return nil
        }
    }
}
```
