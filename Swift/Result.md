# Result


### Result type

<aside>
๐ก ์๋ฌ๊ฐ ๋ฐ์ํ๋ ๊ฒฝ์ฐ, ์๋ฌ๋ฅผ ์ธ๋ถ๋ก ๋์ง์ง ์๊ณ , Result ํ์์ ํจ์ ์คํ ๊ฒฐ๊ณผ๋ฅผ ๋ด์ ๋ฆฌํดํ  ์ ์๋ค.

</aside>

- ๋ด๋ถ์ ์ผ๋ก ์ด๊ฑฐํ์ผ๋ก ๊ตฌํ๋์ด์๋ค.
    - `enum **Result<Success, Failure>** where Failure: Error`
    - ์ฑ๊ณต๊ณผ ์คํจ ์ ๋ณด๋ฅผ ํจ๊ป ๋ด์์ ๋ฆฌํดํ๋ค.
    - Failure์ ๊ฒฝ์ฐ, Error ํ๋กํ ์ฝ์ ์ฑํํด์ผํ๋ค.

### throwing๊ณผ์ ๋น๊ต

```swift
// 1๋จ๊ณ: ์๋ฌํ์ ์ ์

enum HeightError: Error {
    case maxHeight
    case minHeight
}

// 2๋จ๊ณ: throwing ํจ์ ์ ์
func checkingHeight(height: Int) -> throws Bool {
    if height > 190 {
        throw HeightError.maxHeight
    else if height < 130 {
        throw HeightError.minHeight
    } else {
        if height >= 160 {
            return true
        } else {
            return false
        }
    }
}

// 3๋จ๊ณ: ์๋ฌ์ฒ๋ฆฌ
do {
    let _ = try checkingHeight(height: 200)
    print("๊ฐ๋ฅ")
} catch {
    print("๋ถ๊ฐ๋ฅ"
}

// 2๋จ๊ณ: Result type ํจ์ ์ ์
func CheckingHeight(height: Int) -> Result<Bool, HeightError> {
    if height > 190 {
        return Result.failure(HeightError.maxHeight)
    } else if height < 130 {
        return Result.failure(HeightError.minHeight)
    } else {
        if height >= 160 {
            return Result.success(true)
        } else {
            return Result.success(false)
        }
    }
}

// 3๋จ๊ณ: ์๋ฌ์ฒ๋ฆฌ
let result = CheckingHeight(height: 200)

switch result {
case .success(let data):
    print("๊ฐ๋ฅ")
case .failure(let error):
    print("๋ถ๊ฐ๋ฅ")
}
```

### Result Type์ ํ์ฉ

```swift
do {
    let data = try result.get()
    print("๊ฒฐ๊ณผ = \(data)")
} catch {
    print(error)
}
```

- ๋ค์ํ ๋ฉ์๋๊ฐ ์กด์ฌํ๋ค.
    - `get()` ์ ๊ฒฐ๊ณผ๋ฅผ throwingํ๋ ํจ์์ฒ๋ผ ๋ณํ๊ฐ๋ฅํ ํจ์์ด๋ค. (Success value๋ฅผ ๋ฆฌํดํ๋ค)



### Result ํ์์ ์ฌ์ฉํ๋ ์ด์ ๋?

- ์ฑ๊ณต, ์คํจ์ ๊ฒฝ์ฐ๋ฅผ ๊น๋ํ๊ฒ ์ฒ๋ฆฌํ  ์ ์๋ค.
- ๊ธฐ์กด ์๋ฌ์ฒ๋ฆฌ๋ฅผ ์์ ํ ๋์ฒดํ๋ ค๋ ๋ชฉ์ ์ด ์๋๋ผ, ์๋ฌ ์ฒ๋ฆฌ์ ๋ค์ํ ์ต์์ ์ ๊ณตํ๋๊ฒ์ด๋ค.
- ์๋ฌํ์์ ๋ช์์ ์ผ๋ก ์ ์ธํด์ ํ์ ์บ์คํ์ด ๋ถํ์ํด์ง๋ค.




### Network์์ Result ํ์์ ํ์ฉ


```swift
enum NetworkError: Error {
    case .someError
}

func performRequest(with url: String, completion: @escaping (Result<Data, NetworkError> -> void) {
    
    guard let url = URL(string: url) else { return }

    URLSession.shared.dataTask(with: url) { (data, response, error) in 
        if let error {
            completion(.failure(.someError))
            return
        }

        guard let data else {
            completion(.failure(.someError)
            return
        }

        completion(.success(data))
    }.resume()
}

performRequest(with: "address") { result in
    switch result {
    case .failure(let error):
        print(error)
    case .success(let data):
        // data ์ฒ๋ฆฌ
    }
}
```
