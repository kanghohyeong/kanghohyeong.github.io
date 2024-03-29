---
layout: post
title: "함수 기반 랜더링 방식으로 SPA 구현하기"
date: 2021-10-11
categories:
  - Dev
tags:
  - javascript
  - FP
author: HoHyeong Kang
excerpt: "바닐라스크립트로 함수형 프로그래밍 아이디어를 도입하여 SPA을 위한 비동기 View 랜더링을 구현해보았습니다."
---



### View를 그리는 과정과 함수형 프로그래밍

지난 2주간 조금 색다른 도전을 하였습니다. 함수형 프로그래밍에 대해 알아보다가 프론트앤드에서 동시다발적으로 발생하는 비동기 이벤트를 효과적으로 처리하기위한 방법으로 FRP(Functional Reactive Programming)가 적극적으로 도입되고 있다는 것을 알게 되었습니다. 여기서 저는 이벤트 관리, 상태 변화의 측면이 아닌 **뷰를 생성하고 합성하는 과정에도 함수형 프로그래밍의 아이디어를 적용**해볼 수 있지 않을까라는 생각을 하게 되었습니다. 그렇게 **함수 기반 랜더링** 방법을 고안하게 되었고 실제로 2주동안 SPA 프로젝트에 적용시켜보았습니다. 그 과정에서 함수형 프로그래밍의 장점을 피부로 느낄 수 있게 되었고, 이 방식이 제법 유용하다고 생각되어서 범용적으로 사용할 수 있도록 코드를 정리한 후 이를 토대로 [**함수 기반 랜더링을 도입한 SPA 예제**](https://github.com/kanghohyeong/function-based-rendering)를 생성하였습니다. 이 글에서는 함수 기반 랜더링에 대한 간단한 소개와 예제를 만들면서 중요하게 생각했던 것들에 대해 간략하게 기술하고자 합니다. 자세한 내용은 예제 저장소에 적어놓았으니 참고 바랍니다.

저장소 주소 : [https://github.com/kanghohyeong/function-based-rendering](https://github.com/kanghohyeong/function-based-rendering)



### 기본 아이디어

함수 기반 랜더링의 기본 아이디어는 화면을 구성하고 로직을 주입하는 과정을 **수평적으로 나열**할 수 있을 것 같다는 생각에서 시작하였습니다. 이전에 시도하였던, 또 흔히 볼 수 있는 컴포넌트 기반의 랜더링 방식은 과정이 수직적으로 중첩되어 있습니다. 상위 컴포넌트에서 자신의 탬플릿을 정의하고 그 안에 하위 컴포넌트를 편입(마운트) 시키며 그 하위 컴포넌트는 또 다시 자신의 하위 컴포넌트를 마운트 시킵니다. 또한 컴포넌트 자신과 관련된 로직(이벤트리스너 등록 등)은 그 컴포넌트 내에서 처리합니다. 이는 컴포넌트 단위로 어떤 책임을 가지고 있는지를 쉽게 구분할 수 있게 하지만, 컴포넌트들이 중첩되어 있어서 전체 랜더링 과정을 파악하는 것을 어렵게하며, 컴포넌트 하나하나가 비대해져서 재사용이 힘들게 됩니다.

함수 기반 랜더링에서는 하나의 화면을 구성하기 위해서 각 단계에서 **무엇을** 해야하는 지에 집중합니다. 화면이 그려지는 과정을 생각해보자면 다음과 같을 것입니다. `화면 틀을 그린다 -> 해더 영역을 그린다 -> 색션 영역을 그린다 -> ... ` 함수 기반 랜더링에서는 이러한 생각을 직관적으로 코드로 표현할 수 있게 해줍니다. View를 그리는 함수를 순서대로 합성함으로써 더 큰 View 를 만들 수 있습니다. 또한 각 DOM element에 로직을 처리하는 것도 View를 그리는 함수와 자유롭게 합성할 수 있게 하여서, 결론적으로 하나의 화면을 그리는데 어떤 View 들이 그려지고, 어떤 로직이 주입되는지를 한 눈에 볼 수 있게 해줍니다. 

```javascript
//실제 사용된 랜더링 함수
//무엇이 그려지고, 어떤 로직이 처리되는지 한 눈에 보인다.
renderPipe( 
    PageView,
    ContentView, 
    ListView, 
    LeftBoxView, 
    RightBoxView,
    listColorHandler,
    validBtnHandler,
    syncBtnHandler,
    fetchBtnHandler
)({ $app });
```



### SPA에서 유용하게 사용할 수 있을까?

그동안 바닐라 자바스크립트로 SPA를 구현하면서 SPA 설계의 핵심은 **데이터 변화에 따라 화면의 일부분을 비동기적으로 랜더링 할 수 있는가** 라고 생각하게 되었습니다. 화면의 부분만 따로 랜더링 할 수 없다면 페이지 전체를 랜더링하여 반환하는 전통적인 방식과 다르지 않습니다. 특히 DOM 노드가 변화하는 Reflow 과정은 상당히 heavy한 과정이므로 필요한 부분만 다시 랜더링 할 수 있어야 합니다. 또한 동시다발적으로 발생하는 사용자 이밴트와 언제 끝날지 모르는 http 요청에 대응하기 위해 비동기적으로 랜더링이 가능해야 합니다. 

저는 제가 고안한 함수 기반 랜더링이 SPA에서 적절하게 적용될 수 있는가를 확인하기 위해 옵저버 패턴을 활용하여 간단한 data binding을 구현하였습니다. 데이터와 랜더링 함수를 바인딩하여 데이터가 변화할 때마다 랜더링 함수가 실행되도록 하였습니다. SPA에서 발생할 수 있는 다양한 비동기 부분 랜더링을 실험하였고 모두 원활하게 구현되었습니다. 특히 컴포넌트 기반으로 바인딩 할 때에는 설계에 따라 컴포넌트 생명주기를 고려하여 컴포넌트가 생성될 때 바인딩하고 사라질때 바인딩을 해제하는 등의 복잡한 로직이 필요할 때도 있었는데 함수 기반 랜더링에서는 그러한 문제가 전혀 발생하지 않았습니다. 오히려 상태가 변화할 때 무엇이 그려지고, 어떤 로직이 추가되는지를 코드만 보아도 아주 명확하고 단순하게 인지할 수 있었습니다.

![Animation](https://user-images.githubusercontent.com/63776725/136706271-2084701d-248d-43e5-a99d-dc0738539c1a.gif)

*<center> 데모 </center>*



### 결론

이 예제를 제작하면서 선언형으로 작성된 코드의 가독성이 정말 좋다고 느꼈고, 무엇을 해야하는지를 중심으로 로직을 추상화하는 함수형 프로그래밍의 장점이 잘 느껴졌습니다. 또한 작은 단위로 나누어진 함수들을 자유롭게 조립하여 더 큰 함수로 만드는 접근법이 상당히 직관적이고 유용했습니다. 상태에 대한 의존, 전역적으로 존재하는 DOM에 대한 참조 등의 이유로 제가 만든 함수들이 순수하다고 하기는 힘들 것 같지만 커링, 함수 합성 등 함수형 프로그래밍의 아이디어를 여러 군데 사용하였습니다. 이번에 구상한 함수 기반 랜더링에 FRP을 도입한다면 더 강력하게 활용할 수 있을 것 같은 생각이 듭니다. 여러모로 재미있는 공부였습니다.

