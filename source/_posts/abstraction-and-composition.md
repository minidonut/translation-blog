---
title: 합성과 추상화
catalog: true
date: 2018-04-15 14:34:15
subtitle: Abstraction & Composition
header-img: "bg.jpg"
readingTime: 6
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 소프트웨어 개발자로서 성숙해질수록 기본적인 것들에 더 큰 가치를 부여하게 됩니다. 초보자였을 때 사소해 보였던 깨달음들이 그동안의 경험들과 함께 심오한 의미를 갖게됩니다. 구글 사전에 따르면 추상화란  “그 연관성, 속성 또는 구체적인 사례와는 독립적인 무언가를 추려내는 과정” 입니다. abstraction은 라틴어 동사  _abstrahere_ 에서 왔습니다. 이는 없애 버리다(to draw away)라는 의미 입니다.  전 이러한 깨달음을 좋아합니다.  추상화는 사물을 제거하는 것입니다. 그러나 무엇을 제거하고 무엇을 남겨야 합니까?
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/abstraction-composition-cb2849d5bdd6)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)  |  [<< Part 1에서 다시 시작](https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea)  |  [다음>](https://medium.com/javascript-scene/reduce-composing-software-fe22f0c39a1d)

소프트웨어 개발자로서 성숙해질수록 기본적인 것들에 더 큰 가치를 부여하게 됩니다. 초보자였을 때 사소해 보였던 깨달음들이 그동안의 경험들과 함께 심오한 의미를 갖게됩니다.

> "가라데라는 무술에서 [...] 검은띠에 대한 자존심의 상징은 검은 염료가 희게 퇴색할 정도로 길게 입으라는 것 처럼 다시 초심으로 돌아가라는 의미 입니다."~ John Maeda,  ["The Laws of Simplicity (Simplicity: Design, Technology, Business, Life)"](https://www.amazon.com/Laws-Simplicity-Design-Technology-Business/dp/0262134721/ref%3Das_li_ss_tl%3Fie%3DUTF8%26qid%3D1516330765%26sr%3D8-1%26keywords%3Dthe%2Blaws%2Bof%2Bsimplicity%26linkCode%3Dll1%26tag%3Deejs-20%26linkId%3D287b1d3357fa799ce7563584e098c5d8)

구글 사전에 따르면 추상화란  “그 연관성, 속성 또는 구체적인 사례와는 독립적인 무언가를 추려내는 과정” 입니다.

 abstraction은 라틴어 동사  _abstrahere_ 에서 왔습니다. 이는 없애 버리다(to draw away)라는 의미 입니다.  전 이러한 깨달음을 좋아합니다.  추상화는 사물을 제거하는 것입니다. 그러나 무엇을 제거하고 무엇을 남겨야 합니까?

때로는 단어를 다른 언어로 번역 한 다음 영어로 다시 번역하여 우리가 일반적으로 생각하지 않는 다른 연관성을 느끼는 것을 좋아합니다.  "추상화"를 이디시어로 번역하면 그 결과는 "absentminded(무관심한, 넋이 나간)"입니다.  전 이것도 좋아합니다.  무관심한 사람은 자동 조종 장치로 달리고 있는 것이나 마찬가지입니다. 하고있는 일에 대해 적극적으로 생각하지 않고 ... 그냥 하고 있습니다.

추상화를 통해 우리는 자동 조종 장치로 안전하게 달릴 수 있습니다.  모든 소프트웨어는 자동화입니다.  컴퓨터로 할 수 있는 모든 일들은 충분한 시간이 주어지면  종이, 잉크 그리고  비둘기로 할 수 있습니다.  소프트웨어는 수동으로 수행하려면 많은 시간이 소요될 수 있는 작은 세부 사항까지 모두 처리합니다.

모든 소프트웨어는 추상화의 결과물입니다. 우리가 이러한 혜택을 누리는 동안 모든 힘든 작업들과 세부 사항들을 숨기고 있습니다.

소프트웨어 프로세스는 반복적입니다.  문제를 분석하는 단계에서 반복되는 내용을 처음부터 다시 구현하려면 불필요한 작업을 많이 해야 합니다. 바보같은 짓이며 비실용적입니다.

대신에 우리는 여러 종류의 구성 요소(함수, 모듈, 클래스 등)를 만들고 이름 (ID)을 부여하고 원하는 만큼 여러번 재사용함으로써 중복을 제거합니다.

분해 과정은 추상화 과정입니다.  성공적인 추상화의 결과물은 개별적인 쓰임새가 있는 유용하고 재구성 가능한 요소들의 집합입니다.  이로부터 우리는 소프트웨어 아키텍처의 중요한 원칙을 얻게되었습니다.

소프트웨어 솔루션은 내부 구성 요소의 구현 세부 사항을 변경하지 않고도 해당 구성 요소로 분해 할 수 있어야 하며 새로운 솔루션으로 재구성 가능해야합니다.

## 추상화는 단순화 작업입니다.

> "단순함이란 명백한 부분을 제거하고 의미있는 부분을 남기는 것 입니다."- John Maeda,  ["The Laws of Simplicity (Simplicity: Design, Technology, Business, Life)"](https://www.amazon.com/Laws-Simplicity-Design-Technology-Business/dp/0262134721/ref%3Das_li_ss_tl%3Fie%3DUTF8%26qid%3D1516330765%26sr%3D8-1%26keywords%3Dthe%2Blaws%2Bof%2Bsimplicity%26linkCode%3Dll1%26tag%3Deejs-20%26linkId%3D287b1d3357fa799ce7563584e098c5d8)

추상화 프로세스에는 두 가지 요소가 있습니다.

-   **일반화**^Generalization^ 는 반복되는 패턴에서  _유사점_  (명백함)을 찾고 추상화 뒤에 이를 숨기는 과정입니다.
-   **전문화**^Specialization^  는 각 유스케이스별로  무엇이 근본적으로 다른지  찾아 이를 남기는 과정입니다.

추상화란 어떤 개념의 에센스를 추출하는 과정입니다.  서로 다른 영역의 서로 다른 문제들 사이의 공통점을 탐구함으로써 우리의 정신은 잠시 바깥으로 나가 다른 관점에서 문제를 바라보는 방법을 배웁니다.  문제의 본질을 파악해서 나온 좋은 해결책은 다른 많은 문제에 적용할 수 있다는 것을 알게됩니다.  솔루션을 잘 코딩하면 소프트웨어의 복잡성을 근본적으로 줄일 수 있습니다.

> "깊은 깨달음으로 한 가지를 만지면 모든 것을 만질 수 있습니다."~ 팃낙한

이 원칙은 애플리케이션을 빌드하기 위해 필요한 코드를 크게 줄이는데 사용할 수 있습니다.

## 소프트웨어 추상화

소프트웨어 추상화는 다음과 같은 다양한 형태를 취합니다.

-   알고리즘^Algorithm^
-   자료구조^Data^ ^Structure^
-   모듈^Module^
-   클래스^Class^
-   프레임 워크^Framework^

그리고 제 개인적 취향인 :

> "때때로 우아한 구현은 단지 함수뿐입니다.  메소드가 아닙니다. 클래스도 아닙니다. 프레임워크도 아니고 그저 함수입니다."~ John Carmack

함수는 추상화에 필요한 훌륭한 특징들을 가지고 있습니다.

-   **Identity**  - 이름을 붙이고 다른 맥락에서 이를 다시 사용할 수 있는 특징입니다.
-   **Composition**  -간단한 함수들을 합성해 고차 함수를 만들 수 있습니다.

## 합성을 통한 추상화

소프트웨어를 추상화할 때 가장 유용한 함수는 수학 함수의 모듈러 특성을 공유하는  _순수 함수_  입니다.  수학의 함수는 동일한 입력을 받았을 때 항상 동일한 출력을 반환합니다.  함수를 입력과 출력 사이의 관계로 볼 수 있습니다.  어떤 입력  `A`가  주어지면 함수  `f`  는  `B`를 출력합니다. 이 때  `f`  가  `A`  와  `B` 사이의  관계를 정의한다고 말할 수 있습니다.

```
f: A -> B
```

마찬가지로  `B`  와  `C`  사이의 관계를 정의하는 또 다른 함수  `g`  가 있을 때,

```
g: B -> C
```

이는  `A`  에서  `C`로의 직접적인 관계를 정의하는 또 다른 함수  `h`가 있음을 의미합니다.

```
h: A -> C
```


이러한 관계는 문제공간의 구조를 형성하며 함수를 합성하는 방법이 애플리케이션의 구조를 결정합니다.

좋은 추상화는 내부 구조를 숨기며 단순화합니다. 마치 `h` 함수가`A -> B -> C`를  `A -> C` 로 단순화하는 것과 비슷합니다.

![](https://cdn-images-1.medium.com/max/1600/1*uFTKDgI0kT878E97K14V1A.png)

## 적은 코드로 더 많은 작업을 수행하는 방법

추상화는 적은 코드로 더 많은 일을 할 수있는 열쇠입니다.  예를 들어, 단순히 두 개의 숫자를 더하는 함수가 있다고 가정 해보겠습니다.

```javascript
  const add = (a, b) => a + b; 
```

그러나 이를 반복적으로 사용하는 패턴이 있을 때 숫자하나를 고정시켜주는게 좋습니다.

```javascript
  const a = add(1, 1);   
  const b = add(a, 1);   
  const c = add(b, 1);   
  // ... 
```

우리는 add 함수를 커링할 수 있습니다.

```javascript
  const add = a => b => a + b; 
```

그리고 나서 첫 번째 인수를 받아 새 함수를 반환하는 프로그램을 만듭니다.

```javascript
  const inc = add(1); 
```

이제는  `1` 씩 더해야 할 때  `add`  대신  `inc`를 사용할 수 있으므로 필요한 코드의 양이 줄어들었습니다.

```javascript
  const a = inc(1);   
  const b = inc(a);   
  const c = inc(b);   
  // ... 
```

이 경우  `inc`  는 add의  특수한  버전일 뿐입니다.  모든 커링은 추상화입니다.  사실, 모든 고차 함수는 하나 이상의 인수를 전달하여 특화시킬 수 있는 일반화입니다.

예를 들어,  `Array.prototype.map()`은 배열의 각 요소에 함수를 적용하는 아이디어를 추상화한 고차 함수입니다.  보다 명확하게하기 위해  `map`을 커리된 함수로 작성해 보겠습니다.

```javascript
  const map = f => arr => arr.map(f); 
```

이 버전의  `map`은 특정 함수를 취한 다음 처리 할 배열을 인수로 받는 함수를 리턴합니다.

```javascript
const f = n => n * 2;

const doubleAll = map(f);  
const doubled = doubleAll([1, 2, 3]);  
// => [2, 4, 6]
```

`doubleAll`의 코드가 얼마나 짧은지 보입니까 ? `map(f)`  - 이게 전부입니다!  이것이 전체 정의입니다.  추상화를 기본 요소로 삼으면 새로운 코드를 거의 만들지 않고도 매우 복잡한 기능들을 구현할 수 있습니다.

## 결론

소프트웨어 개발자는 모든 시간을 추상화하는데 사용합니다. 그럼에도 추상화 또는 합성에 대한 기본적인 이해가 거의 없는 경우가 많습니다.

어떤 것을 추상화할 때는 충분한 시간을 들여 생각해야 합니다. 그리고 `map`  ,  `filter`  및  `reduce`  와 같은 유용한 추상화들을 알고 있어야합니다.  마지막으로 좋은 추상화의 특징을 알아보는 법을 배워야 합니다.

-   단순한^Simple^
-   간결한^Concise^
-   재사용 가능한^Reusable^
-   독립적인^Independent^
-   분해가능한^Decomposable^
-   재구성가능한^Recomposable^