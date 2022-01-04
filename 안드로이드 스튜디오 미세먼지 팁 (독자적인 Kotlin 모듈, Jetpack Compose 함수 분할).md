## 안드로이드 스튜디오 미세먼지 팁 (독자적인 Kotlin 모듈 / Jetpack Compose 함수 분할)

### 안드로이드 스튜디오 내부에서 독자적인 Kotlin 모듈 만들기

1. 안드로이드 스튜디오 좌측 App 에 오른쪽 버튼
    New -> Module 을 클릭한다.
![Kotlin 모듈 1](https://user-images.githubusercontent.com/80164141/148077461-6094185c-e132-42c9-baf7-2334a64318b9.JPG)
  
2. Application / Library name 에 Kotlin Module 이름 생성
![Kotlin 모듈 2](https://user-images.githubusercontent.com/80164141/148077471-0eb4f103-22cd-4b90-af1d-3e5c8f6747b9.jpg)
> Module name 에는 문법을 위한 이름이 설정된다. 

3. [설정한 이름]의 새로운 모듈 내부에, java 파일 내부에서 원하는 코틀린을 실행한다.
![Kotlin 모듈 3](https://user-images.githubusercontent.com/80164141/148077473-c5abaa6a-2dc7-4f3a-9856-9cce5d2a3c46.jpg)

- `fun main(){}` 은 Kotlin 의 시작이다. 없으면 실행이 안된다.
- 해당 코틀린 파일 (혹은 class) 을 오른쪽 클릭, 실행을 누르면 실행할 수 있다.
- App 과 별개로 코틀린이 작동되며, 밑에 터미널처럼 결과가 나온다.
- [Kotlin Playground](https://play.kotlinlang.org/?_gl=1*1wujwke*_ga*MTU5NDEwOTY2OC4xNjQxMzA4NTMy*_ga_J6T75801PF*MTY0MTMwODUzMi4xLjAuMTY0MTMwODUzMi4w&_ga=2.170054563.2015189665.1641308532-1594109668.1641308532#eyJ2ZXJzaW9uIjoiMS42LjEwIiwicGxhdGZvcm0iOiJqYXZhIiwiYXJncyI6IiIsIm5vbmVNYXJrZXJzIjp0cnVlLCJ0aGVtZSI6ImlkZWEiLCJjb2RlIjoiLyoqXG4gKiBZb3UgY2FuIGVkaXQsIHJ1biwgYW5kIHNoYXJlIHRoaXMgY29kZS4gXG4gKiBwbGF5LmtvdGxpbmxhbmcub3JnIFxuICovXG5cbmZ1biBtYWluKCkge1xuICAgICB2YWwgbiA9IDEyXG4gICAgIHZhbCBkaWdpdCA9IG4udG9TdHJpbmcoKS5tYXAgeyBpdC5jb2RlIC0gNDggfS50b1R5cGVkQXJyYXkoKVxuICAgICB2YXIgcCA9IGRpZ2l0LmNvbnRlbnREZWVwVG9TdHJpbmcoKS50b0ludCgpXG4gICAgIHByaW50bG4ocClcbn0ifQ==) 에서도 가능하긴 하지만, 입력을 자유자재로 할 수 있는 이 방법이 더 편한 듯 하다.  
  
  ---
  
### Jetpack Compose 함수를 쉽게 Composable 로 분할해보자.

#### 상황

![JetpackCompose 분할 1](https://user-images.githubusercontent.com/80164141/148079503-48d4a778-2185-4904-965e-3705db05505e.jpg)
Column 안에 이러한 코드가 있는 상황에서, Surface 함수 전체를 Composable 함수로 분할하고 싶은 상황이다.

1. 분할하고자 하는 코드를 전부 드래그해서 블록을 씌운 후, `오른쪽 클릭 -> Refactor -> Function`
![JetpackCompose 분할 2](https://user-images.githubusercontent.com/80164141/148079512-5f246989-a2b6-4492-9b24-6dcc16bec3e2.jpg)

2. 함수의 이름을 설정하면 ..?
![JetpackCompose 분할 3](https://user-images.githubusercontent.com/80164141/148079518-af97a90b-1f97-482f-8275-9a3d8c47a5e4.jpg)

3. 무쳤다 ..
![JetpackCompose 분할 4](https://user-images.githubusercontent.com/80164141/148079521-0329d543-6156-4536-bc95-d9cb4e7a4f02.jpg)

4. modifier 로 파라미터를 넘겨주고 싶다면 `modifier: Modifier = Modifier` 를 넣자.
![JetpackCompose 분할 5](https://user-images.githubusercontent.com/80164141/148079523-b7582452-b6ff-47e6-93d4-aeb4a9319086.jpg)
- 이때, 함수 내부의 모든 `Modifier` 는 매개변수의 `modifier` 로 교체해주어야 한다.
- 새로운 `modifier` 가 들어오면 새로운 값은 `override`, 기존 값은 그대로 있는다. 

5. 매개변수를 미리 넣고 싶다면 ..
> 음.. 모르겠어요.. 
> `var image:String = "abcde"`형태로 미리 선언한 값을 넣은 후, Composable 분할 후 다시 복사해서 매개변수 값에 넣고 var 나 val 만 지우는 방법을 쓰면 그나마 편하긴 하지만, Refactor 단위에서 하는 방법은 없는 것 같다.

