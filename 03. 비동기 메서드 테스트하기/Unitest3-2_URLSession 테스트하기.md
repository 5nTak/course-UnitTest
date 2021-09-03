# 비동기 메서드 테스트하기

## 2. 비동기 메서드 테스트하기
이번에는 completionHandler를 포함하는 `makeRandomValue(completionHandler:)`와 `reset(completionHandler:)`를 테스트해보도록 하겠습니다.

<br>

### ✐ makeRandomValue(completionHandler:)
```swift
func makeRandomValue(completionHandler: @escaping () -> Void) {
    let urlString = "http://www.randomnumberapi.com/api/v1.0/random?min=0&max=30&count=1"
    guard let url = URL(string: urlString) else {
        return
    }
        
    let task = urlSession.dataTask(with: url) { data, response, error in
        guard let response = response as? HTTPURLResponse, (200...399).contains(response.statusCode) else {
            return
        }
            
        guard let data = data, error == nil else {
            return
        }
            
        do {
            guard let newValue = try JSONDecoder().decode([Int].self, from: data).first else {
                return
            }

            self.randomValue = newValue
            completionHandler()
        } catch {
            return
        }
    }
        
    task.resume()
}
```

`makeRandomValue(completionHandler:)`은 URLSession을 통해 랜덤 값을 받아와서 randomValue에 할당해 주는 메서드입니다. 그럼 이 메서드를 호출하고, `sut.randomValue`와 비교해보는 메서드를 작성하면 되겠죠?! 저희가 해 오던 대로 한 번 테스트를 작성해봅시다.

```swift
func test_makeRandomValue호출시_randomValue를_0에서30까지숫자로설정해주는지() {
    // given
    sut.randomValue = 50 // 기본값이 0~30에 포함되면 무조건 테스트에 통과하므로 범위에서 벗어난 값을 할당
        
    // when
    sut.makeRandomValue {
        // then
        XCTAssertGreaterThanOrEqual(self.sut.randomValue, 0)
        XCTAssertLessThanOrEqual(self.sut.randomValue, 30)
    }
}
```
그리고 테스트를 실행해보면..?

![5](https://user-images.githubusercontent.com/73867548/131937969-36754666-e346-4e19-a5b6-e7c8bdc426a8.jpg)   

테스트가 통과하고 있군요! <br>

completionHanler가 있더라도 그냥 테스트를 하면 되는군요..!!
가 아니라 사실 **이 테스트에는 제대로 동작하고 있지 않습니다.** 아래와 같이 XCAssert 메서드에 break point를 설정하여 확인해보면

![6](https://user-images.githubusercontent.com/73867548/131938361-851963b5-51b2-4eed-a553-bdc6e5e26421.jpg)

break point에서 멈추지 않고 그대로 테스트에 통과하는 것을 확인할 수 있습니다. 즉, XCTAssert 메서드가 호출조차 되지 않고 테스트가 끝나버린 상황입니다! 그 이유는 `makeRandomValue(completionHandler:)`가 `비동기적으로` 작업을 처리하는 메서드이기 때문입니다.

<br>

### ✐ 비동기 메서드 테스트하기
그렇다면 `비동기 메서드`는 어떻게 테스트해볼 수 있을까요? 파생된 스레드에서의 작업을 기다리게 하는 방법은 없을까요? 이는 `expectation(description:)`, `fulfill()`, `wait(for:timeout:)`라는 세 가지의 테스트 메서드로 해결해 줄 수 있습니다. 코드를 먼저 보면 이해가 빠를 것 같습니다!

```swift
func test_makeRandomValue호출시_randomValue를_잘설정해주는지() {
    // given
    let promise = expectation(description: "It makes random value") // expectation
    sut.randomValue = 50 // 기본값이 0~30에 포함되면 무조건 테스트에 통과하므로 범위에서 벗어난 값을 할당
        
    // when
    sut.makeRandomValue {
        // then
        XCTAssertGreaterThanOrEqual(self.sut.randomValue, 0)
        XCTAssertLessThanOrEqual(self.sut.randomValue, 30)
        promise.fulfill() // fulfill
    }
        
    wait(for: [promise], timeout: 10) // wait
}
```

- `expectation(description:)`: 어떤 것이 수행되어야 하는지를 description으로 정해줍니다.
- `fulfill()`: 정의해둔 expectation이 충족되는 시점에 호출하여 동작을 수행했음을 알립니다.
- `wait(for:timeout:)`: expectation을 배열로 담아 전달하여 배열 속의 expectation이 모두 fulfill 될 때까지 기다립니다. timeout을 설정하여 시간을 제한할 수 있습니다.

이렇게 작성하면 비동기 작업을 기다리게 되어, 비동기 메서드를 테스트할 수 있습니다. 여러 개의 비동기 작업을 기다려야 한다면 여러 개의 expectation을 만들어서 작업을 기다려줄 수도 있습니다. 

<br>

### ✐ reset(completionHandler:)
그럼 `reset(completionHandler:)`에 대한 테스트도 스스로 작성해볼까요? 예시 코드를 보기 전에 스스로 해봅시다!

<br>

#### 👀 예시 코드
예시 코드는 예시일 뿐, 정답이 아닙니다.

```swift
func test_reset호출시_tryCount가0이되는지() {
    // given
    let promise = expectation(description: "It makes tryCount zero")
    sut.tryCount = 4 // 기본값인 0으로 테스트를 진행하면 무조건 테스트에 통과하므로 0이 아닌 값을 할당
        
    // when
    sut.reset {
        XCTAssertEqual(self.sut.tryCount, 0)
        promise.fulfill()
    }
        
    wait(for: [promise], timeout: 10)
}
```


<br>
<br>


> ### ✐ 번외 - URLSession에 대한 테스트
> 예제 프로젝트처럼 URLSession을 이용하여 네트워크 통신을 하는 경우에는 네트워크 통신이 잘 이루어지는지에 대해서도 테스트 코드를 작성해볼 수 있습니다. 실제로 URLSession에 대한 테스트를 예제로 다루는 경우도 꽤 많이 보았던 것 같습니다. URLSession에 대한 직접적인 테스트가 필요한지는 상황마다, 각자의 기준마다 다를 수 있으니 URLSession에 대한 테스트를 작성해보고 싶으시다면 작성해보셔도 좋을 것 같습니다 :) <br>
> 결국 비동기 메서드에 대한 테스트이니 위의 내용을 학습하셨다면 어려움 없이 작성해보실 수 있을 거라 생각합니다! 예제 파일에 `URLSessionTests`라는 테스트 파일에 예시 코드를 작성해두었습니다. (예시 코드는 예시일 뿐, 정답이 아닙니다.)

<br>