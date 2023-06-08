# Trouble shooting

## AutoLayout

### storyboard ID를 설정해도 id를 찾을 수 없다는 에러가 발생함.

`Storyboard doesn't contain a view controller with identifier  "ChattingViewController"`

#### 상황 설명

1.  Main View Controller에서 Sign In 버튼을 클릭하면, ChattingViewController가 나와야함.

    ![](<../../.gitbook/assets/image (25).png>)
2.  ChattingViewController 설정

    ![](<../../.gitbook/assets/image (33).png>)
3. 내가 작성한 코드

```swift
    @IBAction func didTappedSignInButton(_ sender: UIButton) {
        
        guard let email = emailTextField.text,
              let password = passwordTextField.text else { return }
        
        Auth.auth().signIn(withEmail: email, password: password) { [weak self] result, error in
            guard let strongSelf = self else { return }
            
            if let error { return }
            guard let viewController = self?.storyboard?.instantiateViewController(withIdentifier: "ChattingViewController") as? ChattingViewController else { return }
            self?.present(viewController, animated: true, completion: nil)
        }
    }
```

#### 원인과 해결과정

Main.storyboard에서 storyboard를 관리하지 않고, storyboard reference를 사용하고 있었다.

그런데 해당 reference를 선언해주지 않아서 self.storyboard에서 해당 ID를 찾을 수 없던 것이었다.

그래서 storyboard reference를 다시 선언해서 문제를 해결했다. (push로 보여줬던 연결을 끊고 삭제했었다)

![](<../../.gitbook/assets/image (9) (1).png>)



#### 추가. 새로운 네비게이션으로 이동하는 방법

```swift
    // MARK: function
    private func moveUserListViewController() {
        let storyboard = UIStoryboard(name: "UserList", bundle: nil)
        let viewController = storyboard.instantiateViewController(withIdentifier: "UserList") as! UserListViewController
        let navigationController = UINavigationController(rootViewController: viewController)
        navigationController.modalPresentationStyle = .fullScreen
        
        self.present(navigationController, animated: true, completion: nil)
    }
    
    // MARK: IBAction
    @IBAction func didTappedSignInButton(_ sender: UIButton) {
        
        guard let email = emailTextField.text,
              let password = passwordTextField.text else { return }
        
        Auth.auth().signIn(withEmail: email, password: password) { [weak self] result, error in
            guard let strongSelf = self else { return }
            
            if let error {
                let alert = UIAlertController(title: "오류", message: "오류가 발생했습니다.", preferredStyle: .alert)
                return
            }
            self?.moveUserListViewController()
        }
    }
```



## Firestore

### FIRInvalidArgumentException', reason: 'Unsupported type: \_\_SwiftValue

* 채팅 방을 생성하기 위해, 참여자를 기준으로 현재 채팅 방이 존재하는지, 존재하지 않는지 확인하기 위해 코드를 작성했다.
* 그래서 users에 \[currentUser, tableView에서 선택한 user]를 기준으로 chatroom에 존재하는 방이 있는지 코드를 작성했다.
* 그런데 FIRInvalidArgumentException', reason: 'Unsupported type: \_\_SwiftValue 이 에러가 발생한다;;



* 결론: 비어있는 collection에 접근했기 때문이다.
  * chatrooms collection과 documents가 모두 비어있었다.
  * 그래서 내가 선언한 codable대로 비어있는 데이터 하나를 만들어주니 조회가 잘 된다..... 내 삽질...

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

### Chat의 변화만 감지할 순 없을까?

우선 위 사진에서 볼 수 있듯 나는 Chat Rooms를 Collection으로 만들었다. 즉, 채팅이 변하면, ChatRoom이 변하게 되는 것이고, 그것에 대한 결과를 보여준 다는 것이다.

이럴 때 어떤 문제가 발생하느냐! 변경된 데이터를 가져와서 tableview를 reload해주기 때문에, 전체 채팅이 새로고침되게 된다. (백문불여일견 영상을 보자)

#### 문제가 된 영상

{% file src="../../.gitbook/assets/Simulator Screen Recording - iPhone 14 Plus - 2023-06-08 at 20.48.48.mp4" %}

#### 문제가 된 코드

```swift
    func setUpDataListener(id: String, completion: @escaping ([Chat]) -> Void) {
        let reference = database.collection("chatRooms").document(id)
        
        reference.addSnapshotListener {document, error in
            guard let document, document.exists,
                  let chatData = document.data()?["chat"] as? [[String: Any]] else {
                print("invalid format")
                return
            }
            
            do {
                let data = try JSONSerialization.data(withJSONObject: chatData, options: [])
                let chats = try JSONDecoder().decode([Chat].self, from: data)
                completion(chats)
            } catch { }
        }
    }
```

reference인 ChatRooms에 snapshot Listner를 달았다. 그래서 Chat이 추가되면, ChatRoom의 변화가 감지되고, 그 결과 chatData에 \["chat"]으로 오는 값들이 추가되게 된다 ㅠ ㅠ

#### 해결해보자!
