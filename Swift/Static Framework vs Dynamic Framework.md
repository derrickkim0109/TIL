# TIL
> 2023-04-24
>
>  내용: Tuist에 대한 공부 중 Static Framework와 Dynamic Framework가 궁금하여 수진님의 블로그를 보게 되었다.
> 
> [참고]("https://sujinnaljin.medium.com/ios-%EB%B9%8C%EB%93%9C-%EA%B2%B0%EA%B3%BC%EB%A1%9C-%EB%B3%B4%EB%8A%94-static-framework-%EC%99%80-dynamic-framework-8568c5840e59")

## Frmework 생성 및 사용

Framework 생성 -> MyFramework

- New Project에서 Framework를 만든다.

**Example**
```swift
public struct Calculator {
	public func add(_ lhs: Int, _ rhs: Int) -> Int {
		return lhs + rhs
	}
	
	public init() {
	
	}
}
```
그리고 미리 생성 해둔 내 프로젝트에서 위에 생성해둔 Framework를 사용하기 위해 추가한다.

- General의 Frameworks, Libraries, and Embedded Content쪽에서도 `+`버튼을 통해 위의 MyFramework를 추가한다.

## Static Framework

> 기본적으로 Framework를 만들면 Mach-O Type이 Dynamic Library로 되어 있다.


**MyFramework를 Static으로 설정을 변경했을 때

> MyApp executable에 Static Framework가 포함된다. 

![](https://i.imgur.com/NGPeExL.png)

- 이렇게 포함되어 있는 경우 Embed 옵션을 `Do not Embed`로 표기하면 중복되는 Framework파일이 제거 된다.
- 이미 MyApp executable에 Static Framework가 포함되어 있다.


## Dynamic Framework

- Framework의 Mach-O Type을 Dynamic Library로 변경할 경우 
    - Embed 옵션을 Embed & Sign으로 변경해야 한다.


## Static Framework vs Dynamic Framework vs Static Library vs App

Static Framework (정적 프레임워크): 빌드 시점에 컴파일되어 앱의 실행 파일에 포함되는 형태의 프레임워크입니다. 앱과 함께 배포되며, 앱이 실행될 때 앱의 메모리에 로드됩니다. 다른 앱에서 재사용될 수 없고, 앱마다 별도로 메모리에 로드되므로 앱의 용량이 커질 수 있습니다.

Dynamic Framework (동적 프레임워크): 런타임에 동적으로 로드되어 앱의 메모리에 로드되는 형태의 프레임워크입니다. 앱과 함께 배포되며, 앱이 실행될 때 필요한 시점에 메모리에 로드되어 사용됩니다. 다른 앱에서 재사용될 수 있고, 앱마다 별도로 메모리에 로드되지 않기 때문에 앱의 용량이 작을 수 있습니다.

Static Library (정적 라이브러리): 빌드 시점에 컴파일되어 앱의 실행 파일에 포함되는 형태의 라이브러리입니다. 앱과 함께 배포되며, 앱이 실행될 때 앱의 메모리에 로드됩니다. 다른 앱에서 재사용될 수 없고, 앱마다 별도로 메모리에 로드되므로 앱의 용량이 커질 수 있습니다.

App: Xcode에서 개발한 앱의 최종 실행 파일입니다. 앱은 사용자가 실행하는 형태로 배포되며, 정적/동적 프레임워크 또는 라이브러리를 포함하여 앱의 동작을 구현합니다.

요약하면, Static Framework, Dynamic Framework, Static Library는 앱 개발 시에 재사용 가능한 코드를 패키징하여 제공하는 형태의 라이브러리 또는 프레임워크이며, 각각의 특성과 차이점이 있습니다. App은 Xcode에서 개발한 앱의 최종 실행 파일로, 앱의 동작을 구현하고 사용자에게 제공하는 형태입니다.




