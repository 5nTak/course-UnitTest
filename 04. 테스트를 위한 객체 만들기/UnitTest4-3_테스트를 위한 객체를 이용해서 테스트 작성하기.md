# 테스트를 위한 객체 만들기 (네트워크 연결 없이 동작하는 테스트)

## 3. 테스트를 위한 객체를 이용해서 테스트 작성하기
다시 본론으로 돌아와 생각해 봅시다. Test Double이 뭔지도 알겠고, 의존성 주입이 무엇인지도 알겠는데 어떻게 하면 URLSession을 네트워크가 없는 환경에서 동작하게 해줄 수 있을까요? 코드를 직접 작성해보기 전에, 어떻게 코드를 작성하게 될지 생각해 볼까요? 아래의 그림은 실제 앱의 코드에서 URLSession이 동작하는 순서와 방식을 간단하게 표현한 것입니다.

![1-1](https://user-images.githubusercontent.com/73867548/131283192-3dc8cd12-2f74-4736-9ef6-5093baa5515a.jpg)

URLSession은 URLSessionDataTask를 만들어서 네트워킹을 통해 얻고자 하는 값을 얻어와서 completionHandler를 실행해 주고 있습니다. 여기서 Test Double을 어떻게 만들어서 네트워킹 없이 completionHandler를 실행시킬 수 있을까요? 

![2](https://user-images.githubusercontent.com/73867548/131263741-09e6fd13-b39b-4cfe-a737-31ff990aec06.jpg)

- Networking으로 받아오는 대신 직접 Data를 만들어서 CompletionHandler까지 전달하기
- 기존의 URLSession과 URLSessionDataTask 자리에 DummyData를 전달할 수 있는 Stub 객체 만들어서 바꿔치기

Dummy와 Stub 등의 Test Double을 활용하여 미리 request에 대한 답을 설정해두고 이를 전달하는 식으로 테스트를 진행해볼 수 있습니다. 이렇게 작성할 수 있다면 모든 상황을 통제할 수 있으니 어떤 네트워크 환경이어도 실행이 가능한 테스트가 될 것입니다.

<br>

### ✐ 작성된 Test Double 살펴보기

![3](https://user-images.githubusercontent.com/73867548/131284037-6828fa8d-d513-4c50-bcb8-de74e43ae12a.jpg)

그럼 이제 미리 만들어둔 Test Double이라는 폴더 안에 있는 파일들을 열어봅시다. 먼저 `URLSessionProtocol.swift`을 열어보면 Stub과 Dummy 등 Test Double들이 미리 작성되어 있는 것을 확인할 수 있습니다. URLSessionProtocol은 채택하는 실제 URLSession 대신 사용할 테스트를 위한 URLSession 객체를 만들어 주기 위한 프로토콜입니다. 작성된 코드를 살펴봅시다!

```swift
typealias DataTaskCompletionHandler = (Data?, URLResponse?, Error?) -> Void

protocol URLSessionProtocol {
    func dataTask(with url: URL,  completionHandler: @escaping DataTaskCompletionHandler) -> URLSessionDataTask
}

extension URLSession: URLSessionProtocol { }
```

먼저 **의존성 주입을 위해** `URLSessionProtocol`을 정의하고 extension을 이용하여 URLSession에 채택시켜줍니다. 이때 URLSession에는 이미 dataTask 메서드가 구현되어 있기 때문에 따로 구현해야 할 내용은 없습니다.  

<br>

다음으로 `StubURLSession.swift`라는 파일을 열어볼까요? 여기에는 3가지 타입이 정의되어 있습니다.

```swift
import Foundation

struct DummyData {
    let data: Data?
    let response: URLResponse?
    let error: Error?
    var completionHandler: DataTaskCompletionHandler? = nil
    
    func completion() {
        completionHandler?(data, response, error)
    }
}

class StubURLSession: URLSessionProtocol {
    var dummyData: DummyData?
    
    init(dummy: DummyData) {
        self.dummyData = dummy
    }

    func dataTask(with url: URL, completionHandler: @escaping DataTaskCompletionHandler) -> URLSessionDataTask {
        return StubURLSessionDataTask(dummy: dummyData, completionHandler: completionHandler)
    }
}

class StubURLSessionDataTask: URLSessionDataTask {
    var dummyData: DummyData?
    
    init(dummy: DummyData?, completionHandler: DataTaskCompletionHandler?) {
        self.dummyData = dummy
        self.dummyData?.completionHandler = completionHandler
    }
    
    override func resume() {
        dummyData?.completion()
    }
}
```

- `DummyData`: URLSession이 네트워킹을 통해 받아와서 처리하는 data, response, error를 직접 만들어서 담아주기 위한 구조체입니다

- `StubURLSession`: 테스트 중에 URLSession을 대체할 객체입니다. URLSessionProtocol을 채택하여 dataTask 메서드를 통해 DummyData를 URLSessionDataTask에 초기화와 동시에 전달할 수 있습니다.

- `StubURLSessionDataTask`: URLSessionDataTask를 상속받은 객체로 테스트 중에 URLSessionDataTask를 대체할 객체입니다. resume을 통해 completionHandler 수행합니다. 이때 dummyData의 completion을 호출하여 dummyData의 프로퍼티에도 접근할 수 있습니다.

이렇게 테스트 더블은 protocol과 상속을 이용해서 만들어주는 경우가 많습니다. 
혹시 아직도 조금 어렵게 느껴진다면 아래의 테스트 코드까지 보시고 천천히 이해해 보는 것도 좋을 것 같아요 :)

<br>

### ✐ 의존성 주입을 활용하여 테스트 코드 작성하기

```swift
import XCTest
@testable import UpDownGame

class StubURLSessionTests: XCTestCase {
    var sut: UpDownGame!

    override func setUpWithError() throws {
        try super.setUpWithError()
        sut = UpDownGame()
    }

    override func tearDownWithError() throws {
        try super.tearDownWithError()
        sut = nil
    }
    
    // MARK: - reset
    func test_randomValue로_3이라는값을_받을때() {
        // given
        let promise = expectation(description: "")
        let url = URL(string: "http://www.randomnumberapi.com/api/v1.0/random?min=1&max=30&count=1")!
        let data = "[3]".data(using: .utf8)
        let response = HTTPURLResponse(url: url, statusCode: 200, httpVersion: nil, headerFields: nil)
        let dummy = DummyData(data: data, response: response, error: nil) // 1
        let stubUrlSession = StubURLSession(dummy: dummy) // 2
        
        sut.urlSession = stubUrlSession // 3
        
        // when
        sut.reset {
            // then
            XCTAssertEqual(self.sut.randomValue, 3)
            promise.fulfill()
        }
        
        wait(for: [promise], timeout: 10)
    }
    ...
}
```

이 테스트 코드는 아래와 같이 진행되고 있습니다. 

1. 위 줄에서 만든 data와 response로 `DummyData`를 초기화한다.
2. UpDownGame에 의존성 주입시켜줄 `StubURLSession`을 초기화한다. 이때 dummy를 건네준다.
3. UpDownGame의 urlSession에 `DummyData`를 가지고 있는 `StubURLSession`을 주입한다.

그러면 내부에서 `StubURLSession`의 `dataTask` 메서드가 호출되면서 우리가 만들어둔 DummyData를 바탕으로 파싱이 이루어지겠지요?

![2](https://user-images.githubusercontent.com/73867548/131263741-09e6fd13-b39b-4cfe-a737-31ff990aec06.jpg)   

위에서의 이 그림대로 동작을 하고 있는 것입니다.

<br>

`테스트를 위한 객체`는 꼭 네트워크 통신을 해결하기 위한 객체는 아닙니다. 다양한 방법으로 사용될 수 있습니다. 상황에 맞게 유연하게 테스트 더블을 이용해보도록 합시다 :)