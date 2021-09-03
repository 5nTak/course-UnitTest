# Unit Test 작성하기

## 1. Unit Test 프로젝트 파일 만들기
Test를 진행하기 위해서는 먼저 **Test 파일**이 필요합니다. Test 파일은 프로젝트를 처음 생성할 때 만들어주는 방법이 있고, 이미 만들어진 프로젝트 내부에서 생성하는 방법이 있습니다. 우리는 이미 프로젝트 파일이 있기 때문에 두 번째 방법으로 Test를 만들어보도록 하겠습니다. <br>

![1](https://user-images.githubusercontent.com/73867548/131237814-b8a9619e-7bc2-4a94-b0e5-6428b481c22c.jpg)   

네비게이터 영역에서 6번째 영역인 `테스트 네비게이터(Test navigator)`로 이동합니다. 그리고 왼쪽 하단의 `+` 버튼을 눌러 `New Unit Test Target`을 선택해 줍니다. (Xcode에서는 Unit Test 파일과 UITest 파일을 만들어줄 수 있어요. 우리는 지금 Unit Test를 작성할 거니까 Unit Test 파일만 만들면 되겠지요?)     

<br>

![2](https://user-images.githubusercontent.com/73867548/131237942-7b92f1f4-a146-44ad-8154-9cf3d40b5f42.jpg)   

그럼 위와 같은 창이 나타나는 것을 볼 수 있습니다. 여기에서 Test 파일의 이름을 정해주고 Finish 버튼을 누르면 테스트 파일이 생성됩니다! 우리는 먼저 `StrangeCalculator`를 테스트해볼 예정이니 파일 이름을 이렇게 지어주면 되겠습니다. 이때 만들어지는 유닛 테스트는 새로운 `Target`으로 추가가 되는 것입니다.   

<br>

![3](https://user-images.githubusercontent.com/73867548/131237816-36fac1ba-534d-4007-963b-a87e600aa189.jpg)   

테스트 파일 생성을 한 후, 다시 프로젝트 네비게이터(Project navigator)로 돌아왔을 때 `StrangeCalculator`라는 폴더 안에 `StrangeCalculator.swift`파일과 `Info.plist` 파일이 만들어져 있다면 우리는 유닛 테스트를 성공적으로 생성한 것입니다! 간단하지요?           

> 잠깐! Info.plist는 왜 또 만들어지는 건가요? *(넣을지 고민)*   
> Info.plist 파일은 실행 패키지에 관한 필수 설정 정보가 포함된 구조화된 텍스트 파일입니다. 앱과 테스트는 다른 Target으로 생성되었습니다! 그렇기 때문에 두 실행에 대한 설정을 따로 관리해 줄 필요가 있겠죠?

<br>

### 📌 프로젝트 생성시  Test 파일 만들기
만약 프로젝트를 만드는 순간에 Unit Test를 생성해 주고 싶다면 아래의 사진처럼 `Include Tests`에 체크해 주면 됩니다. 이때에는 Unit Test와 UITest가 함께 생성됩니다.   
![4](https://user-images.githubusercontent.com/73867548/131238672-251caec2-38bf-41cb-a856-bff48c88e803.jpg)

<br>
