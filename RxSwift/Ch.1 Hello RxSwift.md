# Ch.1 Hello RxSwift

> 2022-04-23

> [참고](https://github.com/fimuxd/RxSwift/blob/master/Lectures/01_HelloRxSwift/Ch.1%20Hello%20RxSwift.md)

> 따라 쓰고 외운다.

## RxSwift란?

> RxSwift is a library for composing asynchronous and event-based code by using observable sequences and functional style operators, allowing for parameterized execution via schedulers.

- RxSwift는 `본질적`으로 코드가 `새로운 데이터에 반응`하고 `순차적으로 분리된`방식으로 처리함으로써 비동기적인 프로그램 개발을 간소화하는 라이브러리이다.


## Cocoa and UIKit Asynchronous APIs

Apple은 iOS SDK 내에서 비동기적인 코드를 작성할 수 있다록 다양한 API를 제공한다. 

**목록**

- Notification Center
- The Delegate Pattern
- Grand Central Dispatch(GCD)
- Closures

일반적으로 대부분의 클래스들은 비동기적으로 작업을 수행하고 모든 UI 구성요소들은 본질적으로 비동기적이라고 한다. 따라서 내가 어떤 앱 코드를 작성할때 매번 어떤 순서로 작동하는지에 대해 가정하는 것은 불가능하다. 

앱 내의 코드 

- 사용자 입력
- 네트워크 활동
- 기타 OS 이벤트 

위의 다양한 요인으로 인해 다른 순서로 실행될 수 있다.

**Important**
> 즉, Apple SDK내의 API를 통하여 비동기적 코드를 부분적으로 나누어 쓰는 것은 어렵다.(추적 불가능)

## 비동기 프로그래밍 용어

**State(Mutable State)**

State는 객체 혹은 시스템의 현재 상태, 데이터를 나타내는 개념이다. 예를 들어, 객체의 속성이나 변수에 저장된 값들이 해당 객체의 `State`이다. 이는 객체나 시스템의 현재 상태를 나타내는 정적인 데이터를 의미한다.

Status는 주로 어떤 작업이나 프로세스의 현재 진행 상황이나 상태를 나타내는 개념이다. 예를들어, 작업의 진행 상황에 따라 `status`가 변경되며, 작업이 완료되면 `status`는 완료 상태로 변경된다. 이는 동적인 상태를 나타내는 것이며, 시간에 따라 변화할 수 있다.

사용 범위: 

`State`는 객체나 시스템의 전반적인 데이터나 상태를 나타내는 데 사용됩니다. 예를 들어, 객체의 속성이나 클래스의 멤버 변수가 해당 객체의 `state`를 나타낼 수 있습니다.

`Status`는 보통 좀 더 특정한 상황이나 작업에 대한 상태를 나타내는 데 사용됩니다. 예를 들어, 파일의 업로드 상태, 네트워크 연결 상태, 작업의 진행 상황 등이 "status"로 표현될 수 있습니다.

> 앱의 상태(State)를 관리하는 것(예: 여러가지 비동기 구성요소를 공유할 때)은 RxSwift를 통해 배울 수 있는 중요한 포인트 중 하나이다.


**명령형 프로그래밍**
프로그래밍의 상태와 상태를 변경시키는 구문에서 연산을 설명하는 프로그래밍 패러다임의 일종이다.
어떻게 수행되어야 하는지를 명시하는 방식이다.

- 앱에게 정확히 `언제`,`무엇을`하라고 말하고 싶을 떄 명령형 코드를 쓸 수 있다.
- 컴퓨터가 이해하고 있는 방식과도 비슷하다. 예를 들어, 모든 CPU는 간단한 명령어로 이루어진 긴 `Sequence`를 따른다.
- 

위에서 말한 비동기적 코드가 실행될 때 순서를 보장하기 어렵다는 점에 대해 예를 들어본다.

예를 들어 iOS ViewController의 viewDidAppear(_:)를 보자.

```swift 
	 override func viewDidAppear(_ animated: Bool) {
 		super.viewDidAppear(animated)
 	
	 	setupUI()
 		connectUIControls()
	 	createDataSource()
 		listenForChanges()
	 }
```

- 각 메서드가 무슨 동작을 하는지 알 수 없다.
- 각 메서드가 순서대로 실행되는지 보증할 수 없다.
- 메서드의 순서가 바뀌게 되면 앱의 동작이 바뀔 수 있다.


**선언형 프로그래밍**

무엇을 원하는지 명시하고, 어떻게 하는지 명시하지 않는 방식으로 프로그래밍하는 패러다임이다. 원하는 결과를 선언하고, 시스템이 이를 어떻게 달성할지를 추상화된 수준에서 처리한다.

- 함수형 프로그래밍
- SQL
- 선언적 UI 프레임워크

코드의 가독성, 재사용성, 테스트 용이성 등을 개선할 수 있고, 프로그래머가 어떻게 구현할지에 대해 고민하는 대신 무엇을 원하는지에 집중할 수 있게 해준다.

-선언형 코드를 통해 동작을 정의할 수 있다. RxSwift는 관련 이벤트가 있을 때 마다 이러한 동작을 실행하고 작업할 수 있는 불변의 고유한 데이터 입력을 제공한다. 이렇게 하면 변경 불가능한 데이터로 작업하게 되고, 순차적이고 결과론적인 방식으로 코드를 실행할 수 있다.

**부(수)작용**

부수작용이란 현재 Scope 외의 상태에서 일어나는 모든 변화를 뜻한다.
예를들어, 상기 코드에서 `contentUIControls`라는 메서드는 아마 어떤 UI구성요소를 제어하기 위한 `Event Handler`일 것이다. 이것이 view의 state(상태)를 변경하게 된다면 부수작용을 만들게 된다.

- 스크린상 `Label`의 `text`를 추가하거나 변경하는 것 -> 디스크에 저장된 데이터를 수정한다는 것 
	- 부수작용을 발생시킨다는 것이다.

부수작용은 그 자체로 나쁜 것이 아니라 컨트롤이 가능하냐는 것이 문제이다.
각각의 코드에 대해서, 해당 코드가 어떤 부수작용을 발생시킬 수 있는 코드인지, 과정을 단순히 나열하는 것인지, 명확한 결괏값만을 발생시키는 것인지 인지하고 있는 것이 중요하다.

- RxSwift는 이러한 문제들을 추적할 수 있게 해준다.

**Reactive Systems**

반응형 시스템이란 의미는 상당히 추상적이다. iOS앱에서는 주로 아래와 같은 특성을 다룬다.

- 반응(Responsive): 항상 UI를 최신 상태로 업데이트 하며, 가장 최근의 앱 상태를 표시한다.
- 복원력(Resilient): 각각의 행동들은 독립적으로 정의되며, 에러 복구를 위해 유연하게 제공된다.
- 탄력성(Elastic): 코드는 다양한 작업의 부하를 처리하며, 종종 `Lazy Full` 기반 데이터 수집, 이벤트 제한 및 리소스 공유와 같은 기능을 구현한다.
- 메시지 전달(Message Driven):구성요소는 메시지 기반 통신을 사용하여 재사용 및 고유한 기능을 개선하고, 라이프 사이클과 클래스 구현을 분리한다.

## RxSwift 기초

- Rx Code는 Building Block(구성요소), Observables(생산자), Operator(연산자), Schedulers(스케줄러)를 가지고 있다. 

### 1. Observables

Observable<T> 클래스는 Rx 코드의 기반이다. 

- T 형태의 데이터를 Snapshot을 전달할 수 있는 일련의 이벤트를 비동기적으로 생성하는 기능을 가지고 있다.
	- 즉, 다른 클래스에서 만든 값을 시간에 따라 읽을 수 있다.
- 하나 이상의 Observers(관찰자)가 실시간으로 어떤 이벤트에 반응하고 앱 UI를 업데이트 하거나 생성하는지를 처리하고 활용할 수 있게 한다.

ObservableType 프로토콜(Observable<T>가 준수함)이 방출하는 세 가지 타입의 이벤트

- `next`: 최신 / 다음 데이터를 전달하는 이벤트이다.
- `completed`:  데이터 전달 후, 성공적으로 이벤트들을 종료 시키는 이벤트이다. 즉, Observable이 성공적으로 자신의 생명주기를 완료했다는 것이고 추가적으로 이벤트를 생성하지 않는다는 것이다.
- `error`: Observable이 에러를 발생시키고, 추가적으로 이벤트를 생성하지 않을 것을 의미한다. 에러와 함께 완전히 종료된다.

![](https://i.imgur.com/EM6zmDw.png)


**Finite Observable Sequences**
    
어떤 Observable sequence는 0, 1 또는 다른 값을 방출한 뒤, 성공적으로 또는 에러를 통해 종료된다.

iOS 앱에서, 인터넷을 통해 파일을 다운로드 하는 코드를 생각해보자.

1. 다운로드를 시작하고, 들어오는 데이터를 관찰한다.
2. 계속해서 파일 데이터를 받는다.
3. 네트워크 연결이 끊어진다면, 다운로드는 멈출 것이고 연결은 에러와 함께 일시정지 될 것이다.
4. 또는, 성공적으로 모든 파일 데이터를 다운로드 할 수 있을 것이다.
이러한 흐름은 앞에서 서술한 Observable의 생명 주기와 정확히 일치한다. RxSwift 코드로 표현하면 다음과 같다.

```swift 
    API.download(file: "http://www...")
 	.subscribe(onNext: { data in
 		... append data to temporary file
 	},
 	onError: { error in 
 		... display error to user
 	},
 	onCompleted: {
 		... use downloaded file
 	})
 
```

- API.download(file:)은 네트워크를 통해 들어오는 Data값을 방출하는 Observable<Data> 인스턴스를 리턴할 것이다.

- onNext 클로저를 통해 next 이벤트를 받을 수 있다. 예제에서는 받은 데이터를 디스크의 temporary file에 저장하게 될 것이다.

- onError 클로저를 통해 error 이벤트를 받을 수 있다. alert 메시지 같은 action을 취할 수 있을 것이다.
최종적으로 onCompleted 클로저를 통해 completed 이벤트를 받을 수 있으며, 이를 통해 새로운 viewController를 push하여 다운로드 받은 파일을 표시하는 등의 엑션을 취할 수 있을 것이다.
    
    
**Infinite Observable Sequences**
    
자연적으로 또는 강제적으로 종료되어야 하는 파일 다운로드 같은 활동과 달리, 단순히 무한한 sequence가 있다. 보통 UI 이벤트는 무한하게 관찰가능한 sequence이다.
    
예를 들어, 기기의 가로/세로 모드에 따라 반응해야하는 코드를 생각해보자.


1. UIDeviceOrientationDidChange observer를 추가한다.

2. 방향 전환을 관리할 수 있는 callback method를 제공해야 한다. UIDevice의 현재 방향값을 확인 한 뒤, 이 값에 따라 화면이 표시될 수 있도록 해야한다.

3. 방향전환이 가능한 디바이스가 존재하는 한, 이러한 연속적인 방향 전환은 자연스럽게 끝날 수 없다.

4. 결국 이러한 시퀀스는 사실상 무한하기 때문에, 항상 최초값을 가지고 있어야 한다.

4. 사용자가 디바이스를 절대 회전하지 않는다고 해서 이벤트가 종료된 것도 아니다. 단지 이벤트가 발생한 적이 없을 뿐.

RxSwift 코드로 표현하면 다음과 같다.

```swift 
     UIDevice.rx.orientation
 	.subscribe(onNext: { current in
 		switch current {
 			case .landscape:
 				... re-arrange UI for landscape
 			case .portrait:
 				... re-arrange UI for portrait
 		}
 	})

```
UIDevice.rx.orientation은 Observable<Orientation>을 통해 만든 가상의 코드다.

- 이를 통해 현재 Orientation(방향)을 받을 수 있고, 받은 값을 앱의 UI에 업데이트 할 수 있다.

- 해당 Observable에서는 절대 발생하지 않을 이벤트이기 때문에 onError나 onCompleted parameter는 건너뛸 수 있다.

    
    
    
### 2. Operators
    
observableType과 Observable 클래스의 구현은 보다 복잡한 논리를 구현하기 위해 함께 구성되는 비동기 작업을 추상화하는 많은 메소드가 포함되어 있다. 이러한 메소드는 매우 독립적이고 구성가능하므로 보편적으로 Operators(연산자) 라고 불린다.

이러한 Operator(연산자) 들은 주로 비동기 입력을 받아 부수작용 없이 출력만 생성하므로 퍼즐 조각과 같이 쉽게 결합할 수 있다.

예를 들어 (5 + 6) * 10 - 2 라는 수식을 생각해보자

- *, (), +, - 같은 연산자를 통해 데이터에 적용하고 결과를 가져와서 해결될 때까지 표현식을 계속 처리하게 된다.

- 비슷한 방식으로 표현식이 최종값으로 도출 될 때까지, Observable이 방출한 값에 Rx 연산자를 적용하여 부수작용을 만들 수 있다.

```swift 
 UIDevice.rx.orientation
 	.filter { value in
 		return value != .landscape
 	}
 	.map { _ in
 		return "Portrait is the best!"
 	}
 	.subscribe(onNext: { string in
 		showAlert(text: string)
 	})
    
```
    
UIDevice.rx.orientation이 .landscape 또는 .portrait 값을 생성할 때 마다, Rx는 각각의 연산자를 데이터의 형태로 방출한다.
    
- 먼저 filter 는 .landscape 가 아닌 값만을 내놓는다. 만약 디바이스가 landscape 모드라면 나머지 코드는 진행되지 않을 것이다. 왜냐하면 filter가 해당 이벤트의 실행을 막을 것이기 때문이다.

- 만약 .portrait 값이 들어온다면, map 연산자는 해당 방향값을 택할 것이며 이것을 String 출력으로 변환할 것이다. ("Portrait is the best!")

- 마지막으로, subscribe를 통해 결과로 next 이벤트를 구현하게 된다. 이번에는 String 값을 전달하고, 해당 텍스트로 alert을 화면에 표시하는 method를 호출한다.


연산자들은 언제나 입력된 데이터를 통해 결과값을 출력하므로, 단일 연산자가 독자적으로 할 수 있는 것보다 쉽게 연결 가능하며 훨씬 많은 것을 달성할 수 있다.
    
    
### 3. Schedulers

Rx에서의 스케줄러는 DispatchQueue와 동일하지만 훨씬 강력하고 쓰기 쉽다.
    
- RxSwift에는 여러가지의 스케줄러가 이미 정의되어 있으며, 99%의 상황에서 사용가능하므로 아마 개발자가 자신만의 스케줄러를 생성할 일은 없을 것이다.

- 이 책의 초기 반 정도에서 다룰 대부분의 예제는 아주 간단하고 일반적인 상황으로, 보통 데이터를 관찰하고 UI를 업데이트 하는 것이 대부분이다. 따라서 기초를 완전히 닦기 전까지 스케줄러를 공부할 필요는 없다.

기존에는 GCD를 통해서 일련의 코드를 작성했다면 스케줄러를 통한 RxSwift는 다음과 같이 돌아간다.
    
![](https://i.imgur.com/YgxILRf.png)

각 색깔로 표시된 일들은 다음과 같이 각각 스케줄(1, 2, 3...)된다.

- network subscription(파랑)은 (1)로 표시된 Custom NSOperation Scheduler에서 구동된다.

- 여기서 출력된 데이터는 다음 블록인 Background Concurrent Scheduler의 (2)로 가게 된다.

- 최종적으로, 네트워크 코드의 마지막 (3)은 Main Thread Serial Scheduler로 가서 UI를 새로운 데이터로 업데이트 하게 된다.

    
## App Architecture

RxSwift는 기존의 앱 아키텍처에 영향을 주지 않는다. 주로 이벤트나 비동기 데이터 시퀀스 등을 주로 처리하기 때문이다.

따라서 Apple 문서에서 언급된 MVC 아키텍처에 Rx를 도입할 수 있다. 물론 MVP, MVVM 같은 아키텍처를 선호한다면 가능하다.

Reactive 앱을 만들기 위해 처음부터 프로젝트를 시작할 필요도 없다. 기존 프로젝트를 부분적으로 리팩토링하거나 단순히 앱에 새로운 기능을 추가할 때도 사용가능하다.

Microsoft의 MVVM 아키텍쳐는 데이터 바인딩을 제공하는 플랫폼에서 이벤트 기반 소프트웨어용으로 개발되었기 때문에, 당연히 RxSwift와 MVVM는 같이 쓸 때 아주 멋지게 작동한다.

ViewModel을 사용하면 Observable<T> 속성을 노출할 수 있으며 ViewController의 UIKit에 직접 바인딩이 가능하다.
이렇게 하면 모델 데이터를 UI에 바인딩하고 표현하고 코드를 작성하는 것이 매우 간단해진다.
    
![](https://i.imgur.com/NTQVCvR.png)

## RxCocoa
    
RxCocoa는 RxSwift의 동반 라이브러리로, UIKit과 Cocoa 프레임워크 기반 개발을 지원하는 모든 클래스를 보유하고 있다. RxSwift는 일반적인 Rx API라서, Cocoa나 특정 UIKit 클래스에 대한 아무런 정보가 없다.

예를들어, RxCocoa를 이용하여 UISwitch의 상태를 확인하는 것은 다음과 같이 매우 쉽다.

```swift 
    toggleSwitch.rx.isOn
 	.subscribe(onNext: { enabled in
 		print( enabled ? "It's ON" : "it's OFF")
 	})
 
```

RxCocoa는 rx.isOn과 같은 프로퍼티를 UISwitch 클래스에 추가해주며, 이를 통해 이벤트 시퀀스를 확인할 수 있다.

RxCocoa는 UITextField, URLSession, UIViewController 등에 rx를 추가하여 사용한다.

