![header](https://capsule-render.vercel.app/api?type=rect&color=auto&height=120&section=header&text=Issue%20Tracker&fontSize=100)

## Issue Tracker

GitHub에서 사용하는 Issue, label, milestone을 관리할 수 있는 앱입니다.



### 🗓 프로젝트 기간

2022.06.13 ~ 2022.07.01



### 👀 Screenshots

<pre>
<img src="https://user-images.githubusercontent.com/92504186/177483585-0b68dbab-e2c7-4e8c-941c-6980c03e41c8.jpg" alt="SS2022-07-06PM03 28 08" width="30%;" />&nbsp;<img src="https://user-images.githubusercontent.com/92504186/177485329-63e99c40-c030-4519-9f88-b9f2ae6b9c43.jpg" alt="SS2022-07-06PM03 37 29" width="30%;" />&nbsp;<img src="https://user-images.githubusercontent.com/92504186/177485337-913a1e12-5b08-4f3c-9245-cf0801e53375.jpg" alt="SS2022-07-06PM03 37 45" width="30%;" />&nbsp;<img src="https://user-images.githubusercontent.com/92504186/177485344-786abe02-1e70-4c9b-822a-5386ff62e908.jpg" alt="SS2022-07-06PM03 37 47" width="30%;" />&nbsp;<img src="https://user-images.githubusercontent.com/92504186/177485355-3e241a0a-1f6a-4f3d-a7a0-ca395606b53f.jpg" alt="SS2022-07-06PM03 37 53" width="30%;" />&nbsp;<img src="https://user-images.githubusercontent.com/92504186/177485360-2970a481-1e79-4796-85c1-4404d0eee44c.jpg" alt="SS2022-07-06PM03 38 00" width="30%;" />&nbsp;<img src="https://user-images.githubusercontent.com/92504186/177485364-ef237bbf-71d2-4844-a26f-961edad9eb2d.jpg" alt="SS2022-07-06PM03 38 02" width="30%;" />
</pre>



<img src="https://user-images.githubusercontent.com/92504186/177494192-93ff6fe6-b150-4cdd-b1f3-4688f70d3b22.gif" alt="SS2022-07-06PM04 27 22" width="30%;" />

### 📌 Keywords

**Architecture**: `MVVM`, `Clean Architecture`

**Library**: `SwiftLint`, `SnapKit`, `SwiftyMarkdown`



### 🔥 Trouble Shooting

1. Delegate 책임을 ViewController로부터 분리하여 발생하는 불필요한 통신 로직(Delegate/ Observer) 제거

	- 기존에 ViewController에 있던 Delegate 역할을 분리하여 새로운 객체를 만들다보니, Delegate 객체와 ViewController간 통신 로직이 필요한 상황이 발생했습니다.

	- 이 문제를 해결하고자, TextFieldDelegate의 경우에는 ViewController에서 ViewModel과 Delegate 역할을 할 TextField의 Action과 바인딩을 해주어 TextFieldDelegate를 제거하여 불필요한 통신 로직을 제거할 수 있었습니다.

		```swift
		// EditingIssueViewController
		func bind(to viewModel: EditingIssueViewModelProtocol) {
		    ...
		    titleViewComponents.titleTextField.addAction( UIAction { [weak self] _ in
		        viewModel.didChangeTitleText(self?.titleViewComponents.titleTextField.text)
		    })
		    ...
		}
		```

	- TextView의 경우에는 addAction 메소드를 사용할 수 없기 때문에, Delegate 메소드가 실행될 때 진행될 클로저 타입의 변수를 추가하여 불필요한 통신 로직을 제거할 수 있었습니다.

		```swift
		final class EditingIssueTextViewDelegate: NSObject, UITextViewDelegate {
		    private var onUpdateText: (String) -> Void = { _ in }
		
		    func setTextViewAction(_ action: @escaping (String) -> Void) {
		        onUpdateText = action
		    }
		
		    func textViewDidChange(_ textView: UITextView) {
		        onupdate(textView.text)
		    }
		    ...
		}
		
		
		final class EditingIssueViewController: UIViewController {
		    ...
		    func bind(to viewModel: EditingIssueViewModelProtocol) {
		        ...
		        textViewDelegate.setTextViewAction(viewModel.didChangeContentText(_:))
		        ...
		    }
		}
		```
