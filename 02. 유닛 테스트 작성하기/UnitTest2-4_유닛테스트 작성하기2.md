# Unit Test 작성하기


## 4. Unit Test 작성하기 - 2
이번에는 `LottoMachine`으로 조금 더 복잡한 메서드의 테스트를 작성해보도록 하겠습니다. Sample 폴더에 있는 `LottoMachine.swift` 파일을 열어봅시다! LottoMachine에는 3개의 메서드가 작성되어 있습니다.

- `isValidLottoNumbers(of:)`: 전달받은 배열이 유효한 로또 번호인지 검증합니다.
- `makeRandomLottoNumbersArray()`: 랜덤으로 로또 번호 6자리를 배열로 만들어 반환해 줍니다.
- `countMatchingNumber()`: 1등 번호와 사용자의 번호가 얼마나 일치하는지를 반환합니다.

그럼 StrangeCalculator에서 했던 것처럼 LottoMachine을 테스트하기 위한 타깃을 먼저 추가해볼까요? Do it yourself! 

<br>

### ✐ isValidLottoNumbers(of:)
테스트 파일을 잘 만드셨나요? 그렇다면 `isValidLottoNumbers(of:)` 메서드에 대한 테스트를 작성해봅시다.

```swift
func isValidLottoNumbers(of numbers: [Int]) -> Bool {
    guard numbers.count == 6, Set(numbers).count == 6 else {
        return false
    }
    
    for num in numbers {
        guard 1...45 ~= num else {
            return false
        }
    }

    return true
}
```

Int 타입의 배열을 전달받아 유효한 로또 번호인지 검사하고 있습니다.    
테스트를 작성하기 전에 이 메서드에서 어떤 것이 테스트되어야 할지 생각해 볼까요?

- 전달된 배열의 원소 수가 6개가 되어야 한다.
- 중복된 번호가 있는지
- 모든 수가 1부터 45 사이의 범위의 수인지

테스트를 해야 할 내용을 이 정도로 정해볼 수 있을 것 같습니다. Test Case는 몇 개가 필요할까요? 위의 각 항목을 확인하는 과정에도 여러 개의 Test Case가 필요할 것 같은데요, 아래와 같이 Test Case를 세분화해볼 수 있을 것 같아요.

- **전달된 배열의 원소 수가 6개가 되어야 한다.**
    - 6개보다 적은 숫자가 입력되었을 때
    - 6개보다 많은 숫자가 입력되었을 때
    - 6개보다 많은 숫자가 입력되었지만 중복된 숫자가 있을 때
- **중복된 번호가 있는지**
    - 중복된 숫자 없이 6개의 숫자가 입력되었을 때
    - 6개의 숫자가 입력되었지만 중복된 숫자가 있을 때
- **모든 수가 1부터 45 사이의 범위의 수인지**
    - 중복 없는 6개의 수 중에 1보다 작은 수가 포함되어 있을 때
    - 중복 없는 6개의 수 중에 45보다 큰 수가 포함되어 있을 때
    - 중복 없는 6개의 수가 모든 수가 1부터 45 범위에 포함되는 배열이 전달되었을 때

이 정도의 테스트가 이루어져야 할 것 같습니다. 따라서 아래와 같이 테스트가 진행될 수 있겠습니다. (앞서 언급했듯이 테스트의 상황과 기준에 따라 무엇을, 얼마나, 어떻게 테스트할지는 다를 수 있습니다.) 


> 📌 꼭 이렇게 어떤 테스트를 해야 할지를 미리 세분화하지 않고도 테스트를 작성하면서 생각해 볼 수도 있을 것 같아요 :)

<br>

### ✐ 테스트 코드 작성하기
```swift
func test_isValidLottoNumbers호출시_6개보다적은숫자입력때_False를반환하는지() {
        // given
        let input = [3, 6, 9]
        
        // when
        let result = sut.isValidLottoNumbers(of: input)
        
        // then
        XCTAssertFalse(result)
    }
```

앞서 작성해본 테스트와 비슷하게 이렇게 작성해볼 수 있을 것 같습니다. 남은 테스트 케이스들도 스스로 작성해볼까요? 코드를 확인하기 전에 스스로 해봅시다!

 <br>

 #### 👀 예시 코드
 예시 코드는 예시일 뿐, 정답이 아닙니다.
 ```swift
func test_isValidLottoNumbers호출시_6개보다적은숫자입력때_False를반환하는지() {
    // given
    let input = [3, 6, 9]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertFalse(result)
}
    
func test_isValidLottoNumbers호출시_6개보다많은숫자입력했을때_False를반환하는지() {
    // given
    let input = [3, 6, 9, 12, 15, 18, 21, 24]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertFalse(result)
}
    
func test_isValidLottoNumbers호출시_중복해서6개가되는_6개보다많은숫자를입력했을때_False를반환하는지() {
    // given
    let input = [3, 6, 9, 12, 15, 18, 15, 9]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertFalse(result)
 }
    
 func test_isValidLottoNumbers호출시_중복된숫자없이_6개숫자입력했을때_True를반환하는지() {
    // given
    let input = [3, 6, 9, 12, 15, 18]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertTrue(result)
}
    
func test_isValidLottoNumbers호출시_중복된숫자가있는_6개숫자입력했을때_False를반환하는지() {
    // given
    let input = [3, 6, 9, 9, 15, 18]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertFalse(result)
}
    
func test_isValidLottoNumbers호출시_1보다작은수를포함하는배열전달했을때_False를반환하는지() {
    // given
    let input = [0, 3, 6, 9, 12, 15]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertFalse(result)
}
    
func test_isValidLottoNumbers호출시_45보다큰수를포함하는배열전달했을때_False를반환하는지() {
    // given
    let input = [3, 6, 9, 12, 15, 50]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertFalse(result)
}
    
func test_isValidLottoNumbers호출시_모든수가1부터45범위에포함되는배열전달했을때_True를반환하는지() {
    // given
    let input = [1, 5, 15, 25, 35, 45]
        
    // when
    let result = sut.isValidLottoNumbers(of: input)
        
    // then
    XCTAssertTrue(result)
}
 ```

<br>

### ✐ makeRandomLottoNumbersArray()

```swift
func makeRandomLottoNumbersArray() -> [Int] {
    var numberSet: Set<Int> = []
        
    while numberSet.count < 6 {
        let randomNumber = Int.random(in: 1...45)
        numberSet.insert(randomNumber)
    }
        
    return Array(numberSet)
}
```

`makeRandomLottoNumbersArray()`는 6개의 로또 번호를 만들어주는 메서드입니다.    
`makeRandomLottoNumbersArray()` 메서드는 어떻게 테스트를 하면 좋을까요? 이건 위에 작성했던 테스트에 비하면 좀 더 간단하게 테스트를 할 수 있을 것 같습니다. 메서드가 만들어준 배열이 **유효한 로또 번호인지**를 비교하면 되기 때문에 이미 테스트를 거친 `isValidLottoNumbers(of:)` 메서드를 활용한다면 쉽게 테스트를 작성해볼 수 있을 것 같습니다.

```swift
func test_makeRandomLottoNumbersArray_반환배열이유효한지() {
    // given
    let randomNumbers = sut.makeRandomLottoNumbersArray()
        
    // when
    let result = sut.isValidLottoNumbers(of: randomNumbers)
        
    // then
    XCTAssertTrue(result)
}
```

> 📌 사실 이 메서드는 **Random** 값을 만들어내기 때문에 완전히 통제된 테스트를 진행할 수는 없습니다. 만약 테스트의 신뢰를 높여주어야 하는 상황이라면 해당 테스트를 많은 횟수로 반복시키는 것도 방법이 될 수 있을 것 같습니다. (이게 방법이 될 수 있을까..)

<br>

**이제 LottoMachine의 마지막 메서드를 테스트해보기 전에..! 바로 다음 페이지에서 `Code Coverage`에 대해서 잠깐 알아보고 갈까요?**