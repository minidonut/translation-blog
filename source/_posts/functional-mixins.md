---
title: 함수형 믹스인
catalog: true
date: 2018-04-08 02:10:13
subtitle: functional-mixins
header-img: "bg.jpg"
readingTime: 12
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 함수형 믹스인은 합성될 수 있는 팩토리함수입니다. 파이프라이닝되어 객체를 찍어내는 공장입니다. 각 함수는 조립 라인의 인력처럼 객체의 속성 또는 동작을 추가합니다. 함수형 믹스인은 기본 팩토리 또는 생성자가 필요하지 않습니다. 임의의 객체를 믹스인으로 전달하기만 하면 해당 객체의 향상된 버전이 반환됩니다. 실제로 현대의 모든 소프트웨어 개발은 합성으로 이루어집니다. 우리는 크고 복잡한 문제를 작고 단순한 문제로 분해 한 다음 솔루션을 조합하여 애플리케이션을 구성합니다.
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/functional-mixins-composing-software-ffb66d5e731c)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)  |  [<< Part 1에서 다시 시작](https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea)  |  [다음>](https://medium.com/javascript-scene/reduce-composing-software-fe22f0c39a1d)

**함수형 믹스인**은 합성될 수 있는 팩토리함수입니다. 파이프라이닝되어 객체를 찍어내는 공장입니다. 각 함수는 조립 라인의 인력처럼 객체의 속성 또는 동작을 추가합니다. 함수형 믹스인은 기본 팩토리 또는 생성자가 필요하지 않습니다. 임의의 객체를 믹스인으로 전달하기만 하면 해당 객체의 향상된 버전이 반환됩니다.

함수형 믹스인의 특징 :

-   데이터 보안/캡슐화
-   private 상태를 상속
-   여러 소스에서 상속
-   다이아몬드 문제(namespace충돌) 없음 - 마지막으로 상속된 것이 이김
-   베이스 클래스가 필요하지 않음

## 동기부여

실제로 현대의 모든 소프트웨어 개발은 합성으로 이루어집니다. 우리는 크고 복잡한 문제를 작고 단순한 문제로 분해 한 다음 솔루션을 조합하여 애플리케이션을 구성합니다.

합성의 재료로 쓰이는 원자 단위는 다음과 같습니다.

-   함수
-   데이터 구조

애플리케이션의 구조는 이러한 원자들이 조합되는 방식으로 정의됩니다. 많은 곳에서 클래스 상속을 사용하여 복합 객체를 만듭니다. 클래스는 상위 클래스의 기능들 대부분을 상속하고 이를 확장^extend^ 또는 오버라이딩합니다.  이 접근 방식은 **"~는 ~이다(is-a) "** 라는 사고를 강요한다는 문제가 있습니다. 예를 들어 "관리자는 직원" 같은 방식으로 관계를 정의하며 이런 접근방식에서 많은 디자인 문제가 발생합니다.

-   **단단한 결합 문제** ^The^ ^tight^ ^coupling^ ^problem^ :  자식 클래스는 부모 클래스의 구현에 의존하기 때문에 클래스 상속은 객체 지향 디자인에서 사용할 수있는 가장 조밀한 결합입니다.
-   **깨지기 쉬운 기초 클래스 문제**^The^ ^fragile^ ^base^ ^class^ ^problem^  : 긴밀한 결합으로 인해  기초 클래스가  변경되면 잠재적으로 제 3자가 관리하는 코드에서 많은 수의 클래스가 손상 될 수 있습니다.  작성자는 알지 못하는 코드를 깨뜨릴 수 있습니다.
-   **경직된 계층 구조 문제**^The^ ^inflexible^ ^hierarchy^ ^problem^ :  단일 조상으로 시작해 충분한 시간과 진화가 이루어진 후에는 사실상 새로운 유스 케이스에 대해  잘못된 클래스 이름을 가지게 될 것입니다.
-   **중복 필요성 문제**^The^ ^duplication^ ^by^ ^necessity^ ^problem^ : 경직된 계층 구조로 인해 새로운 유스 케이스가 종종 확장이 아닌 복제에 의해 구현되고 이로 인해 불필요한 유사한 클래스들이 나타나게 됩니다. 유사한 클래스들이 존재하면 상속의 기준을 무엇으로 잡을지 불투명해 집니다.
-   **고릴라 / 바나나 문제**^The^ ^gorilla/banana^ ^problem^ :  "... 객체 지향 언어의 문제점은 객체가 모든 암묵적인 환경을 함께 가질 수 있다는 것입니다.  당신은 바나나를 원했지만 바나나와 정글 전체를 들고있는 고릴라가있었습니다. "~ Joe Armstrong,  ["Coders at Work "](http://www.amazon.com/gp/product/1430219483%3Fie%3DUTF8%26camp%3D213733%26creative%3D393185%26creativeASIN%3D1430219483%26linkCode%3Dshr%26tag%3Deejs-20%26linkId%3D3MNWRRZU3C4Q4BDN)

관리자가 직원이라고 정의했을 때 일시적으로 관리 업무를 수행하기 위해 외부 컨설턴트를 고용하는 상황을 어떻게 처리합니까?  모든 요구사항을 미리 알고 있다면 클래스 상속이 가능할 수도 있지만 그건 불가능합니다. 애플리케이션이 충분히 사용되다 보면 새로운 문제와 보다 효율적인 프로세스가 발견되고 애플리케이션의 기능과 요구 사항은 필연적으로 증가하고 발전합니다.

이런 상황에서 믹스인은 보다 유연한 접근 방식을 제공합니다.

## 믹스인이란 무엇입니까?

> "클래스 상속보다는 객체 합성을 우선해라", Gang of Four,  ["디자인 패턴 : 재사용 가능한 객체 지향 소프트웨어의 요소"](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4)

**믹스인**은  **객체 합성**의 한 종류로, 구성 요소의 속성들이 혼합되어 복합 객체의 속성들을 구성합니다.

OOP에서 "믹스인"이란 용어는 믹스인 아이스크림 가게에서 등장합니다. 미리 만들어진 많은 양의 아이스크림들이 있는 대신 바닐라 아이스크림과 각 고객별로 맞춤식 맛을 내기 위해 혼합될 수 있는 여러가지 재료가 있습니다.

오브젝트 믹스인도 비슷합니다. 빈 오브젝트로 시작해 기능을 믹스하고 확장합니다.  JavaScript는 클래스가 없이 객체를 생성하고 동적 객체 확장을 지원하기 때문에 오브젝트 믹스인을 쉽게 사용할 수 있습니다. JavaScript에서 가장 일반적인 상속 방법인 만큼 다른 것들과 큰 차이가 있습니다.  예제를 살펴 보겠습니다.

```javascript
const chocolate = {  
  hasChocolate: () => true  
};

const caramelSwirl = {  
  hasCaramelSwirl: () => true  
};

const pecans = {  
  hasPecans: () => true  
};

const iceCream = Object.assign({}, chocolate, caramelSwirl, pecans);

/*  
// or, if your environment supports object spread...  
const iceCream = {...chocolate, ...caramelSwirl, ...pecans};  
*/

console.log(`  
  hasChocolate: ${ iceCream.hasChocolate() }  
  hasCaramelSwirl: ${ iceCream.hasCaramelSwirl() }  
  hasPecans: ${ iceCream.hasPecans() }  
`);
```

이는 다음과 같은 로그를 남깁니다 :

```javascript
hasChocolate: true
hasCaramelSwirl: true
hasPecans: true
```

## 함수형 상속이란 무엇입니까?

함수형 상속은 오브젝트 인스턴스에 함수를 적용하여 기능을 상속하는 프로세스입니다.  이 함수는 클로저를 사용해 일부 데이터를 비공개로 유지하며 동적으로 오브젝트를 확장해 새로운 속성 및 메소드를 가진 인스턴스를 만듭니다.

Douglas Crockford가 작성한 코드를 보자.

```javascript
// Base object factory  
function base(spec) {  
    var that = {}; // Create an empty object  
    that.name = spec.name; // Add it a "name" property  
    return that; // Return the object  
}

// Construct a child object, inheriting from "base"  
function child(spec) {  
    // Create the object through the "base" constructor  
    var that = base(spec);   
    that.sayHello = function() { // Augment that object  
        return 'Hello, I\'m ' + that.name;  
    };  
    return that; // Return it  
}

// Usage  
var result = child({ name: 'a functional object' });  
console.log(result.sayHello()); // "Hello, I'm a functional object"
```

그러나 `child()`가  `base()`에  단단하게 결합되어 있으므로 `grandchild()`,  `greatGrandchild()`등을 추가하려고 할 때면 클래스 상속의 일반적인 문제를 다시 마주치게 됩니다.

## 함수형 믹스인이란 무엇입니까?

함수형 믹스인은 새로운 속성이나 동작을 주어진 개체의 속성과 혼합하는 **합성가능한 함수**입니다.  함수형 믹스인은 기본 팩토리 또는 생성자가 필요하지 않고 이들에 의존하지도 않습니다. 확장을 하기 위해서 임의의 객체를 믹스인으로 전달하기만 하면 됩니다.

예제를 살펴 보겠습니다.

```javascript
const flying = o => {  
  let isFlying = false;

  return Object.assign({}, o, {  
    fly () {  
      isFlying = true;  
      return this;  
    },

    isFlying: () => isFlying,

    land () {  
      isFlying = false;  
      return this;  
    }  
  });  
};

const bird = flying({});  
console.log( bird.isFlying() ); // false  
console.log( bird.fly().isFlying() ); // true
```

`flying()`을 호출 할 때, 객체를 전달해야 합니다.  함수형 믹스인은 **합성 함수**를 사용하도록 설계되었습니다.  더 많은 예제를 알아보겠습니다.

```javascript
const quacking = quack => o => Object.assign({}, o, {  
  quack: () => quack  
});

const quacker = quacking('Quack!')({});  
console.log( quacker.quack() ); // 'Quack!'
```
## 함수형 믹스인을 합성하기

함수형 믹스인은 함수 합성으로 간단하게 구현 할 수 있습니다.
```javascript
const createDuck = quack => quacking(quack)(flying({}));

const duck = createDuck('Quack!');

console.log(duck.fly().quack());
```
조금 어색해 보입니다.  또한 디버깅하거나 합성 순서를 바꾸는 것이 약간 까다로울 수 있습니다.

물론 이것은 기초적인 함수 합성이며  `compose()` 또는 `pipe()`를 사용하는 더 나은 방법을 이미 알고 있습니다.  `pipe()`를 사용하면 순서가 거꾸로 바뀌고 컴포지션은 동일한 우선 순위를 유지하면서  `Object.assign({}, ...)` 또는  `{...object, ...spread}` 처럼 읽을 수 있게 됩니다. 이름이 충돌하는 경우, 마지막으로 합성된 객체, 속성, 메소드가 승리합니다.

```javascript
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);
// OR...
// import pipe from `lodash/fp/flow`;

const createDuck = quack => pipe(
  flying,
  quacking(quack)
)({});

const duck = createDuck('Quack!');

console.log(duck.fly().quack());
```

## 함수형 믹스인을 사용해야 하는 경우

항상 문제를 해결하기 위한 가장 단순한 추상화를 사용해야합니다. 처음에는 **순수 함수**로 시작하십시오.  상태가 존재하는 객체가 필요하면 **팩토리 함수**를 사용해보십시오. 보다 복잡한 객체를 작성해야 할 경우 **함수형 믹스인**을 사용하십시오.

다음은 함수형 믹스인을 사용한 좋은 예들 입니다.

-   애플리케이션 상태 관리 (예 : Redux 저장소).
-   centralized logger와 같은 특정 문제 및 서비스.
-   합성 가능한 함수형 자료구조 (예 : JavaScript의  `Array`를 사용해서 `Semigroup`  ,  `Functor`  ,  `Foldable`을 구현할 수 있습니다.  일부 대수 구조^algebraic^ ^structure^는 다른 대수 구조에서 파생 될 수 있습니다. 즉,  커스터마이징하지 않고도 특정 근원에서 새로운 데이터 유형으로 합성 될 수 있음을 의미합니다.

**React 사용자 :**   `class`를 사용해도 괜찮습니다. 그 이유는 프레임워크 디자인 차원에서 caller가 `new`를 사용하지 않도록 되어있고 React가 기본적으로 제공하는 base 컴포넌트 외에 다른 컴포넌트를 상속하지 않도록  문서화된 모범 사례^best-practice^들을 제공하기 때문입니다.

저는 React의 UI 컴포넌트를 구성할 때 함수 합성을 사용하여 HOC (Higher Order Components)를 사용할 것을 권장합니다.

## 주의 사항

대부분의 문제는 순수 함수를 사용하여 우아하게 해결할 수 있습니다. 그러나 함수형 믹스인의 경우에는 아닙니다.  클래스 상속과 마찬가지로 함수형 믹스인은 자체적으로 문제를 일으킬 수 있습니다.  실제로 함수형 믹스인을 사용하여 클래스 상속의 모든 기능과 문제점을 충실히 재현 할 수 있습니다.

이를 피하기 위해 다음 조언을 따르면 됩니다:

-   가장 간단한 구현방법을 택하세요. 왼쪽에서 시작해 필요에 따라 오른쪽으로 이동하십시오 : 순수 함수 > 팩토리 > 함수형 믹스인 > 클래스.
-   객체, 믹스인 또는 데이터 유형간  **is-a**  관계가 생성되는걸 피하십시오.
-   믹스인사이의 암묵적인 의존성을 제거하십시오 — 가능하다면 함수형 믹스인은 자체로 만족적이어야 하고 다른 믹스인에 대한 참조가 없어야 합니다.
-   "함수형 믹스인"은 "함수형 프로그래밍"을 의미하지 않습니다.
-   `Object.assign()` 또는 객체 스프레드 구문 (  `{...}` )을 사용하여 속성에 액세스 할 때 부작용이 있을 수 있습니다.  또한 열거되지 않는 속성은 건너 뛴다는 것을 주의해야 합니다..  ES2017은 이 문제를 해결하기 위해  [`Object.getOwnPropertyDescriptors()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors)를 추가했습니다.

자신의 사이드 프로젝트가 아닌 큰 프로젝트에서 함수형 믹스인을 사용하려고 한다면,  [stamps](https://github.com/stampit-org/stamp-specification)를 참고하십시오. Stamp Specification은 속성 설명자, 프로토 타입 위임 등을 다루며 표준 JS 기능들을 사용하여 합성 가능한 팩토리 함수를 공유하고 재사용하는 표준입니다.

필자는 주로 애플리케이션의 구조 및 동작을 구성하기 위해 함수 합성을 사용하는데 이 때 함수형 믹스인이나 _stamp_ 는 거의 필요하지 않습니다.  그리고 클래스 상속은 절대 사용하지 않는데 한가지 예외가 있다면 `React.Class`같은 서드파티의 기본 클래스를 최소한의 루트로 상속해서 사용하는 것 입니다. 저는 절대로 직접 상속 계층 구조를 만들지 않습니다.

## 클래스

클래스 상속이 JavaScript에서 괜찮은 접근법인 경우는 거의(아마 절대로) 없습니다. 그러나 본인이 직접 구현하지 않은 라이브러리나 프레임 워크에서 `class`를 사용하는 경우는 많이 있습니다.  이 경우  `class`를 실용적으로 사용할 수 있습니다. 다만 다음과 같은 특징을 가진 라이브러리에서 제공되는 `class`로 제한됩니다:

1.  직접 클래스를 확장 할 필요가 없어야 합니다(즉, 다중 레벨의 클래스 상속 구조를 만들 필요가 없습니다).
2.  `new`  키워드를 직접 사용할 필요가 없습니다. 즉, 프레임 워크가 인스턴스화를 처리합니다.

Angular 2+와 React 모두 이러한 요구 사항을 충족하므로 클래스를 확장하지 않는 한 클래스를 안전하게 사용할 수 있습니다.  React는 여러분이 원하지 않는 경우 클래스를 사용하지 않아도 됩니다(선택사항입니다). 그러나 React의 기본 클래스에 내장 된 최적화 기능을 사용하지 못하고 여러분의 컴포넌트가 예제 문서에 나오는 컴포넌트처럼 보이지 않을 수 있습니다. 어쨌든 React 컴포넌트도 마찬가지로 가능하다면 순수 함수 형식을 항상 선호해야합니다.

### 클래스와 퍼포먼스

일부 브라우저의 JavaScript 엔진에서는 클래스에 특화된 최적화를 제공합니다. 대부분의 경우 이러한 최적화는 앱의 성능에 큰 영향을 미치지 않습니다.  실제로,  `class`  성능 차이에 대해 걱정할 필요없이 오랜 기간동안 프로젝트를 진행할 수 있습니다.  객체를 생성하고 속성에 접근하는 것은 객체를 만드는 방법에 관계없이 항상 매우 빠릅니다(초당 수백만번의 연산이 가능).

그러나, RxJS, Lodash 등과 같은 범용 유틸리티 라이브러리를 만들 때는  `class`  를 사용하여 객체 인스턴스를 만들었을 때 얻을 수 있는 성능상의 이점을 조사해야합니다.  `class` 사용해서 해결할 수 있는 성능상의 병목현상이 없다면 (그리고 그 것을 증명할 수 있는게 아니라면)  성능에 대해 걱정하기 보다는 깨끗하고 유연한 코드 작성하는 방식으로 최적화해야합니다.

## 암시적인 종속성

당신은 함수형 믹스인으로 여러 기능들을 욱여넣고 싶은 유혹을 느낍니다.  당신이 설정 관리자^configuration^ ^manager^를 만들려고 합니다. 이 관리자는 존재하지 않는 설정 속성에 액세스하려고 할 때 경고 로그를 띄웁니다.

다음과 같이 빌드 할 수 있습니다.

```javascript
// in its own module...  
const withLogging = logger => o => Object.assign({}, o, {  
  log (text) {  
    logger(text)  
  }  
});  
  

// in a different module with no explicit mention of  
// withLogging -- we just assume it's there...  
const withConfig = config => (o = {  
  log: (text = '') => console.log(text)  
}) => Object.assign({}, o, {  
  get (key) {  
    return config[key] == undefined ?

      // vvv implicit dependency here... oops! vvv  
      this.log(`Missing config key: ${ key }`) :  
      // ^^^ implicit dependency here... oops! ^^^

      config[key]  
    ;  
  }  
});

// in yet another module that imports withLogging and  
// withConfig...  
const createConfig = ({ initialConfig, logger }) =>  
  pipe(  
    withLogging(logger),  
    withConfig(initialConfig)  
  )({})  
;

// elsewhere...  
const initialConfig = {  
  host: 'localhost'  
};

const logger = console.log.bind(console);

const config = createConfig({initialConfig, logger});

console.log(config.get('host')); // 'localhost'  
config.get('notThere'); // 'Missing config key: notThere'
```

그러나 다음과 같이 빌드 할 수도 있습니다.

```javascript
// import withLogging() explicitly in withConfig module  
import withLogging from './with-logging';

const addConfig = config => o => Object.assign({}, o, {  
  get (key) {  
    return config[key] == undefined ?   
      this.log(`Missing config key: ${ key }`) :  
      config[key]  
    ;  
  }  
});

const withConfig = ({ initialConfig, logger }) => o =>  
  pipe(

    // vvv compose explicit dependency in here vvv  
    withLogging(logger),  
    // ^^^ compose explicit dependency in here ^^^

    addConfig(initialConfig)  
  )(o)  
;

// The factory only needs to know about withConfig now...  
const createConfig = ({ initialConfig, logger }) =>  
  withConfig({ initialConfig, logger })({})  
;  
  

// elsewhere, in a different module...  
const initialConfig = {  
  host: 'localhost'  
};

const logger = console.log.bind(console);

const config = createConfig({initialConfig, logger});

console.log(config.get('host')); // 'localhost'  
config.get('notThere'); // 'Missing config key: notThere'
```

우리는 다양한 요소들을 고려해서 올바른 디자인을 선택해야 합니다.  함수형 믹스인을 사용하기 위해 래핑된 데이터 유형을 요구하는 것은 타당하지만, 그렇다면 이를 함수 서명 및 API 문서에 명시해야합니다.

그렇기에 암시적인 버전의 함수 서명에는  `o`  에 대한 기본값이 존재해야합니다. JavaScript에는 타입 주석 기능이 없기 때문에 기본값을 제공하여 모방할 수 있습니다.
```javascript
 const withConfig = config => (o = {   
 log: (text = '') => console.log(text)   
 }) => Object.assign({}, o, {   
 // ...
```
TypeScript 또는 Flow를 사용하는 경우 명시적으로 인터페이스를 선언해 객체 요구 사항으로 사용하는 것이 좋습니다.

## 함수형 믹스인 및 함수형 프로그래밍

함수형 믹스인에서 "함수형"은 "함수형 프로그래밍"과 같은 뜻을 지니지 않습니다.  함수형 믹스인은 일반적으로 부작용이있는 OOP 스타일로 사용됩니다.  이는 인자로 전달받은 객체를 변경합니다. 주의하십시오.

마찬가지로 함수형 프로그래밍 스타일을 선호는 일부 개발자들은 전달받은 객체의 주소를 계속 가지고 있지 않습니다.  여러분은 두 스타일을 적절히 혼합하여 사용하는 방식으로 코딩해야합니다.

즉, 객체 인스턴스를 리턴해야하는 경우 클로저에 있는 인스턴스 대신 항상 `this`를 리턴하십시오.  이 말은 즉, 그 둘이 동일한 객체가 아닐 가능성이 있다는 것 입니다.  또한  `Object.assign()` 나  `{...object, ...spread}`  구문을 사용해서 인스턴스를 복사, 할당한다고 가정할 경우 열거되지 않는 속성이 있으면 최종 객체에서 작동하지 않을 것입니다.
```javascript
const a = Object.defineProperty({}, 'a', {  
  enumerable: false,  
  value: 'a'  
});

const b = {  
  b: 'b'  
};

console.log({...a, ...b}); // { b: 'b' }
```
마지막으로, 함수형 코드에서 작성하지 않은 함수형 믹스인을 사용하는 경우 코드가 순수하다고 가정하지 마십시오. 기본 객체가 변형 될 수 있고 부작용이 있으며 참조 투명성이 확보되지 않는다고 가정해야 합니다. 즉, 함수형 믹스인으로 구성된 팩토리는 메모이제이션하기 힘듭니다.

## 결론

함수형 믹스인는 조립 라인의 스테이션처럼 객체에 속성 및 동작을 추가하는 합성 가능한 팩토리 함수입니다.  고전적인 클래스(  **is-a**  )처럼 모든 기능을 상속하는 것과는 대조적으로 여러 관계(  **has-a, uses-a, can-do**  )와 기능으로 동작을 정의하는 좋은 방법입니다.

"함수형 믹스인"은 "함수형 프로그래밍"을 의미하는 것이 아니라 단순히 "함수에 기초한 믹스인"을 의미합니다.  함수형 믹스인은 함수형 프로그래밍 스타일을 사용하여 부작용을 피하고 참조 투명성을 유지하면서 작성 될 수 있지만 보장되지는 않습니다.  서드파티의 믹스인에는 부작용과 비결정적인 위험 요소들이 있을 수 있습니다.

-   단순한 오브젝트 믹스인와 달리 함수형 믹스인는 비공개 데이터를 상속하는 등 실제 데이터 프라이버시 (캡슐화)를 지원합니다.
-   단일 조상 클래스 상속과는 달리, 함수형 믹스인은 클래스 데코레이터, 특성^trait^ 또는 다중 상속과 마찬가지로 다양한 조상으로부터 기능을 상속받는데 적합합니다.
-   C ++의 다중 상속과는 달리, JavaScript에서는 다이아몬드 문제가 거의 발생하지 않습니다. 충돌이 발생할 때 간단한 규칙이 있기 때문입니다. 마지막으로 추가 된 믹스인이 승리합니다.
-   클래스 데코레이터, 특성 또는 다중 상속과 달리 기본 클래스를 필요로 하지 않습니다.

가장 간단한 구현으로 시작하여 필요한 경우에만보다 복잡한 구현으로 이동하십시오.

 >**함수 > 객체 > 팩토리 함수 > 함수형 믹스인 > 클래스**
> Functions > objects > factory functions > functional mixins > classe

[**다음: ES6+와 팩토리 함수 >**](#)