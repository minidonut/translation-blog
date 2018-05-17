---
title: Mocking은 코드 냄새(Code Smell)입니다
catalog: true
date: 2018-04-20 03:54:56
subtitle: Mocking is a Code Smell
header-img: "bg.jpg"
readingTime: 24
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 코드 냄새란 일반적으로 시스템의 더 깊은 문제에 해당하에 깊숙히 숨어있는 문제를 드러내는 표면적인 표시입니다. Martin Fowler.  TDD와 유닛 테스트를 할 때 가장 손이 많이 가는 부분은 테스트할 유닛을 분리하는 과정에서 필요한 mocking을 만드는 일 입니다. 몇몇 사람들은 유닛 테스트가 정말로 의미있는 일인지 의심스러워 하기도 합니다.  실제로, 저는 개발자들이 mock, fake, stub을 만들다 길을 잃고  실제 코드가 전혀 실행되지 않는  유닛 테스트를 작성하는걸 보았습니다. 아이고.
---


> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/04/18/javascript-monads-made-simple/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/04/23/the-hidden-treasures-of-object-composition/)

TDD와 유닛 테스트를 할 때 가장 손이 많이 가는 부분은 테스트할 유닛을 분리하는 과정에서 필요한 **mocking**을 만드는 일 입니다. 몇몇 사람들은 유닛 테스트가 정말로 의미있는 일인지 의심스러워 하기도 합니다.  실제로, 저는 개발자들이 mock, fake, stub을 만들다 길을 잃고  _실제 코드가 전혀 실행되지 않는_  유닛 테스트를 작성하는걸 보았습니다. 아이고..

다른 한편으로는 개발자가 TDD에 스스로를 너무 옭아매는 모습이 자주 보입니다. 이 경우 개발자는 코드베이스를 더 복잡하게 만들어버릴지라도 무조건 100% 코드 커버리지를 달성해야 한다고 생각합니다. 

저는 mocking에서 코드 냄새를 맡으라고  말합니다. 그러나 대부분의 개발자들은 100% 유닛 테스트 커버리지를 달성할 수 있다는 TDD 신봉자 단계를 거치게 되며 이 기간 동안에는 대규모의 모의 객체를 사용하지 않는 세상을 상상할 수 없습니다.  모의 객체^mocks^를 응용 프로그램에 집어 넣기 위해 의존성 주입^dependency^ ^injection^ (DI)함수로 자신의 코드유닛을 감싸거나 더 안좋은 경우, 서비스를 의존성 주입 컨테이너로 만들어버리기도 합니다.

Angular는 모든 컴포넌트 클래스에 의존성을 주입하며 DI를 기본적인 디커플링 수단으로 사용하도록 유혹합니다.  하지만 의존성 주입이 디커플링을 수행하는 가장 좋은 방법은 아닙니다.

## TDD는 더 나은 디자인으로 이어질 때 그 의미가 있습니다.

> 제대로된 TDD를 배운다는 것은 제대로 앱을 모듈화시키는 법을 배우는 것과 같습니다.

TDD는 코드를 복잡하게 만드는것이 아닌 단순하게 만들어야 합니다. 코드를 더 잘 테스트 할 수 있게 고치는 댓가로 가독성과 유지보수성을 내주어선 안됩니다.  DI 보일러 플레이트로 코드를 부풀리고 있다면 잘못된 TDD를 하고있는 겁니다.

온 세상을 mocking할 수 있다는 환상을가지고 앱에 의존성을 주입하고 있다면 그것이 도움이 되기 보다는 해가 될 가능성이 매우 높습니다.  테스트 가능한 코드를 작성하는 과정에서 코드가 단순해져야 합니다.  우리에게 필요한 것은 적은 수의 코드 라인과 보다 읽기 쉽고 유연하며 유지 보수가 가능한 구조입니다.  의존성 주입은 반대 효과를냅니다.

제가 말하고 싶은 것은 이 두 가지 입니다.

1.  의존성 주입 없이도 디커플링된 코드를 작성할 수 있습니다.
2.  코드 커버리지를 최대화하려는 시도는 좋지 않습니다. 100%에 다가갈수록 애플리케이션 코드는 복잡해지고 결국 버그를 줄인다는 목표를 뒤엎고 말게 됩니다.

 복잡한 코드에는 종종 자질구레한 코드들도 함께 있습니다. 잘 정돈된 코드를 작성하는 것은 집을 깔끔하게 유지하려는 것과 같은 이유입니다.

-  코드가 어수선해지면 버그가 숨을 수 있는 편리한 장소가 생기고 더 많은 버그가 발생합니다.
-   뭔가를 찾기 위해서는 우선 정돈된 환경이 필요합니다.


## 코드 냄새^Code^ ^smell^란 무엇입니까?

> _"코드 냄새란 일반적으로 시스템 깊숙히 숨어있는 문제를 드러내는 표면적인 표시입니다."~ Martin Fowler_

> **코드 스멜**(code smell←코드 냄새)은 컴퓨터 프로그래밍 코드에서 더 심오한 문제를 일으킬 가능성이 있는 프로그램 소스 코드의 증상을 가리킨다.  ~ Wikipedia

나쁜 코드 냄새를 맡았다고 해서 곧바로 뭔가 잘못 되었고 이를 고쳐야 한다는 것을 의미하진 않습니다.  코드 냄새란 당신에게 뭔가를 향상시킬 수있는 기회를 알려주는 _경험 법칙_ 입니다.

이 글은 모든 mocking이 나쁘다는 것을 의미하지 않으며 mocking을 절대 하면 안된다고 말하는게 아닙니다.

코드의 유형에 따라 다양한 수준 (및 다른 종류)의 mocking이 필요합니다. I/O를 다루는 코드는 I/O 를 테스트하는 것 이외에 다른 방법은 거의 없습니다. 이 경우 모의 테스트를 줄이면 유닛 테스트 커버리지가 0%에 가까워 질 수 있습니다.

로직이 없는 코드는(파이프와 순수 함수들로만 이루어진) 유닛 테스트 커버리지가 0%가 되어도 괜찮습니다. 대신 통합 또는 기능 테스트 커버리지가 100 %에 가까워질 것입니다.  그러나 로직(조건식, 변수 할당, 메소드 호출 등)이 있다면 유닛 테스트 커버리지가 필요하며, 이 때 코드를 단순화하고 mocking 요구사항을 줄이는 목표를 세울 수 있습니다.

## Mock(모의)란 무엇입니까?

Mock이란 유닛 테스트에서 실제 구현 코드를 대신하는 **테스트 더블**^[test double, 여기서 double이란 스턴드 대역 배우(stunt double)와 같은 대역이란 뜻입니다.]입니다.  모의 객체는 테스트가 진행되는 동안 테스트 코드에 의해 조작, 변경되고 이를 assert 구문으로 확인합니다. Assert 구문에 테스트 더블이 쓰인다면 이는 모의입니다.

"모의(mock)"라는 용어는 여러 종류의 테스트 더블을 포괄하는 일반적인 용어입니다. 앞으로의 논의에서 우리는 "모의(mock)"와 "테스트 더블(test double)"이라는 단어를 동등하게, 상호 교환적으로 사용하겠습니다.  모든 테스트 더블 (dummies, spies, fake 등)은 테스트 대상과 단단하게 결합된 실제 코드를 나타냅니다. 따라서 테스트 더블은 일종의 커플링이 있다는 것을 뜻하며, 구현을 단순화하고 향상시킬 수 있다는 의미입니다. 그리고 모의 객체가 필요하지 않도록 코드를 바꾸는 것은 테스트를 근본적으로 단순화시키는 방법입니다.

## 유닛 테스트란 무엇입니까?

**유닛 테스트**는 개별 유닛(모듈, 함수, 클래스)을 나머지 프로그램과 분리하여 테스트하는 행위입니다,

유닛 테스트와 달리 **통합 테스트**^Integration^ ^test^란 두 개 이상의 유닛 간의 통합을 테스트하는 것입니다.   **기능 테스트**^Functional^ ^test^란 사용자의 관점에서 애플리케이션을 테스트하는 것 입니다.  사용자 워크 플로 테스트는 UI 조작에서 데이터 레이어가 업데이트되고 그것들이 다시 화면에 출력되는 과정을 모두 관찰합니다.  기능 테스트는 앱이 실행되는 맥락에서 모든 장치와 유닛을 테스트하기 때문에 통합 테스트의 하위 집합이라고 볼 수 있습니다.

일반적으로 유닛은 유닛의 공용 인터페이스 (일명 "퍼블릭 API"또는 "surface area") 만 사용하여 테스트됩니다.  이것을 **블랙 박스 테스트**라고합니다.  블랙 박스 테스팅은 고장나기 쉬운 테스트입니다.  왜냐하면 시간이 지남에 따라 퍼블릭 API는 그대로인데 유닛의 구현 세부 사항이 변경되기 때문입니다. 내부 구현 방식까지 다루는 화이트 박스 테스트는 더더욱 고장나기 쉬운데, 퍼블릭 API가 예상대로 계속 작동하더라도 구현 세부 사항을 변경하면 테스트가 중단 될 수 있기 때문입니다.  즉, 화이트 박스 테스트에는 훨씬 많은 시간과 노력이 들어갑니다.

## 테스트 커버리지^[테스트 적용 범위]란 무엇입니까?

코드 커버리지란 테스트 케이스가 다루는 코드의 양을 의미합니다.  테스트 도중 어떤 코드의 행이 사용되었는지 기록하여 커버리지 리포트를 작성합니다. 일반적으로 우리는 커버리지를 높이려고 노력하지만 100 %에 가까워 질수록 테스트 효율이 감소하기 시작합니다.

경험에 비추어 볼 때, 90%가 넘는 커버리지는 남은 버그들을 없애는데 거의 상관 관계가없는 것 같습니다.

왜 그럴까요?  100% 테스트 된 코드란 코드가 의도 한대로 작동한다는 것을 100% 확신시켜주는 의미 아닙니까?

이 문제는 그리 간단하지 않습니다.

대부분의 사람들은 두 가지 종류의 커버리지가 있다는 것을 모릅니다.

1.  **코드 커버리지** : 코드가 실행되는 정도
2.  **케이스 커버리지** :  테스트 스위트가 커버하는 유스 케이스의 수

케이스 커버리지는 유스 케이스 시나리오를 다룹니다 : 코드가 실제 환경, 실제 네트워크에서 어떻게 작동할 것인가? 악의적인 목적으로, 소프트웨어 설계를 의도적으로 파괴하려는 해커한테 어떻게 작동 할 것인가?

커버리지 리포트는 케이스 커버리지가 아닌 코드 커버리지의 부족한 점을 알려줍니다.  그러나 동일한 코드가 둘 이상의 유스 케이스에 적용될 수 있을 뿐더러 단일 유즈 케이스가 테스트 대상 외부에 있는 코드, 별도의 애플리케이션 또는 서드파티 API에 의존 할 수도 있습니다.

유스 케이스는 환경, 여러 단위, 사용자 및 네트워킹 조건을 포함 할 수 있으므로 필요한 모든 유스 케이스를 유닛 테스트들 밖에 없는 테스트 스위트로 처리하는 것은 불가능합니다.  유닛 테스트는 통합되지 않은, 격리 된 테스트 단위를 의미하므로 유닛 테스트로만 이루어진 테스트 스위트는 항상 통합 및 기능 유스  케이스 시나리오에 대해 거의 0%의 커버리지를 달성하게 될 것입니다.

100% 코드 커버리지는 100% 케이스 커버리지를 보장하지 않습니다.

100% 코드 커버리지를 목표로 하는 개발자는 잘못된 목표를 쫓고 있습니다.

## 단단한 결합(Tight Coupling)이란 무엇입니까?

모의 객체가 필요하다는 것은 유닛 테스트를 위해 유닛을 분리해야 하며 이 때 유닛간의 결합이 있기 때문입니다.  긴밀한 결합은 코드를보다 단단하고 부서지기 쉽게 만듭니다. 코드가 변경되었을 때 프로그램이 멈추게 됩니다.  일반적으로 느슨한 결합은 코드를 확장하고 유지하기 쉽게 만듭니다. 여기에 더해 mocking에 대한 필요성을 없앰으로써 테스트를 더 쉽게한다는 사실은 두말할 필요도 없습니다.

이것으로부터 우리가 뭔가를 mocking 하고있다면 그 대신에 유닛간의 결합을 줄임으로써 코드를보다 유연하게 만들 수 있는 가능성이 존재한다는 것을 추론 할 수 있습니다.  그리하면 더 이상 가짜 코드들이 필요하지 않습니다.

커플링(또는 결합)이란 코드 유닛(모듈, 함수, 클래스 등)이 다른 코드 유닛에 종속되는 정도입니다.  밀접한 결합 또는 높은 결합도는 종속된 코드들이 변경 될 때 유닛이 망가질 가능성을 나타냅니다.  즉, 커플링이 빡빡할수록 응용 프로그램을 유지 관리하거나 확장하는 것이 어렵습니다.  느슨한 결합은 버그를 수정하고 응용 프로그램을 새로운 유스 케이스로 확장 할 때 마주치는 복잡성을 줄입니다.

커플링에는 여러 형태가 있습니다.

-   **하위 클래스 커플링 :**  하위 클래스는 상위 클래스의 구현 및 전체 계층 구조에 의존합니다. OO 디자인에서 사용할 수있는 가장 단단한 형태의 결합입니다.
-   **의존 코드 제어 :**  전달받은 메소드 이름으로 수행 할 작업을 지시하는 것처럼 종속된 유닛들을 제어하는 ​​코드.  API가 변경되면 종속된 코드가 중단됩니다.
-   **변경 가능한 상태 :**  다른 코드와 변경 가능한 상태를 공유하는 코드. (e.g.공유된 객체의 속성을 변경하는 일)  코드가 실행되는 상대적인 타이밍이 바뀔 경우 종속 코드가 손상 될 수 있습니다.  타이밍이 비 결정적이라면, 모든 종속 유닛을 완전히 뜯어보지 않는 이상 프로그램 정확성을 달성하는 것이 불가능할 수 있습니다. 예를 들어, 해결 불가능한 레이스 컨디션이 있을 수 있습니다.  한 버그를 수정하면 다른 종속 유닛에 새로운 버그가 나타날 수 있습니다.
-   **형태적 종속성 :**  다른 코드와 특정 데이터 구조를 공유하며 구조의 일부분만 사용하는 코드입니다.  구조적인의 형태가 변경되면 종속 코드가 손상 될 수 있습니다.
-   **이벤트 / 메시지 커플링 :**  메시지 전달, 이벤트 등을 통해 다른 유닛과 통신하는 코드.

## 무엇이 단단한 결합을 만드는가?

단단한 결합에는 많은 원인이 있습니다.

-   **변이**  vs  _불변성_
-   **부수효과**  vs  _순수함 /부수효과를 분리_
-   **책임 과부하**  vs  _Do One Thing (DOT)_
-   **절차를 기술**  vs  _구조를 기술_
-   **명령형 구성**  vs  _선언적인 구성_

명령형 및 객체 지향 코드는 함수형 코드보다 단단하게 결합되기 쉽습니다.  그러나  함수형 스타일로 프로그래밍한다고 해서 단단한 결합에 무적이 되진 않습니다. 다만 함수형 프로그래밍은 프로그램의 기본 구성 단위로 순수 함수를 사용하며 이는  본질적으로 느슨한 결합들을 형성합니다.

순수 함수 :

-   동일한 입력이 주어지면 항상 동일한 출력을 반환
-   부수효과 없음

순수 함수는 어떻게 결합도를 낮출까요?

-   **Immutability :**  순수 함수는 기존 값을 변경하지 않습니다.  대신 새로운 값을 반환합니다.
-   **No side effects :**  순수 함수가 유일하게 표현하는 것은 리턴 값입니다. 따라서 화면, DOM, 콘솔, 표준 입출력, 네트워크, 디스크 입출력 등과 같은 외부 상태와 연관있는 다른 함수의 조작을 방해할 가능성이 없습니다. 
-   **Do One Thing :**  순수 함수는 단일 임무를 수행합니다. 입력과 출력을 맵핑하는 임무입니다.  객체 및 클래스 기반 코드는 몇줄 안되는 코드에게 과한 책임을 지우는 경향이 있습니다. 책임 과부하를 피하십시오.
-   **Structure, not instructions :**  순수 함수는 안전하게 메모이제이션 할 수 있습니다. 즉, 시스템 메모리가 무한하다고 가정할 때  함수 입력을 인덱스로 사용하여 테이블에서 해당 값을 검색하는 조회 테이블로 바꿀 수 있습니다.  다시 말해 순수 함수는 컴퓨터에게 내리는 명령이 아닌 데이터 간의 구조적 관계를 설명하므로 서로 충돌하는 두 가지 명령이 서로의 발가락을 밟고 문제를 일으킬 수 없습니다.

## 합성은 mocking과 무슨 관계가 있습니까?

 **Everything**.  소프트웨어 개발의 본질은 큰 문제를 작고 독립적인 조각으로 분해하고 작은 솔루션들을 합쳐 큰 문제를 해결하는 과정입니다.

> 문제를 분해하는 전략이 실패했을 때 mocking이 필요해집니다.

큰 문제를 작은 부분으로 나누는 데 사용되는 조그만  유닛이 서로 의존 할 때 mocking이 필요합니다.  다시 말하자면  _우리가 생각한  원자 단위가 실제로 원자가 아니며,_  우리의 분해 전략이 큰 문제를 더 작고 독립적인 문제로 분해하지 못했을 때 필요합니다.

문제를 성공적으로 분해했다면 일반적인 합성 유틸리티들을 사용해 솔루션 조각들을 다시 합칠 수 있습니다.  예 :

-   **함수 합성**  e.g.,  `lodash/fp/compose`
-   **컴포넌트 합성**  e.g., 함수 합성으로 고차 컴포넌트 작성
-   **상태 저장소/모델 합성**  e.g.,  [Redux combineReducers](http://redux.js.org/docs/api/combineReducers.html)
-   **객체 또는 팩토리 합성** e.g. 객체 믹스인 또는 함수형 믹스인
-   **프로세스 합성**  e.g. 트랜스듀서^transducer^ ^[   [Understanding Transducers in JavaScript](https://medium.com/@roman01la/understanding-transducers-in-javascript-3500d3bd9624)를 참고 ]
-   **프로미스 또는 모나드 합성**  e.g., `asyncPipe()`  또는  `composeM()`,  `composeK()` Kleisli 합성 
-   기타..

이러한 유틸리티들을 사용하는 경우 합성의 재료가 되는 각 요소들은 다른 요소를  _mocking하지 않고도_  격리 된 유닛 테스트를 수행 할 수 있습니다  _._

합성 자체는 완벽하게 선언적이므로  _유닛 테스트_ 가 필요한 로직이 없습니다. (그리고 이런 합성 유틸리티는 자체적인 유닛 테스트가 있는 서드파티 라이브러리입니다).

이러한 상황에서 유닛 테스트를 하는 건 아무런 의미가 없습니다.  대신 통합 테스트가 필요합니다.

익숙한 예제를 가지고 명령적^Imperative^ 합성과 선언적^Declarative^ 합성을 비교해 보겠습니다.

```javascript
// Function composition OR  
// import pipe from 'lodash/fp/flow';  
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);

// Functions to compose  
const g = n => n + 1;  
const f = n => n * 2;

// Imperative composition  
const doStuffBadly = x => {  
  const afterG = g(x);  
  const afterF = f(afterG);  
  return afterF;  
};

// Declarative composition  
const doStuffBetter = pipe(g, f);

console.log(  
  doStuffBadly(20), // 42  
  doStuffBetter(20) // 42  
);
```

함수 합성은 함수의 반환 값에 다른 함수를 적용하는 프로세스입니다.  즉, 함수의 파이프 라인을 만든 다음 입력 값을 파이프 라인에 전달하면 물건이 공장 조립 라인을 통과하듯 값이 개별 함수들을 거쳐가며 변환됩니다.  결국, 파이프 라인의 마지막 함수는 최종 값을 반환합니다.

```
initialValue -> [g] -> [f] -> result
```

이는 패러다임과 무관하게 모든 주류 언어들이 애플리케이션 코드를 구성하는 주요 수단입니다.  자바조차도 클래스 인스턴스간 메세지를 전달하는 기본 메커니즘으로 함수 (메소드)를 사용합니다.

함수를 수동으로(명령형으로) 합성하거나 자동으로(선언적으로) 합성할 수 있습니다. 함수가 퍼스트 클래스가 아닌 언어에서는 선택권이 많지 않습니다. 명령형으로 할 수 밖에 없습니다.  자바 스크립트 (그리고 거의 모든 주요 대중 언어)에서는 선언적으로 합성할 수 있습니다.

명령형이란 컴퓨터에게 단계적으로 뭔가를 수행하도록 명령한다는 의미입니다. How-To 가이드입니다.  위의 예에서 명령형 스타일은 다음과 같습니다.

1.  인수를 취하여  `x` 를 대입하십시오.
2.  `afterG`  라는 바인딩을 선언하고 `g(x)` 의 결과를 할당하십시오.
3.  `afterF`  라는 바인딩을 선언하고  `f(afterG)`  의 결과를 할당합니다.
4.  `afterF`  의 값을 리턴합니다.

명령형 버전에는 테스트해야 할 로직들이 있습니다.  비록 단순한 과제일지라도 잘못된 변수를 전달하거나 리턴하는 곳에서 버그가 발생하는 것을 자주 보았습니다.

선언적 스타일은 우리가 사물 간의 관계를 컴퓨터에 알리는 것을 의미합니다. 그것은 등식을 사용해서 구조에 대해 설명하는 것 입니다.  선언적 스타일은 다음과 같습니다 :

-   `doStuffBetter` 는  `g` 와  `f` 의 파이프 컴포지션입니다.

이게 전부입니다.

`f`와  `g`에 대한 유닛 테스트가 존재하고  `pipe()`가 자체 유닛 테스트 (Ramda의  `pipe()`  또는 Lodash의 `flow()` 를 사용)를 사용한다고 가정하면 더이상 유닛 테스트를 해야될 로직이 없습니다.

이 스타일이 올바르게 작동하려면 유닛들이  _분리_ 되어야 합니다.

## 커플링을 제거하려면 어떻게 해야합니까?

커플링을 제거하려면 먼저 종속성이 어디서 발생하며 그 형태가 어떤지 알아야 합니다. 다음은 중요한 예들입니다. 그 순서는 대략적으로 커플링의 긴밀한 정도입니다.

단단한 결합 :

-   클래스 상속 (상속 계층이 증가할 때마다, 각 자손 클래스가 많아질 때 마다 결합도는 배가 됩니다)
-   전역 변수
-   변경 가능한 전역 상태 (브라우저 DOM, 공유 저장소, 네트워크 등)
-   부수효과가 있는 모듈 가져오기
-   합성 과정에 숨어든 암시적인 종속성  e.g., `const enhancedWidgetFactory = compose(eventEmitter, widgetFactory, enhancements);`  `widgetFactory`는 `eventEmitter`에게 의존합니다.
-   의존성 주입 컨테이너
-   의존성 주입 파라미터
-   컨트롤 파라미터 (외부 유닛이 해당 유닛의행동을 지시하고 통제하는 것)
-   변경 가능한 매개 변수

느슨한 결합:

-   부수효과가 없는 모듈 가져오기 (블랙 박스 테스트를 할 때 import된 모든 모듈을 분리할 필요는 없음)
-   메시지 전달 / pubsub
-   변경 불가능한 매개 변수 

아이러니하게도, 커플링을 발생시키는 원인의 대부분은 원래 커플링을 줄이기 위해 설계된 메커니즘들입니다. 왜냐하면 문제를 풀기 위한 작은 솔루션들을 완전한 애플리케이션으로 재구성하기 위해 어떻게든 이들을 통합하고 연결해야 했기 때문입니다.  좋은 방법과 나쁜 방법이 있을 뿐입니다.  단단한 커플링을 유발하는 원인들이 실제로 그렇게 되는 경우에는 피해야 합니다.  느슨한 커플링 옵션은 일반적으로 건강한 앱을 만드는 바람직한 방법입니다.

많은 책과 블로그 포스트에서 의존성 주입 컨테이너와 매개 변수를 "느슨한 결합"으로 분류합니다. 왜 이들이 "단단한 커플링" 그룹에 속하는지 혼란스러울 수 있습니다.  커플링은 바이너리가 아닙니다.  그라디언트 스케일입니다.  즉, 어떤 그룹에든 다소 주관적이고 임의적으로 속할 수 있습니다.

따라서 제가 간단하고 객관적인 리트머스 테스트를 준비했습니다.

의존성을 mocking하지 않고 유닛을 테스트 할 수 있습니까?  그렇게 할 수 없다면 mocking은 의존성과  _밀접하게 결합_  되어 있는 것입니다.

유닛의 종속성이 높을수록 문제가 되는 커플링이 발생할 가능성이 커집니다. 커플링이 어디서 발생하는지 이해했습니다. 이제 어떻게 해야 하는지 알아보겠습니다.

1.  클래스, 명령형 프로시저 또는 무언가를 변경하는 함수 대신 **순수 함수**를 프로그램의 원자 단위로 사용하십시오.
2.  프로그램 로직과  **부수효과**를  **격리**하십시오.  즉, I/O (네트워크, 디스크 I/O, UI 렌더링, 로깅 등)와 로직을 섞지 마십시오.
3.  독립적인 구성 요소에서  **종속성을 만드는 로직**을  **제거**하여 유닛 테스트가 필요없는 선언적 구성이 될 수 있도록 하십시오.  로직이 없다면 단위 테스트에 아무런 의미가 없습니다.

즉, 네트워크 요청을 설정하고 핸들러를 요청하는 코드는 유닛 테스트가 필요하지 않습니다. 대신 통합 테스트를 하십시오.

> _I/O를 유닛 테스트 하지 마십시오._ 

> _I/O는 통합을 위한 것입니다._  _통합 테스트를 하십시오._

통합 테스트를 위해 mocking하고 가짜를 만드는 것은 전혀 문제되지 않습니다.

## 순수 함수 사용

순수 함수를 사용하기 위해선 약간의 연습이 필요하며, 그러한 연습 없이는 원하는 작업을 수행하기 위해 코드를 작성하는 방법이 명확하지 않을 수 있습니다. 순수 함수는 전역 변수, 전달 된 인수, 네트워크, 디스크 또는 화면을 직접 변경할 수 없습니다. 오로지 값을 반환하는 일만 할 수 있습니다.

순수 함수가 배열이나 객체를 전달받아 해당 객체의 변경된 버전을 반환는 경우 객체를 변경하고 리턴할 수 없습니다. 객체의 새 사본을 만들어 변경사항을 적용하고 리턴해야 합니다. 배열  [접근 메소드](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype)  (변경 메소드가 아님),  `Object.assign()`, 빈 객체를 생성, 배열 또는 객체 스프레드 구문을 사용하여 이를 수행 할 수 있습니다.  예 :

```javascript
// Not pure  
const signInUser = user => user.isSignedIn = true;

const foo = {  
  name: 'Foo',  
  isSignedIn: false  
};

// Foo was mutated  
console.log(  
  signInUser(foo), // true  
  foo              // { name: "Foo", isSignedIn: true }  
);
```

vs...

```javascript
// Pure  
const signInUser = user => ({...user, isSignedIn: true });

const foo = {  
  name: 'Foo',  
  isSignedIn: false  
};

// Foo was not mutated  
console.log(  
  signInUser(foo), // { name: "Foo", isSignedIn: true }  
  foo              // { name: "Foo", isSignedIn: false }  
);
```

또는 [Mori](http://swannodette.github.io/mori/),  [Immutable.js](https://facebook.github.io/immutable-js/)와 같은 불변 데이터 타입 라이브러리를 사용하는 방법도 있습니다.  언젠가 JavaScript가 Clojure와 비슷한 불변 데이터 타입을 지원해주기를 바라고 있습니다. 

기존 객체를 재사용하는 대신 새 객체를 생성하기 때문에 성능이 저하될 수 있다고 생각할 수도 있지만, 여기엔 사실 좋은 부수효과 있습니다. 객체의 변경여부를 항등연산자로 (  `===`  ) 확인할 수 있습니다. 즉, 우리는 하나라도 변경된 것을 발견하기 위해 객체 내부를 구석구석 확인하지 않아도 됩니다. 

이 방법을 사용하면 복잡한 상태 트리가 있는 React 구성 요소를 더 빨리 렌더링할 수 있습니다. 각 렌더 패스 별로 심도있게 순회할 필요가 없어지기 때문입니다. `PureComponent`를 상속하여 얕은 `prop` 및 `state` 비교로  `shouldComponentUpdate()`를 구현할 수 있게됩니다.  객체의 주소가 변경되지 않았음을 탐지하면 상태 트리의 해당 부분에서 아무 것도 변하지 않았으며 트리를 모두 순회하지 않고도 계속 진행하면 된다는 것을 알 수 있습니다.

순수 함수는 메모이제이션 할 수 있습니다. 이전에 동일한 입력을 처리한적이 있다면 전체 객체를 다시 만들 필요가 없습니다. 미리 계산 된 값을 테이블에 저장하는 방식으로 계산 복잡도를 메모리와 교환할 수 있습니다. 많은 메모리가 필요하지 않고 계산적으로 비싼 프로세스의 경우, 이는 훌륭한 최적화 전략이 될 수 있습니다.

순수 함수는 또한 부수효과가 없기 때문에 **분할-정복** 전략을 사용하여 분산처리시스템에게 계산을 맡기는 것이 안전합니다.  이 전략은 원래 그래픽 용으로 설계된  GPU를 사용하여 이미지, 비디오 또는 오디오 프레임을 처리하는 데 주로 사용되지만 시뮬레이션, 과학실험 컴퓨팅과 같은 다른 용도로 많이 사용됩니다.

다시 말해, 값을 변경하는 방식이 항상 빠르지는 않으며 매크로 최적화를 희생하여 마이크로 최적화를 수행하기 때문에 더 느린 경우가 많습니다.

## 프로그램의 주요 로직과 부수효과를 분리

부수효과와 나머지 프로그램 로직을 분리하는데 도움이 되는 몇 가지 전략이 있습니다.  다음은 그 중 일부입니다.

1.  pub/sub 패턴을 사용하여 뷰와 프로그램 로직에서 I/O를 분리하십시오.  UI 뷰와 프로그램 로직에서 부수효과를 발생시키는 코드를 직접 호출하는 대신 이벤트 또는 액션 객체를 보내 통신하는게 좋습니다.
2.  I/O에서 로직을 분리하십시오. e.g.,  `asyncPipe()`를 사용해 프로미스를 리턴하는 함수를 합성
3.  I/O 코드에서 직접 계산 하지 않고 지연된 계산을 표현하는 객체를 사용하십시오. e.g.,  [redux-saga](https://github.com/redux-saga/redux-saga)의  `call()`  은 실제로 함수를 호출하지 않습니다.  대신, 함수와 인수에 대한 레퍼런스가 담긴 객체를 반환하며, saga 미들웨어가 이를 호출합니다.  따라서  `call()`과 그것을 사용하는 모든 함수가  _순수 함수가되고_, 쉽게 유닛 테스트를 만들 수 있으며  _mocking이 필요하지 않습니다._

### pub / sub 사용

Pub/sub은 게시^Publish^/구독^Subscribe^ 패턴의 약자입니다.  이 패턴에서 유닛은 서로 직접 호출하지 않습니다.  대신, 다른 유닛(구독자)가 들을 수있는 메시지를 게시합니다.  게시자는 어떤 유닛이 자신을 구독할지 알지 못하며 구독자는 게시자가 게시할 내용을 알지 못합니다.

Pub/sub의 좋은 예로 DOM(Document Object Model)이 있습니다.  애플리케이션의 모든 컴포넌트는 마우스 이동, 클릭, 스크롤 이벤트, 키 입력 등과 같이 DOM 요소에서 전달 된 이벤트를 수신 할 수 있습니다.  모든 사람들이 jQuery로 웹 애플리케이션을 만들던 시절, jQuery의 커스텀 이벤트는 DOM을 pub/sub 이벤트 버스로 사용하여 렌더링과 로직을 분리했습니다.

Pub/sub는 또한 Redux에서 볼 수 있습니다.  Redux에서는 애플리케이션 상태에 대한 전역 모델(저장소라고 함)을 만듭니다. 직접 모델을 조작하는 대신 뷰 및 I/O 핸들러는 액션 객체를 저장소에 보냅니다(dispatch라고 표현합니다).  액션 객체가 가지고 있는 `type`이라는 특별한 키를 다양한 reducers들이 듣고 응답할 수 있습니다. 또한 Redux는 특정 액션 타입을 수신하고 응답 할 수있는 미들웨어를 지원합니다.  이렇게하면 뷰에서 애플리케이션 상태가 처리되는 방법을 알 필요가 없으며 상태 로직이 뷰에 대해 알 필요가 없습니다.

덕분에 디스패처를 간단하게 수정하는 것 만으로 미들웨어를 통해 액션 로깅/분석, 저장소 또는 서버와의 상태 동기화, 서버 및 네트워크 피어와의 실시간 통신 기능 패치와 같은 복잡한 문제들을 다룰 수 있습니다.

### I/O와 로직을 분리

모나드(e.g., 프로미스) 합성을 ​​사용해 프로그램의 의존 로직을 제거할 수 있습니다. 예를 들어 다음 함수의 로직은 모든 비동기 함수를 mocking하지 않고서는 유닛 테스트를 할 수 없게 설계되어있습니다.

```javascript
async function uploadFiles({user, folder, files}) {  
  const dbUser = await readUser(user);  
  const folderInfo = await getFolderInfo(folder);  
  if (await haveWriteAccess({dbUser, folderInfo})) {  
    return uploadToFolder({dbUser, folderInfo, files });  
  } else {  
    throw new Error("No write access to that folder");  
  }  
}
```

이를 실행할 수 있도록 도와주는 의사 코드를 작성해 보겠습니다.

```javascript
const log = (...args) => console.log(...args);

// Ignore these. In your real code you'd import  
// the real things.  
const readUser = () => Promise.resolve(true);  
const getFolderInfo = () => Promise.resolve(true);  
const haveWriteAccess = () => Promise.resolve(true);  
const uploadToFolder = () => Promise.resolve('Success!');

// gibberish starting variables  
const user = '123';  
const folder = '456';  
const files = ['a', 'b', 'c'];

async function uploadFiles({user, folder, files}) {  
  const dbUser = await readUser({ user });  
  const folderInfo = await getFolderInfo({ folder });  
  if (await haveWriteAccess({dbUser, folderInfo})) {  
    return uploadToFolder({dbUser, folderInfo, files });  
  } else {  
    throw new Error("No write access to that folder");  
  }  
}

uploadFiles({user, folder, files})  
  .then(log)  
;
```

`asyncPipe()`로 프로미스를 합성할 수 있도록 리팩터링합니다.

```javascript
const asyncPipe = (...fns) => x => (  
  fns.reduce(async (y, f) => f(await y), x)  
);

const uploadFiles = asyncPipe(  
  readUser,  
  getFolderInfo,  
  haveWriteAccess,  
  uploadToFolder  
);

uploadFiles({user, folder, files})  
  .then(log)  
;
```

프로미스는 자체적으로 조건부 분기하도록 설계되어있기 때문에 `if` 로직을 쉽게 제거 할 수 있습니다.  로직과 I/O는 잘 섞이지 않기 때문에 I/O 의존적인 코드에서 로직을 제거하는 것이 좋습니다.

이러한 종류의 합성 작업을 하기 위해서는 다음 두 가지를 보장해야합니다.

1.  `haveWriteAccess()`는 사용자가 쓰기 권한이 없는 경우 거절해야 합니다.  조건 로직을 프로미스 컨텍스트로 옮겼기에 따로 유닛 테스트를 할 필요가 없습니다.(JS 엔진 코드에 자체 테스트가 생성됩니다).
2.  각 함수는 동일한 데이터 타입을 사용하여 처리됩니다.  우리는 `{ user, folder, files, dbUser?, folderInfo? }`를 키로 사용하는 `pipelineData`  타입을 만든 것 입니다.  이렇게 하면 각 구성 요소들이 특정 구조를 공유한다는 종속성이 생기지만 이러한 함수를 추상화시켜 보다 일반적인 버전을 만들 수 있으며 간단하게 래핑하여 다양한 파이프라인에 적용할 수 있습니다.

이러한 조건들이 충족되면 다른 함수들을 mocking하지 않고 각 함수를 서로 격리하여 테스트하기 쉬워집니다. 우리가 파이프라인에서 모든 로직을 추출했으므로,이 파일에서 유닛 테스트를 하는 것은 아무런 의미가 없습니다.  결국 테스트가 필요해지는 것은 통합 뿐입니다.

> 기억하세요:   _로직과  I/O는 별도의 관심사입니다._
> _로직이란 생각하는 것입니다._  _효과란 행동입니다._  _행동하기 전에 생각해야 합니다!_

### 미래의 계산을 나타내는 객체 사용

redux-saga의 전략은 미래의 계산을 나타내는 객체를 사용하는 것입니다.  이 아이디어는 모나드를 반환하는 것과 유사합니다. 단, 항상 모나드가 반환되는 것은 아닙니다.  모나드는 체이닝으로 함수를 합성할 수 있지만, 대신 명령형 코드를 사용하여 함수를 수동으로 연결할 수 있습니다.  다음은 redux-saga가 어떻게 동작하는지에 대한 대략적인 스케치입니다.

```javascript
// sugar for console.log we'll use later  
const log = msg => console.log(msg);

const call = (fn, ...args) => ({ fn, args });  
const put = (msg) => ({ msg });

// imported from I/O API  
const sendMessage = msg => Promise.resolve('some response');

// imported from state handler/Reducer  
const handleResponse = response => ({  
  type: 'RECEIVED_RESPONSE',  
  payload: response  
});

const handleError = err => ({  
  type: 'IO_ERROR',  
  payload: err  
});  
  

function* sendMessageSaga (msg) {  
  try {  
    const response = yield call(sendMessage, msg);  
    yield put(handleResponse(response));  
  } catch (err) {  
    yield put(handleError(err));  
  }  
}
```

네트워크 API를 mocking하거나 부수효과를 일으키지 않고도 모든 호출을 볼 수 있습니다.  보너스 : 비 결정적인 네트워크 상태 등을 걱정하지 않고도 애플리케이션을 매우 쉽게 디버깅 할 수 있습니다.

네트워크 오류를 시뮬레이션하고 싶습니까?  
간단하게 `iter.throw(NetworkError)`를 호출하면 됩니다.

다른 곳에서는 일부 라이브러리 미들웨어가 함수를 작동시키고 실제로 프로덕션 애플리케이션에서 부수효과를 발생시킵니다.

```javascript
const iter = sendMessageSaga('Hello, world!');

// Returns an object representing the status and value:  
const step1 = iter.next();

log(step1);  
/* =>  
{  
  done: false,  
  value: {  
    fn: sendMessage  
    args: ["Hello, world!"]  
  }  
}  
*/
```

 `call()`  객체를 해체하여 yield하는 방식으로 미래의 계산을 검사하거나 호출합니다.

```javascript
const { value: {fn, args }} = step1;
```

실제로는 미들웨어에서 실행되며 테스트 및 디버깅 할 때는 이 부분을 건너뛸 수 있습니다.

```javascript
const step2 = fn(args);

step2.then(log); // "some response"
```

API 또는 http 호출을 mocking하지 않고 네트워크 응답을 시뮬레이션 하려면  `.next()`에 가짜 리스폰스를 전달하면 됩니다.

```javascript
iter.next (simulatedNetworkResponse); 
```

결국  `done`이  `true`가 될 때까지  `.next()`를  계속 호출하면 됩니다.

유닛 테스트에서 제너레이터와 계산 표현을 사용해 모든 것을 **부수효과 없이** 시뮬레이션할 수 있습니다.  값을  `.next()`에 전달해 가짜 응답을 만들거나 에러를 이터레이터로 전달해 가짜 에러상황 및 거절된 프로미스를 시뮬레이션할 수 있습니다.

결국, 부수효과가 많은 복잡한 통합 워크 플로의 경우에서도 mocking을 할 필요가 없어집니다.

## "코드 냄새"는 법칙이 아닌 경고 신호입니다.  Mock은 Evil하지 않습니다.

더 나은 아키텍처를 사용할 수 있음에도 실제로는 다른 사람들의 API를 사용하고 레거시 코드와 통합해야 합니다. 이들 중에는 순수하지 않은 코드들이 많이 있습니다.  이러한 경우에 테스트 더블을 격리시켜 사용해야 합니다. 예를 들어, express는 컨티뉴에이션 패싱 기법으로 공유 가변 상태^shared^ ^mutable^ ^state^ 및 모델의 부수효과들을 전달합니다.

일반적인 예를 살펴 보겠습니다.  사람들은 익스프레스 서버 스크립트에 의존성 주입을 해야한다고 말합니다. 그렇지 않다면 어떻게 익스프레스 앱에 들어가는 것들을 테스트 할 수 있는지 궁금해합니다.  예 :

```javascript
const express = require('express');  
const app = express();

app.get('/', function (req, res) {  
  res.send('Hello World!')  
});

app.listen(3000, function () {  
  console.log('Example app listening on port 3000!')  
});
```

_이 파일_  을 "유닛 테스트"하려면,  우리는 의존성 주입 솔루션을 개발한 다음 모든 것을 mocks으로 (아마도  `express()`  자체도 포함해서) 전달해야 할 것입니다.  그러나 이 파일이 매우 복잡해질 경우, 즉 여러 리퀘스트 핸들러들이 서로 다른 익스프레스 기능을 사용하고 있고 다양한 로직들에 의존하고 있다면 아마도 꽤 정교한 mock들을 만들어야 할 것입니다.  저는 개발자들이 익스프레스 인스턴스, 세션 미들웨어, 로그 처리기, 실시간 네트워크 프로토콜 등과 같은 정교한 가짜와 모의 객체를 생성하는 것을 자주 봤습니다.  어떻게 해야 할까요? 대답은 간단합니다.

> 이 파일은 유닛 테스트하면 안됩니다.

익스프레스 앱의 서버 정의 파일은 말 그대로 앱의 주요  **통합**  지점입니다. 이 파일을 테스트한다는 것은 프로그램 로직, 익스프레스 및 앱의 모든 핸들러 간의 통합을 테스트하는 것입니다.  100% 유닛 테스트 커버리지를 달성할 수 있다고 하더라도 통합 테스트를 건너 뛰면 **절대로** 안됩니다.

이 파일을 유닛 테스트하려고 하는 대신 프로그램 로직을 별도의 유닛로 분리하고 유닛 테스트를 하십시오. 서버 파일은 실제 통합 테스트를 해야 합니다. 다시 말해 실제로 네트워크에 연결하고 실제 HTTP 메시지를 주고받고,  [supertest](https://github.com/visionmedia/supertest)와 같은 도구를 사용해 헤더를 테스트하십시오.

Hello World Express 예제를 더 쉽게 테스트 할 수 있도록 리팩터링 해 봅시다.

`hello`  핸들러를 자체 파일로 분리해 유닛 테스트를 만들어야 합니다.  앱의 나머지 구성 요소를 mocking할 필요가 없습니다.  이는 분명히 순수 함수가 아니므로  `.send()`  호출하도록 응답 객체를 mocking해야 합니다.

```javascript
  const hello = (req, res) => res.send ( 'Hello World!'); 
```

이런 식으로 테스트 해 볼 수 있습니다 :

```javascript
{  
  const expected = 'Hello World!';  
  const msg = `should call .send() with ${ expected }`;

  const res = {  
    send: (actual) => {  
      if (actual !== expected) {  
        throw new Error(`NOT OK ${ msg }`);  
      }  
      console.log(`OK: ${ msg }`);  
    }  
  }

  hello({}, res);  
}
```

Listen 핸들러를 자체 파일로 분리해 유닛 테스트를 만들겠습니다.  이 때도 같은 문제가 등장합니다.  익스프레스 핸들러들은 순수하지 않으므로 로거를 집어넣어 스파이웨어가 호출되는지 확인해야합니다.  테스트는 이전 예와 비슷합니다.

```javascript
const handleListen = 
       (log, port) => 
                () => 
        log(`Example app listening on port ${ port }!`);
```

이제 서버 파일엔 통합 로직만 남겨졌습니다.

```javascript
const express = require('express');

const hello = require('./hello.js');  
const handleListen = require('./handleListen');  
const log = require('./log');

const port = 3000;  
const app = express();

app.get('/', hello);

app.listen(port, handleListen(port, log));
```

여전히 통합 테스트는 필요하지만 유닛 테스트를 아무리 추가해도 케이스 커버리지는 향상되지 않습니다.  우리는 logger를  `handleListen()`에 전달하기 위해 최소한의 의존성 주입 패턴을 사용하지만 의존성 주입 프레임 워크가 필요하진 않습니다.

## 모의(mocking) 테스트는 통합 테스트에 적합합니다.

통합 테스트는 유닛간의 상호작용과 통합을 테스트하기 때문에 CPU 클러스터 또는 네트워크상의 별도의 프로세스에 존재하는 다른 유닛과의 통신 중에 발생할 수있는 다양한 조건과 상황을 재현해야 합니다.  서버나 네트워크 프로토콜, 네트워크 메시지 등을 가짜로 만드는 것은 너무나 당연합니다.

때로는 유닛이 타사 API와 통신하는 것을 테스트해야 하며 때로는 이러한 API가 실제로 테스트하려면 엄청나게 비쌉니다.  실제 서비스에 대한 워크 플로우 트랜잭션을 기록한 후 fake 서버에서 이를 다시 재생하면 유닛이 별도의 네트워크 프로세스에서 실행중인 타사 서비스와 얼마나 잘 통합되어 있는지 테스트할 수 있습니다.  "이 메시지 헤더가 올바른 형식입니까?"같은 상황을 테스트하는 가장 좋은 방법입니다.

네트워크 대역폭을 제한하고, 네트워크 지연을 연출하고, 네트워크 오류를 생성하고, 통신 레이어를 mocking하는 등 유닛 테스트가 할 수 없는 많은 다른 조건을 테스트하는 통합 테스트 도구들이 많이 있습니다.

통합 테스트없이 100% 케이스 커버리지를 달성하는 것은 불가능합니다.  100% 유닛 테스트 커버리지를 달성하더라도 이를 건너 뛰면 안됩니다. 100%가 100%가 아닙니다.


[**다음: 객체 합성이라는 숨겨진 보물 >**](https://midojeong.github.io/2018/04/23/the-hidden-treasures-of-object-composition/)