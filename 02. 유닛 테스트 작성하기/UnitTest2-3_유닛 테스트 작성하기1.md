# Unit Test 작성하기

## 3. Unit Test 작성하기 - 1
이제 테스트를 위한 준비가 끝난 것 같으니 본격적으로 Unit Test를 작성해봅시다! 무엇을 테스트해야 할까요? 우리가 테스트할 `StrangeCalculator` 파일을 열어봅시다. 유닛 테스트는 메서드 단위의 테스트라고 했으니 우리는 `StrangeCalculator`의 메서드인 `addNumbers(of:)`에 대해서 테스트를 진행해볼 수 있겠네요! 

```swift
func addNumbers(of numbers: [Int]) -> Int {
    return numbers.reduce(0, +)
}
```

<br>


### ✐ @testable
먼저 테스트 함수를 작성하기 전에 아래의 `@testable import (Target)`라는 코드를 작성해 주어야 합니다.

```swift
import XCTest
@testable import UnitTestSample

class StrangeCalculatorTests: XCTestCase {
    ...
}
```

`import XCTest` 코드 아래에 `@testable import UnitTestSample`이라는 코드를 추가해 줍니다.  
`@testable`은 Unit Test에서 실제 앱 타깃에 있는 코드들에 접근하기 위한 키워드입니다. 우리가 `UnitTestSample`이라는 타깃에 있는 코드를 작성하기 위해서 필요한 코드인 것이지요. 보통 앱 코드 내부에서는 internal 수준의 접근 제한으로 타입을 만들어주는 것이 일반적입니다. 그렇기 때문에 앱 타깃의 타입들에 외부 타깃에서 접근하는 것이 불가능한데, `@testable`은 테스트하는 동안에는 다른 타깃의 코드에 접근할 수 있도록 해주는 것입니다.

<br>

### ✐ SUT (System Under Test)
```swift
import XCTest
@testable import UnitTestSample

class StrangeCalculatorTests: XCTestCase {
    var sut: StrangeCalculator! // 1
    
    override func setUpWithError() throws { // 2
        try super.setUpWithError()
        sut = StrangeCalculator()
    }

    override func tearDownWithError() throws { // 3
        try super.tearDownWithError()
        sut = nil
    }
...
}
```

그리고 위와 같은 코드를 작성해 줍니다. `StrangeCalculatorTests` 클래스의 첫 줄에는 `sut`이라는 프로퍼티를 만들어 주었는데요, sut은 **System Under Test**라는 의미입니다. 즉, test를 할 타입이라고 생각할 수 있습니다! (꼭 sut이라는 네이밍을 사용하지 않아도 상관없습니다.)    
우리는 `StrangeCalculator`라는 타입을 테스트해 주어야 하니까 위와 같이 타입 설정을 할 수 있습니다. 이때 타입이 옵셔널인 이유는 아래의 내용과 관련이 있습니다! <br>

`setUpWithError`에서는 sut을 초기화해 주고 있고, `tearDownWithError`에서는 sut에 nil을 할당해 주고 있습니다. 앞서 언급했듯이 이 메서드들은 각각 테스트 케이스가 시작되는 처음과 끝에 호출이 되면서 모든 테스트 케이스가 의도된 조건에서 수행될 수 있는 환경을 만들어줍니다. 그렇기 때문에 sut을 테스트 케이스마다 초기화를 해주어야겠지요? 그리고 이를 위해서 nil을 할당해 주어야 하기 때문에 sut의 타입은 옵셔널로 하는 것이 적절해 보입니다. <br>

**setUpWithError, tearDownWithError** 내부에서 `super`를 호출해 주는 이유는 우리가 만들어준 `StrangeCalculatorTests`가 `XCTestCase`을 상속받았고, 메서드들을 `override` 해서 사용해 주고 있기 때문입니다. 

<br> 

### ✐ 테스트 코드 작성하기
자 이제 진짜 첫 Unit Test를 작성해봅시다! 앞서 테스트는 **기대하는 값과 결괏값을 비교하는 과정**으로 이루어진다고 이야기했습니다. 그렇다면 `addNumbers(of:)` 메서드는 어떻게 동작해야 할까요? 단순하게 전달된 배열의 모든 수를 더해서 반환하면 됩니다.     
그럼 예시를 만들어봅시다. 예를 들어 **3, 7, 23**이라는 수들을 전달했을 때, **33**을 반환해 주면 되겠지요? 이에 대해서 아래와 같은 테스트 코드를 작성해볼 수 있습니다.

```swift
    func test_addNumbers호출시_2_7_23을전달했을때_33을반환하는지() {
        // given
        let input = [3, 7, 23]
        
        // when
        let result = sut.addNumbers(of: input)
        
        // then
        XCTAssertEqual(result, 33)
    }
```

어느 정도 감이 잡히시나요? 생각보다 원리는 간단합니다!     
`XCTAssertEqual`은 테스트의 결과를 확인하는 함수입니다. 해당 함수는 두 값을 비교한 결과에 따라 **테스트 통과/실패**가 결정됩니다. 

> ### 🤔 코드를 given/when/then으로 나누어 작성하는 이유는 무엇인가요?
> 이러한 흐름의 테스트 작성 방법은 BDD(Behavior Driven Development)라는 테스트 방식에서 가져온 것입니다. 어떤 상황이 주어지고(**given**), 어떤 코드를 실행하고(**when**), 테스트 결과를 확인하는(**when**) 단계로 구분하여 테스트의 흐름을 보다 쉽게 파악하기 위함입니다. <br>
> 이러한 테스트의 흐름은 사람마다, 팀마다 다릅니다. 정답은 없습니다!! 누군가는 input/output, 또 누군가는 expectation/result 이렇게 흐름을 구분하여 작성하기도 합니다. 


<br>

### ✐ Test 함수
`XCTAssertEqual`로 테스트 결과를 결정하는 경우가 많지만, 실제로 Test 함수는 XCTAssertEqual 말고도 아주아주 많이 있습니다. 예를 들어 대소 비교를 하는, nil 인지를 판별하는(XCTAssertNil), 무엇을 throw 하는지(XCTAssertThrowsError), 어떤 Bool 타입을 반환하는지(XCTAssertTrue).. 등 외에도 다양한 메서드들이 있습니다. `XCTAssertEqual`로 모든 경우를 비교해볼 수도 있겠지만 다양한 함수를 찾아보면서 사용해보면 더 좋을 것 같아요 :)



<br>

### ✐ 테스트 실행하기
이렇게 작성한 테스트는 어떻게 실행해볼 수 있을까요? 방법은 여러 가지가 있습니다!
![5](https://user-images.githubusercontent.com/73867548/131240317-665a0440-a553-4b10-8182-ba96e0872868.jpg)

먼저 네비게이터 영역의 **테스트 네비게이터**에서 테스트 함수를 실행시키는 방법이 있고, `거터(gutter)`라는 코드 옆(line number가 표시되는 곳)의 다이아몬드 버튼을 누르는 방법이 있습니다. 단축키로는 `Command + U`로 실행해볼 수 있는데 단축키로 실행할 경우 모든 테스트 클래스가 실행됩니다. <br>

저는 거터를 눌러서 테스트를 실행해보도록 하겠습니다.

![6](https://user-images.githubusercontent.com/73867548/131240401-a32d0bdd-7fba-4b96-8d8d-15b130b6f515.jpg)
그러면 이렇게 Build에 성공하고, `Test Succeeded`라는 문구와 함께 Test에 성공하면서 거터가 초록색 다이아몬드로 바뀌게 됩니다. 테스트에 성공했다는 의미입니다! 첫 유닛 테스트에 성공해보았군요!  <br>

그런데 만약 테스트에서 실패하면 어떻게 될까요? 일부러 실패하도록 코드를 바꾸어서 실행해보겠습니다.

![7](https://user-images.githubusercontent.com/73867548/131240435-eba6d261-c41c-443d-b4ce-3a38007eee3a.jpg)
테스트에 실패하게 되면 이렇게 거터가 빨간색으로 채워지게 됩니다. 이럴 경우에는 앱의 코드에 리팩토링이 필요하거나, 테스트의 예상과 결과에 대해 돌아볼 필요가 있겠네요.

<br>

### ✐ 추가 테스트 작성하기
`addNumbers(of:)` 메서드의 첫 테스트는 잘 통과를 했습니다! 그렇다면 이 메서드는 이제 문제가 없는 메서드일까요? <br>

물론 메서드의 동작이 아주 단순하기 때문에 문제가 없음을 쉽게 알 수는 있습니다. 하지만 테스트 코드를 작성할 때에는 예외 케이스는 없는지, 특수하게 처리되어야 하는 케이스는 없는지 등을 꼼꼼히 살펴볼 필요가 있습니다. 예를 들면,

- 음수가 들어가도 잘 동작하는지
- 빈 배열을 전달해도 예상처럼 동작하는지
- 아주 많은 수 혹은 큰 수가 들어가도 잘 동작하는지   

등에 대해서는 테스트해 볼 필요가 있습니다. 위에서 작성했던 것처럼 직접 작성해보는 것도 좋을 것 같네요! 물론 위에서 제시해드린 예시는 예시일 뿐입니다. 어느 정도로 테스트를 해야 할지, 어떤 것을 테스트해야 할지는 **상황이나 테스트의 목적에 따라, 또 사람에 따라 다를 수 있습니다!** 항상 어떤 것을 얼마나 테스트해야 할지에 대해서는 고민해 볼 필요가 있을 것 같아요 :)
