# Unit Test 작성하기

## 2. Test 파일의 기본 코드 살펴보기

프로젝트 파일을 만들었다면 한 번 열어보도록 합시다! 
우리가 테스트를 작성할 `StrangeCalculatorTests.swift` 파일을 열면 기본적으로 코드가 작성되어 있습니다. 처음 보는 코드들이 있네요! 어떤 코드들인지 살펴보도록 할까요?

<br>

### ✐ XCTest
```swift
import XCTest
```
XCTest는 유닛 테스트, 퍼포먼스 테스트, UI 테스트를 만들고 실행하는 프레임워크입니다.    
테스트에서 사용되는 코드들을 사용하기 위해서는 이 XCTest 프레임워크가 반드시 import 되어야 합니다. 

<br>

### ✐ XCTestCase
```swift
class StrangeCalculatorTests: XCTestCase {
    ...
}
```

그리고 테스트를 위한 클래스가 만들어져있는 것을 확인할 수 있을 거예요. 테스트 클래스가 상속하고 있는 `XCTestCase`는 무엇일까요? <br>

`XCTestCase`는 추상 클래스인 `XCTest의 하위 클래스`로, 테스트를 작성하기 위해 상속해야 하는 가장 기본적인 클래스입니다. **XCTest는 테스트를 위한 프레임워크의 이름이기도 하고, 테스트에서 가장 기본이 되는 추상 클래스의 이름이기도 합니다.** 프레임워크와 클래스의 이름이 같은 경우입니다.    
XCTestCase를 상속받은 클래스에서는 test에서 사용되는 다양한 프로퍼티와 메서드를 사용할 수 있습니다. 

<br>

### ✐ setUpWithError()
```swift
override func setUpWithError() throws {
    // Put setup code here. This method is called before the invocation of each test method in the class.
}
```

`setUpWithError()`는 각각의 test case가 실행되기 전마다 호출되어 테스트의 state를 reset 하도록 도와주는 메서드입니다. <br> 

예를 들어 몇몇 테스트가 A라는 값에 대해서 이루어지고 있는데, 먼저 작성된 case에 의해서 값이 변경된다면 다음 테스트는 정상적으로 이루어지지 않을 위험이 있겠지요? 정상적으로 테스트를 하기 위해서는 setUpWithError와 같은 메서드를 통해 test case가 이루어질 때마다 테스트의 상태를 reset시켜주어야 합니다.

<br>

### ✐ tearDownWithError()
```swift
override func tearDownWithError() throws {
    // Put teardown code here. This method is called after the invocation of each test method in the class.
}
```

tearDownWithError()는 각각의 test 실행이 끝난 후마다 호출되는 메서드입니다. 보통 setUpWithError()에서 설정한 값들을 해제할 때 사용됩니다. <br>

그러니까 여러 개의 테스트 케이스가 실행되는 경우 `setUpWithError()`와 `tearDownWithError()`는 아래의 그림과 같은 순서로 호출됩니다.   

![8](https://user-images.githubusercontent.com/73867548/131817287-16ea489a-b9d6-4aee-a7ae-29181af53fb9.jpg) 

> ### 🤔 setUp()과 tearDown()라는 메서드도 있던데 무슨 차이인가요?
> setUp()과 tearDown() 메서드와 setUpWithError(), tearDownWithError()의 차이는 에러를 throw 할 수 있느냐에 대한 차이입니다. 원래는 setUp()과 tearDown() 메서드가 기본 메서드로 제공이 되었는데 Xcode 11.4 버전 이후로 기본으로 setUpWithError(), tearDownWithError()를 제공하고 있어요.    
좀 더 내용이 궁금하다면 [Understanding Setup and Teardown for Test Methods](https://developer.apple.com/documentation/xctest/xctestcase/understanding_setup_and_teardown_for_test_methods) 문서를 참고해보면 좋을 것 같습니다 :)

<br>

### ✐ testExample()
```swift
func testExample() throws {
    // This is an example of a functional test case.
    // Use XCTAssert and related functions to verify your tests produce the correct results.
}
```

`test`로 시작하는 메서드들은 우리가 작성해야 할 test case가 되는 메서드입니다. 우리가 테스트할 내용을 메서드로 작성해볼 수 있습니다. 메서드 네이밍의 시작은 무조건 test로 시작되어야 합니다.

> 🤔 보통 프로그래밍의 네이밍은 거의 영어로 해주었지만 특이하게 테스트 함수의 네이밍은 한글로 작성하는 경우도 있어요. 그 이유는 테스트 코드가 기능을 **명세화, 문서화** 하는 역할도 하기 때문이라고 할 수 있습니다. 한국인들끼리 작업하는 경우라면 영어보다 한글로 테스트를 작성할 때 더 쉽게 눈에 들어올 수 있겠네요!

<br>

### ✐ testPerformanceExample()
```swift
func testPerformanceExample() throws {
    // This is an example of a performance test case.
    measure {
        // Put the code you want to measure the time of here.
    }
}

// mesure
func measure(_ block: () -> Void)
```
성능을 테스트해보기 위한 메서드입니다. XCTestCase의 `measure(block:)`라는 메서드를 통해 성능을 측정하게 됩니다.    
**예시로 작성된 testExample()과 testPerformanceExample()은 사용하지 않을 코드이니 지워주시면 됩니다!** 

<br>



