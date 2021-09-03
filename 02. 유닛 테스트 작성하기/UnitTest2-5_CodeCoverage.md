# Unit Test 작성하기

## 5. Code Coverage
![0](https://user-images.githubusercontent.com/73867548/131962882-c8922515-4afa-4ba9-919d-ce148625cef1.jpg)    

`Code Coverage`는 위 사진과 같이 테스트의 가치를 측정해 주는 툴입니다. 좀 더 쉬운 말로 **실제 앱 코드에서 어느 정도의 테스트가 진행되었는지**를 알 수 있는 툴이라고 할 수 있겠네요. Code Coverage를 활용하면 아래의 3가지에 대해서 확인할 수 있습니다.

- 실제 테스트에서 어떤 코드가 실행되었는지 
- 정확성, 성능에 대해 얼마나 충분히 테스트가 이루어졌는지
- 테스트가 포함하고 있지 않은 코드는 무엇인지

<br>

### ✐ Code Coverage 확인하기

Code Coverage를 확인하는 방법은 아래와 같습니다.

![1](https://user-images.githubusercontent.com/73867548/131219065-cf597655-9cde-4e1f-b914-d0fab1b9b047.jpg)

메뉴바에서 `Product - Scheme - Edit Scheme`으로 접근합니다.


![2](https://user-images.githubusercontent.com/73867548/131219061-65a6dad8-1583-445f-97a7-0bd61b9f156d.jpg)

`Test - Options - Code Coverage`의 체크를 활성화해줍니다. 여기서 타깃을 원하는 타깃만 지정해 줄 수가 있고, 모든 타깃을 설정해 줄 수도 있습니다.

![3](https://user-images.githubusercontent.com/73867548/131219064-bf7ecb04-f87c-44d7-8367-f339e684475d.jpg)

설정을 마치면 내비게이션 영역의 Report 내비게이션에서 Test log에서 Coverage를 선택하시면 해당 테스트에서의 Code Coverage를 확인할 수 있습니다. 

> ### 🤔 테스트를 작성하지 않은 영역의 Coverage는 왜 높게 나타나나요?
> Coverage 수치는 테스트가 실행되는 동안 지나온 모든 코드에 대해서 결정됩니다. 즉 ViewController를 예로 들자면, 테스트를 진행하기 위해서 Simulator를 실행시키게 되는데 우리의 프로젝트에서는 기본 메서드인 `viewDidLoad()`만 구현되어 있는 상태이기 때문에 테스트 중에는 모든 코드를 Cover 하게 되는 것입니다.

<br>

### ✐ 개별 파일에서 확인하기

우리가 방금까지 테스트를 작성했던 LottoMachine.swift의 coverage는 약 70퍼센트네요. 어떤 부분이 cover 되지 않은지 알 것 같긴 한데, 직접 확인해볼까요?

![4](https://user-images.githubusercontent.com/73867548/131242379-e199bf77-0cbf-4f1f-9b5d-75c6644c2861.jpg)

이렇게 원하는 파일에 커서를 올리면 파일로 이동하는 버튼이 활성화됩니다. 버튼을 따라 파일로 이동하면

![5](https://user-images.githubusercontent.com/73867548/131242482-c3cf4b02-5192-47bb-80de-7e8eac940339.jpg)

이렇게 코드 영역의 우측에 **빨간 박스**로 cover 되지 않은 영역을 알려줍니다. 저 빨간 박스 위에 커서를 올리면

![6](https://user-images.githubusercontent.com/73867548/131242479-9c6f88ee-8f11-4525-807a-cabad26f8b8a.jpg)

이렇게도 확인할 수가 있습니다. 우리가 테스트를 작성하지 않은 마지막 메서드에 대해서만 cover 되지 않았네요. 그럼 `LottoMachine`의 마지막 메서드에 대해서도 테스트를 작성해볼까요?!

<br>

# Do it yourself! 스스로 해보세요!

### ✐ countMatchingNumber(user:winner:)

```swift
func countMatchingNumber(user: [Int], winner: [Int]) throws -> Int {
    guard isValidLottoNumbers(of: user) && isValidLottoNumbers(of: winner) else {
        throw LottoMachineError.invalidNumbers
    }
        
    let winNumbers = user.filter { winner.contains($0) }
    return winNumbers.count
}
```

마지막 로또 번호 중에 몇 개의 번호를 맞추었는지를 반환하는 메서드에 대해서 유닛 테스트를 작성해봅시다.

<br>
