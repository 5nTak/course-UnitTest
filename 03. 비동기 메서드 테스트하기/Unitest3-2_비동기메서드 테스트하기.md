# Unit Test 작성하기

## 2. 비동기 메서드 테스트하기
이번에는 completionHandler를 포함하는 `makeRandomValue(completionHandler:)`와 `reset(completionHandler:)`를 테스트해보도록 하겠습니다.














다음으로 `reset(completionHandler:)` 메서드를 테스트해봅시다! 그런데  `reset(completionHandler:)`은 내부에서 `makeRandomValue(completionHandler:)`이라는 **비동기 메서드**를 호출하고 있기 때문에 테스트가 까다로워 보입니다. 또한 `makeRandomValue(completionHandler:)` 내부에서는 **URLSession**을 이용한 네트워킹 작업을 해주고 있어서 어떻게 테스트를 작성해야할지 또 잘 모르겠습니다 🥲 (`makeRandomValue(completionHandler:)` 메서드는 private 수준의 접근제한을 가지므로 테스트를 진행할 수 없습니다.) <br>

천천히 생각해봅시다! 우리는 `reset(completionHandler:)` 메서드에서는 어떠한 테스트가 이루어져야할까요?

- tryCount가 잘 초기화가 되는지
- randomValue를 잘 설정해주는지 `->` **URLSession로 값을 제대로 받아오는지**

우리는 이와같은 테스트를 통해 `reset(completionHandler:)`의 유닛 테스트를 작성해볼 수 있을 것 같습니다. 그럼 먼저 URLSession으로 randomValue를 잘 설정해주는지에 대해서 테스트를 해보아야겠네요! `URLSessionTests` 라는 이름의 테스트 파일을 하나 추가해봅시다. 이번에는 타겟 추가가 아닌, 기존의 타겟에서 `Command + N` 커맨드를 이용해서 `Unit Test Case Class` 파일을 생성해줍시다.

![1](https://user-images.githubusercontent.com/73867548/131252626-66c05e2b-db38-40af-8876-775eb2a7c517.jpg)

![2](https://user-images.githubusercontent.com/73867548/131252665-a046926e-938c-4ffd-9b4a-c294a7b01ee4.jpg)

<br>

그렇다면 URLSession에 대해서는 어떠한 테스트 케이스가 작성되면 좋을까요?

- URL 요청에 200번 상태코드가 돌아오는지
- URL 요청에 error가 nil인지
- URL 요청에 0부터 30에 속하는 data가 들어오는지

<br>

### -  URL 요청에 200번 상태코드가 돌아오는지
먼저 200번의 상태코드를 받아오는지부터 우리가 아는대로 테스트를 작성해봅시다!

```swift
import XCTest
@testable import UpDownGame

class URLSessionTest: XCTestCase {
    var sut: URLSession!
    let url = URL(string: "http://www.randomnumberapi.com/api/v1.0/random?min=0&max=30&count=1")!
    
    override func setUpWithError() throws {
        try super.setUpWithError()
        sut = URLSession(configuration: .default)
    }

    override func tearDownWithError() throws {
        try super.tearDownWithError()
        sut = nil
    }

    func test_URL요청상태코드200번이_돌아오는지() {
        let task = sut.dataTask(with: url) { data, response, error in
            guard let response = response as? HTTPURLResponse else {
                return
            }
            
            let statusCode = response.statusCode
            XCTAssertEqual(statusCode, 200)
        }
        
        task.resume()
    }
    ...
}
```

![3](https://user-images.githubusercontent.com/73867548/131252710-38da69b1-0983-4b1c-9440-b135716ba8dd.jpg)

이 테스트를 실행하면 어떻게 될까요? 초록불이 나오네요! 그러면 테스트는 성공적으로 잘 작성된 것일까요?  <br>

Test Succeeded가 확인되지만 사실 이 테스트는 제대로 이루어지지 않고 있습니다. 그 이유는 dataTask의 completionHandler가 **비동기적으로 처리되기 때문**입니다. 파생된 스레드의 동작을 기다리지 않고 dataTask의 resume()을 호출하는 것으로 테스트가 종료된 것입니다. 

![4](https://user-images.githubusercontent.com/73867548/131252892-4d0d740d-3481-4b99-b81e-fac43ea974d2.jpg)

BreakPoint를 찍어도 `XCTAsserEqual`은 호출되지 않은채 테스트가 종료되는 것을 확인할 수 있습니다. 그렇다면 어떻게 이 테스트를 정상적으로 수행시킬 수 있을까요? 파생된 스레드에서의 작업을 기다리게 하는 방법은 없을까요? <br>

이는 `expectation(description:)`, `fulfill()`, `wait(for:timeout:)` 세 가지의 테스트 메서드로 해결해줄 수 있습니다.

```swift
func test_URL요청상태코드200번이_돌아오는지() {
    // given
    let promise = expectation(description: "StatusCode is 200") // expectation
        
     // when
    let task = sut.dataTask(with: url) { data, response, error in
        guard let response = response as? HTTPURLResponse else {
           return
      }
            
        let statusCode = response.statusCode
        // then
        XCTAssertEqual(statusCode, 200)
        promise.fulfill() // fulfill
    }
        
    task.resume()
    wait(for: [promise], timeout: 10) // wait
}
```

- `expectation(description:)`: 어떤 것이 수행되어야하는지를 description으로 정해줍니다.
- `fulfill()`: 정의해둔 expectation이 충족되는 시점에 호출하여 동작을 수행했음을 알립니다.
- `wait(for:timeout:)`: expectation을 배열로 담아 전달하여 배열 속의 expectation이 모두 fulfill될 때까지 기다립니다. timeout을 설정하여 시간을 제한할 수 있습니다.

이렇게 작성한다면 `XCTAsserEqual`이 호출되어 원하는 테스트를 진행할 수 있게 됩니다.     
같은 방식으로 **URL 요청에 error가 nil인지, URL 요청에 0부터 30에 속하는 data가 들어오는지** 에 대해서도 유닛 테스트를 작성해볼 수 있습니다! <br>

## Do it yourself! 코드를 보기 전에 스스로 작성해봅시다.

<br>
<br>

```swift
func test_URL요청에러가_nil인지() {
    // gitven
    let promise = expectation(description: "Error is nil")
        
    // when
    let task = sut.dataTask(with: url) { data, response, error in
        // then
        XCTAssertNil(error)
        promise.fulfill()
    }
        
    task.resume()
    wait(for: [promise], timeout: 10)
}
    
func test_URL요청에_0에서30까지의randomValue가_들어오는지() {
    // given
    let promise = expectation(description: "RandomValue is in 0 to 30")
        
    // when
    let task = sut.dataTask(with: url) { data, response, error in
        guard let data = data else {
            return
        }
            
        guard let newValue = try? JSONDecoder().decode([Int].self, from: data).first else {
            return
        }
            
        // then
        XCTAssertGreaterThanOrEqual(newValue, 0)
        XCTAssertLessThanOrEqual(newValue, 30)
        promise.fulfill()
    }
        
    task.resume()
    wait(for: [promise], timeout: 10)
}
```
