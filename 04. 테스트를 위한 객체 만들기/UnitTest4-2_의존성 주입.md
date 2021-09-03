# 테스트를 위한 객체 만들기 (네트워크 연결 없이 동작하는 테스트)

## 2. 의존성 주입 (Dependency Injection)
의존성 주입은 하나의 객체가 다른 객체의 의존성을 제공하는 기술로 줄여서 DI(Dependency Injection)이라고 부르기도 합니다. 의존성 주입은 말보다는 코드로 설명하는 편이 이해가 더 빠를 것 같아요!

<br>

### ✐ 의존성이란?

```swift
class Car {
    var wheel: Wheel = Wheel()
}

class Wheel {
    var weight = 10
}
```

의존성이라는 말이 낯설게 느껴지지만 간단한 개념입니다. 어떤 객체가 내부에서 생성하여 가지고 있는 객체를 **의존성(Dependency)이라고** 합니다.   
위의 코드에서는 Car 클래스 내부에서 사용된 Wheel가 **의존성(Dependency)입니다.** 그리고 Car라는 클래스는 Wheel이라는 클래스에 **의존**하고 있는 것이 됩니다.

<br>

### ✐ 의존성 주입이란?

```swift
class Car {
    var wheel: Wheel

    init(wheel: Wheel) {
        self.wheel = wheel
    }
}

class Wheel {
    var weight = 10
}

let myWheel = Wheel()
let myCar = Car(wheel: myWheel)
```

의존성 주입이란 말 그대로 의존성을 *주입* 시킨다는 뜻입니다. 내부에서 초기화가 이루어지는 것이 아니라 외부에서 객체를 생성하여 내부에 **주입**해주는 것입니다. 위 코드에서는 myWheel을 외부에서 생성하여 Car를 초기화할 때 myWheel을 **주입**시켜주고 있습니다.

<br>

### ✐ 의존성 주입은 왜 사용하나요?
의존성 주입을 사용하는 이유는 객체간의 결합도를 낮추기 위해서입니다. 객체 간의 결합도가 낮으면 리팩토링이 쉽고, 테스트 코드 작성이 쉬워진다는 장점이 있었습니다. 예제의 코드를 잠깐 살펴봅시다.

```swift
class UpDownGame {
    var randomValue: Int = 0
    var tryCount: Int = 0
    var urlSession: URLSessionProtocol
    
    init(urlSession: URLSessionProtocol = URLSession.shared) {
        self.urlSession = urlSession
    }

    ...
}
```

예제 코드에서는 `URLSessionProtocol`이라는 프로토콜을 만들어서 직접적으로 URLSession을 타입으로 만들지 않고 의존성을 주입할 수 있도록 되어있습니다. 이렇게 의존성을 주입하도록 만든 이유는 테스트를 진행할 때에는 앞서 설명했던 Test Double 객체를 실제 URLSession의 자리에 바꿔치기 시키면서 테스트를 진행시키기 위함입니다. 이는 결합도가 낮아졌기 때문에 가능한 이야기라고 할 수 있습니다. 

<br>



