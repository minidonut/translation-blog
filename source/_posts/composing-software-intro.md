---
title: "소프트웨어 합성: 개요"
catalog: true
date: 2018-03-16 14:13:44
subtitle: "composing software: introduction"
header-img: "bg.jpg"
readingTime: 12
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 첫 고등학교 프로그래밍 수업 시간에 소프트웨어 개발이란 복잡한 문제를 작은 문제들로 분해하고 작은 문제들의 해법들을 다시 조합해 복잡한 문제를 해결 할 수 있는 솔루션을 만드는 행위라고 배웠습니다. 전 그 말의 중요성을 너무 늦게 깨달았다는 것이 매우 후회됩니다. 너무 늦게 소프트웨어 설계의 본질을 깨달은 것 입니다. 수백 명의 개발자들을 인터뷰하며 나만 그런 것이 아님을 알게됐습니다.  아주 소수의 개발자만이 소프트웨어 개발의 본질에 대해 잘 알고 있었습니다.  대부분의 경우에는 우리가 활용해야 하는 가장 중요한 개념과 그것을 잘 적용하는 방법을 몰랐습니다.  다음은 소프트웨어 개발에서 가장 중요한 두가지 질문입니다.
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한  것입니다.  [[원문보기]](https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea)
> 
![main image](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)
*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈에 대한 소개입니다.  앞으로 계속하여 연재될 것입니다.
[_다음편 >_](https://midojeong.github.io/2018/03/20/the-rise-and-fall-and-rise-of-functional-programming-composable-software/)


> 합성 : 전체를 구성하기 위해 부분이나 요소를 결합하는 행위 
>  Dictionary .com

첫 고등학교 프로그래밍 수업 시간에 소프트웨어 개발이란 복잡한 문제를 작은 문제들로 분해하고 작은 문제들의 해법들을 다시 조합해 복잡한 문제를 해결 할 수 있는 솔루션을 만드는 행위라고 배웠습니다.

전 그 말의 중요성을 너무 늦게 깨달았다는 것이 매우 후회됩니다. 너무 늦게 소프트웨어 설계의 본질을 깨달은 것 입니다.

수백 명의 개발자들을 인터뷰하며 나만 그런 것이 아님을 알게됐습니다.  아주 소수의 개발자만이 소프트웨어 개발의 본질에 대해 잘 알고 있었습니다.  대부분의 경우에는 우리가 활용해야 하는 가장 중요한 개념과 그것을 잘 적용하는 방법을 몰랐습니다.  다음은 소프트웨어 개발에서 가장 중요한 두가지 질문입니다.

-   함수 합성 ^function^ ^composition^ 이란 무엇인가?
-   객체 합성 ^object^ ^composition^이란 무엇인가?

문제는 당신이 단지 잘 모른다고해서 객체 및 함수 합성을 하지 않을 수는 없다는 것입니다.  당신은 여전히 의식하지않고 (나쁘게)합성을 합니다. 그렇기에 버그가 넘쳐나고 다른 개발자가 이해하기 어려운 코드가 나옵니다.  이는 큰 문제이며 적지않은 대가를 지불하고 있습니다.  소프트웨어는 만드는 것보다 유지 보수하는 데 더 많은 시간이 들고, 버그는 전 세계 수십억 명의 사람들에게 영향을줍니다.

오늘날 소프트웨어는 모든 곳에 존재합니다.  모든 자동차는 바퀴가 달린 미니 수퍼 컴퓨터이며 소프트웨어 설계 문제로 인해 사고가 발생하고 사람이 목숨을 잃습니다.  2013년, 배심원단은 사고를 조사하는 과정에서 Toyota의 소프트웨어 개발팀이 10,000 개의 글로벌 변수가있는 스파게티 코드를 작성한 것을 발견했습니다.  

[해커 및 정부](https://www.technologyreview.com/s/607875/should-the-government-keep-stockpiling-software-bugs/)는  버그를 활용해 사람들을 감시하고, 신용 카드를 훔치고, DDoS 공격을 하고 , 암호를 해독하고,  심지어 [선거를 조작](https://www.technologyreview.com/s/604138/the-fbi-shut-down-a-huge-botnet-but-there-are-plenty-more-left/)  합니다.

우리가 더 잘해야합니다.

## 사실 당신은 매일 소프트웨어를 합성합니다
모든 소프트웨어 개발자는 의식하든 하지 못하든 매일 매일 함수와 데이터 구조를 합성합니다.  조금만 신경을 써서 더 잘 할 수 있지만, 그렇지 않고 덕트 테이프와 순간 접착제로 모든걸 망쳐버릴 수도 있습니다.

소프트웨어 개발 프로세스란 큰 문제를 작은 문제로 분해하여 작은 문제를 해결하는 요소를 만들고 이러한 구성 요소들을 합성하여 완전한 응용 프로그램을애플리케이션 구성하는 것입니다.

## 함수 합성
함수 합성이란 한 함수의 출력에 다른 함수를 결합시키는 과정입니다.  대수학에서  `f`  와  `g` 두 함수, 그리고 합성 함수   `(f ∘ g)(x) = f(g(x))` 를 본적 있을 겁니다.  조그마한 원 기호는 합성 연산자입니다.  일반적으로 "composed with"또는 "after"로 발음^[한국에서는 보통 ~와 ~를 합성한다고 하거나 ~에 ~ 처럼생략합니다 ]됩니다.    `f` 와 `g`를 합성하는 것은  `f`에  `g`에 `x` 와 같이 부를 수 있습니다 . 이 때  `g`  가 먼저 평가되기 때문에  `g`  를 계산하고  그 결과를  `f`의  인수로 전달합니다.

다음과 같은 방식의 코드를 작성할 때마다 함수를 합성하고 있는 것입니다.
```javascript
const g = n => n + 1;   
const f = n => n * 2; 

const doStuff = x => {   
	const afterG = g (x);   
	const afterF = f (afterG);   
	return afterF;   
}; 

doStuff (20);  // 42
```
ES6의 promise chain은  합성함수입니다.

```javascript
const g = n => n + 1;  
const f = n => n * 2;

const wait = time => new Promise(  
  (resolve, reject) => setTimeout(  
    resolve,  
    time  
  )  
);

wait(300)  
  .then(() => 20)  
  .then(g)  
  .then(f)  
  .then(value => console.log(value)) // 42
```
마찬가지로 배열 메소드 호출하거나 lodash 메소드, observables (RxJS, etc ...)를 체인 할 때마다 함수를 합성하게 됩니다.  체이닝은 합성입니다.  반환 값을 다른 함수에 전달하는 것도 합성입니다.  `this`를  입력으로하여 두 개의 메소드를 순차적으로 호출하는 것 역시 합성입니다.

>당신이 체이닝을 한다면 합성중입니다.

함수를 합성하고있다는 사실을 알고 있어야 합니다.

다음은 함수를 합성하여  `doStuff()`  함수를 한줄로 고치는 코드입니다.

```javascript
const g = n => n + 1;   
const f = n => n * 2; 

const doStuffBetter = x => f (g (x)); 

doStuffBetter (20);  // 42
```
  이러한 방법은 종종 디버깅이 어렵다는 얘기를 듣습니다.  그렇다면 디버깅 로직이 담긴 아래 코드를 함수 합성방식으로 재작성 해보겠습니다.
```javascript
const doStuff = x => {  
  const afterG = g(x);  
  console.log(`after g: ${ afterG }`);  
  const afterF = f(afterG);  
  console.log(`after f: ${ afterF }`);  
  return afterF;  
};

doStuff(20); // =>  
/*  
"after g: 21"  
"after f: 42"  
*/
```
먼저, `afterF`,  `afterG`를 추상화할 수 있는 `trace()` 라는 간단한 로거^logger^함수를 만들어 보겠습니다.

```javascript
const trace = label => value => {  
  console.log(`${ label }: ${ value }`);  
  return value;  
};
```
이제 다음과 같이 사용할 수 있습니다.
```javascript
const doStuff = x => {  
  const afterG = g(x);  
  trace('after g')(afterG);  
  const afterF = f(afterG);  
  trace('after f')(afterF);  
  return afterF;  
};

doStuff(20); // =>  
/*  
"after g: 21"  
"after f: 42"  
*/
```
Lodash 및 Ramda와 같은 인기있는 함수 프로그래밍 라이브러리에는 함수를 쉽게 구성 할 수있는 유틸리티가 포함되어 있습니다.  위의 함수를 다음과 같이 다시 작성할 수 있습니다.

```javascript
import pipe from 'lodash/fp/flow';

const doStuffBetter = pipe(  
  g,  
  trace('after g'),  
  f,  
  trace('after f')  
);

doStuffBetter(20); // =>  
/*  
"after g: 21"  
"after f: 42"  
*/
```
라이브러리를 사용하지 않고 다음과 같이 pipe를 직접 정의 할 수도 있습니다.
```javascript
// pipe(...fns: [...Function]) => x => y  
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
```
아직은 어떻게 작동하는지 몰라도 괜찮습니다.  나중에 훨씬 더 자세하게 알아볼 것입니다.  사실 매우 중요한 코드이기 때문에 문서 전체에 걸쳐 반복해서 정의되고 시연 될 겁니다.  중요한 건 당신이 그 의미와 사용법을 자동적으로 떠올릴 수 있을 만큼 익숙해지는 것입니다.  **합성**^composition^과 한 몸이 되어야 합니다.

`pipe()` 유틸리티는 한 함수의 출력을 다른 함수의 입력으로 전달하여 함수 파이프 라인을 생성합니다.  `pipe()`  (그리고 쌍둥이라고 할 수 있는  `compose()`  )를 사용하면 중간 변수^intermediary^ ^variables^가 필요 없습니다.  인수를 언급하지 않고 함수를 작성하는 것을  **포인트없는 스타일**^point-free^ ^style^ 혹은 **무인수 방식**이라고  **합니다.**  이를 위해 함수를 명시적으로 선언하는 대신 다른 함수를 반환하는 함수를 호출합니다.  즉,  `function`  키워드 나 화살표 구문 (  `=>`  )이 필요하지 않습니다.

무인수 방식으로 전체 로직을 구성할 수도 있지만, 중간 변수들이 여러분의 기능에 불필요한 복잡성^complexity^을 더하기 때문에 여기 저기 조금씩 사용하는 것도 좋습니다.

복잡성 감소에는 몇 가지 이점이 있습니다.

## 단기 기억

인간 두뇌의  [단기 기억력](http://www.nature.com/neuro/journal/v17/n3/fig_tab/nn.3655_F2.html)은 서로 다른 항목을 저장하기 위한 공간이 한정되어있으며, 함수의 인자와 변수는 잠재적으로 그 공간 중 하나를 소비합니다.  변수가 늘어날 수록 각 변수의 의미를 기억하기 힘들어 집니다. 단기 기억 모형에 따르면 우리의 두뇌는 일반적으로 4-7 개의 항목을 단기 기억 공간에 저장할 수 있으며 이 보다 크면 오류율이 급격히 증가합니다.

우리는 함수 파이프라이닝을 사용해 변수 3개를 소거하였고 다른 변수를 기억하는데 사용할 수  있게 됐습니다.  단기 기억 공간의 거의 절반을 확보한 것입니다. 이는 인지 부하를 상당히 줄여줍니다.  소프트웨어 개발자는 데이터를 묶어 기억하는 경향이 있지만 그렇다고 이 개념의 중요성이 약해지는 것은 아닙니다.

## 신호 대 잡음비
간결함은 코드의 신호 대 잡음 비율^Signal^ ^to^ ^Noise^ ^Ratio^을 향상시킵니다.  라디오를 듣는 것과 같습니다. 라디오의 주파수가 제대로 맞춰져 있지 않으면 소음이 발생하며 음악을 듣기가 어려워집니다.  방송국 주파수로 정확하게 튜닝하면 소음이 사라지고 음악 신호가 강해집니다.

코드도 마찬가지입니다.  간결한 표현은 이해력을 향상시킵니다. 일부 코드는 유용한 정보를 제공하고 일부 코드는 공간을 차지합니다.  전달되어야 할 의미를 변화시키지 않는 선에서 코드의 양을 줄이면 코드를 읽고 이해해야하는 다른 사람들이 더 쉽게 이해할 수 있습니다.^[개발자가 코드를 작성하고 다른 개발자가 이를 이해한다는 것을 라디오의 방송국과 청취자들의 관계에 비유했 습니다. 이 때 신호란 전달되어야하는 의미이고 잡음이란 공간만 차지하는 코드입니다. 즉 더 적은 코드로 같은 의미를 전달하는 것이 좋다는 말이 됩니다.]

## 코드의 면적과 버그

함수형으로 작성된 코드를 살펴보면 마치 코드가 살을 빼 다이어트 한 것처럼 보입니다. 이 비유가 중요한 이유는 여분의 코드가 버그를 숨길 수있는 추가 표면적을 의미하기 때문입니다 . 즉 더 많은 버그가 숨어 버릴 수 있음을 의미합니다.

> _적은 코드 = 버그가 적은 표면 = 버그가 적습니다._

## 객체 합성
> "클래스 상속보다는 객체 합성을 우선해라", Gang of Four,  ["디자인 패턴 : 재사용 가능한 객체 지향 소프트웨어의 요소"](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4)

> "컴퓨터 과학에서 복합 자료형^composite^ ^datatype^이란 프로그래밍 언어의 원시형 및 기타 복합 자료형 사용하여 프로그램에서 조합 할 수있는 모든 자료형입니다.  \[...\] 복합 자료형을 만드는 행위는 합성^composition^으로 알려져 있습니다. "~ Wikipedia


다음은 원시형입니다.
```javascript
const firstName = 'Claude';  
const lastName = 'Debussy';
```
그리고 이것은 복합체입니다.
```javascript
const fullName = {  
  firstName,  
  lastName  
};
```
마찬가지로 모든 array, set, map, weak map, typed array 등은 복합 자료형입니다.  비 원시형 구조를 작성할 때마다 우리는 객체를 합성합니다.

Gang of Four의 **컴포지트 패턴**^composite^ ^pattern^은 객체들의 관계를 재귀적으로 구성하여 부분-전체 계층을 표현하는 패턴으로, 사용자가 단일 객체와 복합 객체 모두 동일하게 다루도록 정의합니다. 일부 개발자는 컴포지트 패턴이 _객체 합성의 유일한 형태_  라고 생각하면서 혼란스러워 합니다.  혼동하지 마십시오.  객체 합성에는 여러 가지 종류가 있습니다.

Gang of Four는 계속해서 "객체합성은 여러 디자인 패턴에 다양하게 적용될 것 입니다"라고 말한 다음 객체가 합성될때 두 객체의 관계를 정의하기 위한 세가지 표현을 정의했습니다.
1.  **위임**^delegation^  (state, strategy 및 visitor 패턴에서 사용 됨) 
2.  **인지**^acquaintance^  (객체가 참조로 다른 객체를 알고있을 때 일반적으로 매개 변수로 전달됨 : 네트워크 요청 처리기가 로거에 대한 참조를 전달하여 요청을 기록 \- 요청 처리기가 로거를 사용함) 
3.  **집합**^aggregation^  (자식 객체가 부모 객체의 일부를 형성하는 경우 : a - a 관계, 예를 들어 자식 DOM은 부모 노드의 구성 요소 - DOM 노드는 자식을 가짐).

클래스 상속은 복합 객체를 구성하는 데 사용할 수 있지만 제한적이고 취약한 방법입니다.  Gang of Four는 "클래스 상속보다는 객체 합성을 우선해라"라고 말하면서 객체를 합성하기 위해 클래스 상속이란 강고하고 단단히 결합 된 접근 방식보다는  유연한 접근 방식을 사용하도록 조언합니다.

["Categorical Methods in Computer Science: With Aspects from Topology"](https://www.amazon.com/Categorical-Methods-Computer-Science-Topology/dp/0387517227/ref=as_li_ss_tl?ie=UTF8&qid=1495077930&sr=8-3&keywords=Categorical+Methods+in+Computer+Science:+With+Aspects+from+Topology&linkCode=ll1&tag=eejs-20&linkId=095afed5272832b74357f63b41410cb7) (1989)에서 나오는 개체 합성에 대한보다 넓은 정의를 사용하겠습니다.

> "복합 객체는 객체들이 합쳐져 만들어 지는데, 각각의 latter는 former의 일부분이된다."

또 다른 좋은 참고 자료는 "Reliable Software Through Composite Design"(Glenford J Myers, 1975)입니다. 두 책 모두 절판되었지만 여러분이 객체 합성이라는 주제에 대해 심도있게 알아보고 싶으면 Amazon이나 이베이에서 여전히 책을 찾을 수 있습니다.

_클래스 상속은 복합 객체 생성의 한 종류에 불과합니다._ 모든 클래스는 복합 객체를 생성하지만 모든 복합 객체가 클래스 또는 클래스 상속에 의해 생성되는 것은 아닙니다.  "클래스 상속보다는 객체 합성을 우선해라"라는 말은 클래스 계층의 조상 (ancestor)에서 모든 속성을 상속하지 않고 작은 구성 요소 부분에서 복합 객체를 형성해야한다는 것을 의미합니다.  전자는 객체 지향 설계에서 잘 알려진 다양한  문제를 일으 킵니다.

-   **단단한 결합 문제** ^The^ ^tight^ ^coupling^ ^problem^ :  자식 클래스는 부모 클래스의 구현에 의존하기 때문에 클래스 상속은 객체 지향 디자인에서 사용할 수있는 가장 조밀한 결합입니다.
-   **깨지기 쉬운 기초 클래스 문제**^The^ ^fragile^ ^base^ ^class^ ^problem^  : 긴밀한 결합으로 인해  기초 클래스가  변경되면 잠재적으로 제 3자가 관리하는 코드에서 많은 수의 클래스가 손상 될 수 있습니다.  작성자는 알지 못하는 코드를 깨뜨릴 수 있습니다.
-   **경직된 계층 구조 문제**^The^ ^inflexible^ ^hierarchy^ ^problem^ :  단일 조상으로 시작해 충분한 시간과 진화가 이루어진 후에는 사실상 새로운 유스 케이스에 대해  잘못된 클래스 이름을 가지게 될 것입니다.
-   **중복 필요성 문제**^The^ ^duplication^ ^by^ ^necessity^ ^problem^ : 경직된 계층 구조로 인해 새로운 유스 케이스가 종종 확장이 아닌 복제에 의해 구현되고 이로 인해 불필요한 유사한 클래스들이 나타나게 됩니다. 유사한 클래스들이 존재하면 상속의 기준을 무엇으로 잡을지 불투명해 집니다.
-   **고릴라 / 바나나 문제**^The^ ^gorilla/banana^ ^problem^ :  "... 객체 지향 언어의 문제점은 객체가 모든 암묵적인 환경을 함께 가질 수 있다는 것입니다.  당신은 바나나를 원했지만 바나나와 정글 전체를 들고있는 고릴라가있었습니다. "~ Joe Armstrong,  ["Coders at Work "](http://www.amazon.com/gp/product/1430219483%3Fie%3DUTF8%26camp%3D213733%26creative%3D393185%26creativeASIN%3D1430219483%26linkCode%3Dshr%26tag%3Deejs-20%26linkId%3D3MNWRRZU3C4Q4BDN)

JavaScript에서 객체 합성의 가장 일반적인 형태는  **객체 연결**  (mixin composition이라고도 함)이라고합니다.  마치 아이스크림처럼 작동합니다. 먼저 바닐라 아이스크림과 같은 객체로 시작한 다음 원하는 기능을 믹스합니다.  견과류, 캐러멜, 초콜릿 소용돌이를 추가하면 너트 카라멜 초콜릿 소용돌이 아이스크림이 됩니다.

클래스 상속을 사용하여 복합 객체 만들기 :

```javascript
class Foo {  
  constructor () {  
    this.a = 'a'  
  }  
}

class Bar extends Foo {  
  constructor (options) {  
    super(options);  
    this.b = 'b'  
  }  
}

const myBar = new Bar(); // {a: 'a', b: 'b'}
```
믹스 인 성분으로 복합 객체 만들기 :
```javascript
const a = {  
  a: 'a'  
};

const b = {  
  b: 'b'  
};

const c = {...a, ...b}; // {a: 'a', b: 'b'}
```

앞으로 객체를 합성하는 다양한 방법에 대해 알아볼 것입니다.  지금까지의 논의를 정리하자면 다음과 같습니다.

1.  어떤 것을 수행하는 데는 한 가지 이상의 방법이 있습니다.
2.  어떤 방법은 다른 방법보다 낫습니다.
3.  당장의 작업을 위해 가장 단순하고 유연한 솔루션을 선택하려고 합니다.

## 결론

이 글은 FP (Functional Programming)와 OOP (Object-Oriented Programming) 또는 프로그래밍 언어에 대한 글이 아닙니다.  소프트웨어를 합성하기 위한 컴포넌트는 함수, 자료 구조, 클래스 등의 형태를 취할 수 있습니다. 프로그래밍 언어마다 컴포넌트에 대해 서로 다른 접근방식을 취합니다. 자바는 클래스를 제공하고, 하스켈은 함수를 제공합니다. 그러나 어떤 언어, 어떤 패러다임을 선호하든 관계없이 함수와 자료구조를 합성할 수 밖에 없습니다.  결국, 모두 뒤죽박죽이 될지라도 말이지요.

우리는 지금까지 대부분의 논의를 함수형 프로그래밍에 관하여 했습니다.  그 이유는 함수야 말로 JavaScript에서 가장 합성을 하기 쉬운 요소이고, 함수 프로그래밍 커뮤니티가 함수 합성 테크닉을 공식화하는 데 많은 시간과 노력을 투자했기 때문입니다.

이 글은 함수형 프로그래밍이 객체 지향 프로그래밍보다 낫다는 논의를 하려는게 아닙니다. 만약 그렇다면 두 패러다임중 하나를 선택하라 할테지요.  OOP 대 FP는 잘못된 이분법입니다.  최근 몇 년 동안 본 모든 Javascript 애플리케이션은 FP와 OOP를 광범위하게 혼합합니다.

객체를 합성하여 FP에서 사용될 자료구조를 만들고 함수형 프로그래밍으로 OOP에서 객체를 만들어볼 것 입니다.

_소프트웨어를 작성하는 방법에 상관없이 훌륭한 합성을 해야합니다_

> 소프트웨어 개발의 핵심은 합성입니다.

합성을 이해하지 못하는 소프트웨어 개발자는 볼트나 못에 대해 모르는 주택 건설업자와 같습니다.  어떻게 합성될지 신경쓰지 않고 소프트웨어를 제작하는 것은 집을 건설할 때 덕트 테이프와 순간 접착제로 벽을 붙이는 것과 같습니다.

이제는 단순하게 생각할 때입니다. 어떤 것을 단순화하는 가장 좋은 방법은 본질에 도달하는 것입니다.  문제는, 소프트웨어 산업에 있는 대부분의 사람들이 본질에 대해 무관심하다는 것입니다.  우리 업계는 소프트웨어 개발자인 당신을 제대로 가르치지 못했습니다.  업계는 개발자를 더 잘 훈련시켜야할 책임이 있습니다. 우리는 이를 고쳐야 합니다.  우리가 책임을 져야합니다.  경제에서 의료 장비에 이르기까지 모든 것에서 소프트웨어가 실행됩니다.  이 행성의 모든 인간의 삶은 소프트웨어 품질에 영향을받고 있습니다.  우리가 뭘 하고있는지 알아야합니다.

지금부터 소프트웨어 합성 방법을 배우면 됩니다.

[다음: 함수형 프로그래밍 패러다임의 역사>](https://midojeong.github.io/2018/03/20/the-rise-and-fall-and-rise-of-functional-programming-composable-software/)