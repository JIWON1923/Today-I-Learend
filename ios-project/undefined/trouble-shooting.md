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
