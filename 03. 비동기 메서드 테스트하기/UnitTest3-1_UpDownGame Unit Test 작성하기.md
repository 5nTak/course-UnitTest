# 비동기 메서드 테스트하기

## 1. UpDownGame Unit Test 작성하기
다운로드한 프로젝트를 열어보면 `UpDownGameTests`라는 테스트 파일이 이미 생성되어 있을 것입니다. 우리는 여러 파일 중에 UpDownGame에 대해서 테스트를 작성해볼 예정이에요. 그럼 `UpDownGame.swift` 파일을 먼저 열어볼까요? UpDownGame은 3개의 메서드를 가지고 있습니다.

- `makeRandomValue(completionHandler:)`: 네트워크 통신을 통해 random 값을 얻어오는 메서드입니다.
- `reset(completionHandler:)`: UpDownGame을 처음 상태로 초기화합니다.
- `compareVlaue(with:)`: randomValue와 hitNumber의 값을 비교하여 결과를 반환합니다.

우리가 UpDownGame에서 테스트할 메서드는 이 3개의 메서드입니다. completionHandler가 포함된 ` makeRandomValue(completionHandler:)`와 `reset(completionHandler:)`는 잠시 미뤄두고, 우리는 간단하게 `compareValue(with:)`에 대한 테스트 먼저 진행해보도록 하겠습니다. 

<br>

### ✐ compareValue(with:)

```swift
func compareValue(with hitNumber: Int) -> HitResult {
    if randomValue == hitNumber {
        return .Win
    } else if tryCount >= 5 {
        return .Lose
    } else if hitNumber > randomValue {
        return .Down
    } else {
        return .Up
    }
}
```

유저가 선택한 숫자(hitNumber)를 전달받아 컴퓨터가 정해놓은 randomValue와 비교하여 비교 결과를 반환하는 메서드입니다. 어떤 테스트 케이스를 작성해야 할지 생각해 봅시다!

- hitNumber가 randomValue보다 작을 때
- hitNumber가 randomValue보다 클 때
- hitNumber가 randomValue와 같을 때
- tryCount가 5이면서 hitNumber가 randomValue이 일치하지 않을 때

등에 대한 테스트를 작성해볼 수 있을 것 같아요. 세분화한다면 물론 더 많은 테스트를 작성해볼 수도 있겠지요? <br>   

여기까지는 LottoMachine에서의 테스트 코드처럼 작성해보시면 될 것 같습니다. 역시 예시 코드를 보기 전에 직접 작성해보면 더 좋을 것 같습니다!

<br>

#### 👀 예시 코드
예시 코드는 예시일 뿐, 정답이 아닙니다.

```swift
import XCTest
@testable import UpDownGame

class UpDownGameTests: XCTestCase {
    var sut: UpDownGame!

    override func setUpWithError() throws {
        try super.setUpWithError()
        sut = UpDownGame()
    }

    override func tearDownWithError() throws {
        try super.tearDownWithError()
        sut = nil
    }

    // MARK: - compareVlaue
    func test_hitNumber가_randomValue보다_작을때() {
        // given
        let hitNumber = 10
        sut.randomValue = 5
        
        // when
        let result = sut.compareValue(with: hitNumber)
        
        // then
        XCTAssertEqual(result, .Down)
    }
    
    func test_hitNumber가_randomValue보다_클때() {
        // given
        let hitNumber = 5
        sut.randomValue = 10
        
        // when
        let result = sut.compareValue(with: hitNumber)
        
        // then
        XCTAssertEqual(result, .Up)
    }
    
    func test_hitNumber가_randomValue과_같을때() {
        // given
        let hitNumber = 10
        sut.randomValue = 10
        
        // when
        let result = sut.compareValue(with: hitNumber)
        
        XCTAssertEqual(result, .Win)
    }
    
    func test_tryCount가5이면서_hitNumber가_randomValue과_다를때() {
        // given
        let hitNumber = 7
        sut.randomValue = 10
        sut.tryCount = 5
        
        // when
        let result = sut.compareValue(with: hitNumber)
        
        // then
        XCTAssertEqual(result, .Lose)
    }
}
```

<br>

다음 페이지에서는 completionHandler가 있는 `비동기 메서드`를 테스트해보도록 하겠습니다.