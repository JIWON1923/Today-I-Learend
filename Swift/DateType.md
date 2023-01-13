# DateType

## Date

### UTC (Coordinated Universal Time)

- 국제적인 표준 시간 - **협정 세계시**
    - 기존 평균 태양시인 **그리니치 표준시(GMT)를 대체**하여 사용한다.
    - 일반적으로 GMT 시간과 UTC 시간을 혼용해서 사용한다.
    - 영국, 런던의 그리니치 천문대의 시간을 기준으로 하는 시간
- 예시
    - 우리나라: UTC + 9 (영국을 기준으로 9시간 앞선다)
    - 뉴욕 : UTC - 5 (5시간 느리다.)
    - 영토가 넓은 나라는 여러개의 표준시를 사용한다. (중국은 1개)
        - 미국(5), 러시아(11)

### Date 구조체

<aside>
💡 Swift에서 기본으로 제공하는 날짜를 다루는 구조체이다.

</aside>

- `Date()` : 현재 시점의 Date 인스턴스가 생성된다.
    - 기준 시점 (reference data, 2001.01.01 00:00:00 UTC)에서 몇 초가 떨어져있는지를 계산한다.
        - `.timeIntervalSinceReferenceDate`
        - `TimeInterval` Type, 초 단위의 개념이다.
- 양력, 음력 등 타임 존의 영향을 받지 않는 독립적인 시간의 값이다.
    - 암시적인 날짜와 시간으로 구성되어있다.
    - 달력, 타임존 (지역)을 변환하여 사용해야한다.
    - 필요한 경우 적절한 형식의 문자열 타입으로 변환해서 사용할 수 있다.

```swift
let now - Date() // 생성 시점의 날짜와 시간 (초 단위)
print(now) // UTC 기준의 시간 출력
now.timeIntervalSinceReferenceDate 
```


## Calendar

### Date의 실제 활용 방법

1. 양력, 음력의 달력을 다루는 **Calendar 구조체** 활용
2. 원하는 형식의 문자열로 변형하기 위한 **DateFormatter 클래스** 활용

### Calendar구조체

- 절대시점 Date를 연대/연도/날짜/요일 등의 달력의 요소로 변환을 도와준다.
- `Calendar.current` 그레고리력(Gregorian calendar: 양력)
    - `Calendar.autoupdatingCurrent` 유저가 선택한 달력 기준 (세계적 서비스)
- 지역 설정
    - 나라마다 날짜와 시간을 표기하는 형식과 언어가 다름
    
    ```swift
    var calendar = Calendar.current
    
    // 달력의 지역, 달력 표기 방법
    calendar.locale
    
    // timezone, UTC 시간과 관련된 개념
    calendar.timeZone
    
    calendar.locale = Locale(identifier: "ko_KR")
    ```
    

### Date의 년/월/일/시/분/초 확인

```swift
// 1) 날짜 - 년 / 월 / 일
let year: Int = calendar.component(.year, from: now)
let month: Int = calendar.component(.month, from: now)
let day: Int = calendar.component(.day, from: now)

// 2) 시간 - 시 / 분 / 초
let timeHour: Int = calendar.component(.hour, from: now)
let timeMinute: Int = calendar.component(.minute, from: now)
let timeSecond: Int = calendar.component(.second, from: now)

// 요일
let weekday: Int = calendar.component(.weekday, from: now) // 일: 1, 월: 2..
```

<aside>
💡 실제 앱에서 표기할 때, 위처럼 분리하여 Label에 표시한다.

</aside>

```swift
let myCal = Calendar.current

var myDateCom = myCal.dateComponents([.year, .month, .day], from: now)
//myCal.dateComponents(<#T##components: Set<Calendar.Component>##Set<Calendar.Component>#>, from: <#T##Date#>)

myDateCom.year
myDateCom.month
myDateCom.day

print("\(myDateCom.year!)년 \(myDateCom.month!)월 \(myDateCom.day!)일")
```

### 예시

1. 달력을 기준으로 나이 계산

```swift
class Dog {
    var name: String
    var yearOfBirth: Int
    
    init(name: String, year: Int) {
        self.name = name
        self.yearOfBirth = year
    }
    
    // 나이를 계산하는 계산 속성
    var age: Int {
        get {
            let now = Date()
            let year = Calendar.current.component(.year, from: now)
            return year - yearOfBirth
        }
    }
}

let choco = Dog(name: "초코", year: 2015)
choco.age
```

1. 열거형으로 용리을 만들고, 오늘의 요일 계산

```swift
enum Weekday: Int {
    case sunday = 1, monday, tuesday, wednesday, thursday, friday, saturday
    
    static var today: Weekday {
        let weekday: Int = Calendar.current.component(.weekday, from: Date()) 
        return Weekday(rawValue: weekday)!
    }
}

// 오늘이 무슨요일인지
let today = Weekday.today
```

1. 두 날짜 사이의 일 수 계산

```swift
let startDate = Date()
let endDate = startDate.addingTimeInterval(3600 * 24 * 60) // 60일 후

let calendar2 = Calendar.current
let someDays = calendar2.dateComponents([.day], from: startDate, to: endDate).day!

print("\(someDays)일 후")
```

## DateFormatter

### Date Formatter

<aside>
💡 날짜와 시간을 원하는 형식의 문자열로 변환하는 방법을 제공하는 클래스이다. RFC 3339 표준으로 작성되었다.
Date를 특정 형식의 문자열로 변환하기 위해서는 **지역, 시간대, 날짜형식, 시간형식**을 지정해야한다.

</aside>

### 기본적인 사용 방법

```swift
let formatter = DateFormatter()

// 1. 지역 설정
formatter.locale = Locale(identifier: "ko_KR")

// 2. 시간대 설정, default 값 존재
formatter.timeZone = TimeZone.current

// 3. 날짜 형식 설정
formatter.dateStyle = .full           // "Tuesday, April 13, 2021"
//formatter.dateStyle = .long         // "April 13, 2021"
//formatter.dateStyle = .medium       // "Apr 13, 2021"
//formatter.dateStyle = .none         // (날짜 없어짐)
//formatter.dateStyle = .short        // "4/13/21"

// 4. 시간 형식 선택
formatter.timeStyle = .full           // "2:53:12 PM Korean Standard Time"
//formatter.timeStyle = .long         // "2:54:52 PM GMT+9"
//formatter.timeStyle = .medium       // "2:55:12 PM"
//formatter.timeStyle = .none         // (시간 없어짐)
//formatter.timeStyle = .short        // "2:55 PM"

// 사용
let timeString = formatter.string(from: Date())
print(timeString)
```

### 커스텀 형식으로 생성

```swift
let formatter = DateFormatter()
formatter.locale = Locale(identifier: "ko_KR")
formatter.dateFormat = "yyyy년 MMMM d일(E)"

let timeString = formatter.string(from: Date())
```

### 문자열에서 Date로 변환

```swift
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy/MM/dd"

let date = newFormatter.date(from: "2023/01/01)
print(date) // Date 형식
```

### 두 날짜 사이의 차이

```swift
let start = Date()
let end = start.addingTimeInterval(3600 * 24 * 60) // 60일 후
let formatter  DateFormatter()

formatter.locale = Locale(identifier: "ko_KR")
formatter.timeZone = TimeZone.current
// formatter.timeZone = TimeZone(identifier: "Asiz/Seoul")

formatter.dateStyle = .long
formatter.timeStyle = .none

print("기간: \(formatter.string(from: start)) - \(formatter.string(from: end))")
```


## Date Component

### DateComponents

- 날짜와 시간의 요소들을 다룰 수 있는 구조체
- 원하는 특정 날짜, 시간을 생성할 수 있다.


```swift
var components = DateComponents()
components.year = 2021
components.month = 1
components.day = 1

components.hour = 12
components.minute = 30
components.second = 0

let specifiedDate: Date = Calendar.current.date(fromt: components)!

```

```swift
// 구조체의 확장
extension Date {
    init?(y year: Int, m month: Int, d day: Int) {
        var components = DateComponents()
        components.year = year
        components.month = month
        componetns.day = day

    guard let date = Calendar.curretn.date(from: components) else {
        return nil // 실패가능생성자가 필요한 이유
    } 

    self = date // 구조체이기때문에 self에 새로운 인스턴스 할당 방식으로 초기화 가능
                // 힙에 만들지 않기 때문에 가능 self vs Self 비교
    }
}
```

### datecomponents

- 절대적 시점(초기준)의 인스턴스를 만들 수 있다.
- 원하는 날짜, 시간으로 세팅하는 방법이다.

### 지속적으로 사용하는 구현

- 계속 사용하게 될 코드는 성격을 파악해서 속성이나 메서드 등으로 구현해서 코드에 넣는다.
- 실제로는 계산 속성(보편적), 타입 메서드, 셍성자, 서브스크립트 등으로 구현한다.
