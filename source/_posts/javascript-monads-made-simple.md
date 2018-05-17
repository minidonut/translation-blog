---
title: JavaScript 모나드
catalog: true
date: 2018-04-18 10:29:57
subtitle: JavaScript Monads Made Simple
header-img: "bg.jpg"
readingTime: 16
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 모나드Monad는 특정 컨텍스트에 속한 함수를 합성하는 도구입니다. 특정 컨텍스트란  계산, 분기, I/O, 값을 반환하는 과정 등을 예로 들 수 있습니다.  모나드 타입은 리프팅 함수a => M(b)를 합성할 수 있도록 lift, flat, map을 활용해 타입을 정렬합니다.  이 과정은 결국 임의의 타입 a를  b로 맵핑하는 것이며 계산 컨텍스트 속에 lift, flatten 및 map이 숨겨져 있습니다.
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/javascript-monads-made-simple-7856be57bfe8)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/04/14/composable-datatype-with-functions/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/04/19/mocking-is-a-code-smell/)

모나드를 이해하기 위해선 다음을 이미 알고 있어야 합니다.

-   함수 합성 :  `compose(f, g)(x) = (f ∘ g)(x) = f(g(x))`
-   Functor의 기본 :  `Array.map()`에 대한 이해.

----------

> "모나드를 이해하고 나면 갑자기 설명할 방법이 떠오르지 않습니다."Lady Monadgreen’s curse ~ Gilad Bracha (used famously by Douglas Crockford)

----------

> "Hoenikker 박사가 말했습니다. 8살짜리 아이에게 자신이 하고있는 일을 설명 할 수 없는 과학자는 돌팔이입니다."~ Kurt Vonnegut의 소설 Cat 's Cradle


인터넷에서 "모나드"를 검색하면 불가사의한 카테고리 이론 문서들의 포격을 받게 되며,  그 후 부리토와 우주복을 예로 들어 모나드를 "정말 쉽게" 설명하고 있는 글들을 마주치게 됩니다.

모나드는 간단합니다.  그러나 이를 설명하고 있는 용어들은 어렵습니다. 우리는 본질을 파고들어야 합니다.

**모나드**는 특정 컨텍스트에 속한 함수를 합성하는 도구입니다. 특정 컨텍스트란  계산, 분기, I/O, 값을 반환하는 과정 등을 예로 들 수 있습니다.  모나드 타입은 리프팅 함수`a => M(b)`를 합성할 수 있도록 lift, flat, map을 활용해 타입을 정렬합니다.  이 과정은 결국 임의의 타입  `a`를  `b`로 맵핑하는 것이며 계산 컨텍스트 속에 lift, flatten 및 map이 숨겨져 있습니다.

-   함수 맵:  `a => b`
-   컨텍스트가 있는 Functor 맵:   `Functor(a) => Functor(b)` 
-   컨텍스트와 Flatten을 사용하는 모나드 맵:   `Monad(Monad(a)) => Monad(b)`

**컨텍스트**와 **flatten** 그리고 **map**이 과연 무엇일까요?

-   **Map**이란 "`a`에 특정 함수를 적용해  `b`를 리턴합니다"라는 의미입니다. 특정 입력을 받아 특정 출력을 반환합니다.
-   **컨텍스트**는 모나드 합성에 관련된 구현 세부 사항입니다.  Functor/모나드 API와 동작방식은 모나드를 앱의 나머지 부분과 합성할 수 있게 하는 컨텍스트를 제공합니다.  Functor와 모나드의 핵심은 이 컨텍스트를 추상화하여 어떤 것을 합성하고 연산하는 동안 문제가 생기지 않게 만드는데 있습니다.  컨텍스트 내에서 맵핑한다는 것은  `a => b`라는 함수를 컨텍스트 내부에 있는 값 `a`에 적용해서 새로운 값 `b`을  동일한 컨텍스트로 리턴한다는 의미입니다.  Observable이 왼쪽에 있으면 오른쪽에도 있어야 합니다. `Observable(a) => Observable(b)`   왼쪽에 배열이 있으면 오른쪽에도 있어야 합니다.  `Array(a) => Array(b)` 
-   **Type lift**는 값을 컨텍스트로 감싸는 것 입니다. 해당 값을 가지고 할 수 있는 동작, 연산들을 정의해놓은 API가 바로 컨텍스트이며, 컨텍스트 자체와 관련된 연산들도 포함되어있습니다.   `a => F(a)`(모나드는 일종의 펑터입니다).
-   **Flatten**은 컨텍스트 속에 있는 값을 빼내는 것을 의미 합니다 .  `F(a) => a`  


예:

```javascript
const x = 20;             // Some data of type `a`  
const f = n => n * 2;     // A function from `a` to `b`  
const arr = Array.of(x);  // The type lift.  
// JS has type lift sugar for arrays: [x]

// .map() applies the function f to the value x  
// in the context of the array.  
const result = arr.map(f); // [40]
```

이 경우  컨텍스트는 `Array`가 되고 `x`는 컨텍스트에 담겨 맵핑되는 값입니다.

이 예제는 이중배열을 다루진 않지만,  `.concat()`로 배열을 flatten할 수 있습니다.

```javascript
[].concat.apply([], [[1], [2, 3], [4]]); // [1, 2, 3, 4]
```

## 여러분은 이미 모나드를 사용하고 있습니다.

기술 수준이나 카테고리 이론에 대한 이해도와 상관없이 모나드를 사용하면 코드를 더 쉽게 짤 수 있습니다. 모나드를 활용하지 못한다면 코드가 더 어려워질 것입니다. (e.g., 콜백 지옥, 중첩 된 조건문, 스파게티 코드)

소프트웨어 개발의 본질은 합성이고 모나드는 합성을 쉽게 할 수 있도록 도와주는 역할을 합니다.

-   함수 맵:  `a => b`,  특정 타입의 함수를 합성할 수 있습니다.
-  컨텍스트가 있는 Functor 맵: `Functor(a) => Functor(b)`,  함수를 합성 할 수 있습니다.  `F(a) => F(b)`
-   컨텍스트와 flatten을 사용하는 모나드 맵:  `Monad(Monad(a)) => Monad(b)`, lift 함수를 합성할 수 있습니다.  `a => F(b)`

이들은 모두  **함수 합성**을 표현하는 다른 방식입니다.  함수는 합성되기 위해 존재합니다. 함수는 복잡한 문제를 쉽게 풀 수 있는 간단한 문제로 분해하고 솔루션들을 여러 가지 방법으로 합성하여 애플리케이션을 만들 수 있도록 도와줍니다.

함수를 이해하고 올바르게 사용하려면 합성을 더 깊이 이해해야 합니다.

함수를 합성한다는 것은 데이터가 흐르는 파이프라인을 만드는 것과 같습니다. 파이프라인의 첫 단계에 특정 값을 넣으면 마지막 단계에서 변환된 값이 출력됩니다.  그러나 이것이 작동하려면 파이프 라인의 각 단계에서 이전 단계에서 반환하는 데이터 형식이 필요합니다.

일반 함수를 합성하는 것은 간단합니다. 타입을 쉽게 정렬시킬 수 있기 때문이죠. 리턴 타입  `b`를 인풋 타입  `b`와 일치 시키면 됩니다.

```
g:           a => b  
f:                b => c  
h = f(g(a)): a    =>   c
```

Functor를 합성하는 것도 간단합니다.  타입을 정렬시킬 수 있기 때문이죠. 

```
g:             F(a) => F(b)  
f:                     F(b) => F(c)  
h = f(g(Fa)):  F(a)    =>      F(c)
```

그러나  `a => F(b)`,  `b => F(c)`라는 함수를 합성하려면 모나드가 필요합니다.  모나드와 Functor를 구분하기 위해 `F()` 대신  `M()`이라고 쓰겠습니다.

```
g:                  a => M(b)  
f:                       b => M(c)  
h = composeM(f, g): a    =>   M(c)
```

잠깐. 이 예제에서는  _함수 타입들이  정렬되지 않았습니다!_   `f`의 입력으로 `b`라는 타입이 필요하지만 실제로 전달받은 타입은  `M(b)`(monad of `b`)였고 결국`composeM()`에는  `g`가 리턴한 `M(b)`에서 `b`를 빼내는 과정이 필요합니다.   이 프로세스(`.bind()`  또는  `.chain()`)속에  flatten과 mapping이 숨겨져있습니다.

다음 함수로 전달하기 전에  `M(b)`에서 `b`를 추출(unwrapping, flatten)합니다.

```
g:             a => M(b) flattens to => b  
f:                                      b           maps to => M(c)  
h composeM(f, g):  
               a       flatten(M(b)) => b => map(b => M(c)) => M(c)
```

모나드는  `a => M(b)`형식의 함수들을 합성할 수 있도록 타입을 정렬시킵니다.

`M(b) => b`로의 `flatten`과   `b => M(c)`로의 `map`은    `chain` 연산 내부에서 호출되며 `chain`은  `composeM()` 내부에서 호출됩니다.  이러한 세부 구현은 추상화되어있기 때문에 사용자가 걱정할 필요가 없습니다.  따라서 일반 함수를 합성하는 것과 같은 방식으로 모나드 타입 함수를 합성할 수 있습니다.

모나드가 필요한 이유는 실제로 프로그램의 여러 모듈에서 단지  `a => b`와 같은  간단한 맵핑만 처리하는 것이 아니기 때문입니다.  일부 함수는 부수작용(프라미스, 스트림)이나 분기(Maybe), 예외 (Either) 등을 처리해야합니다.

다음은 좀 더 구체적인 예입니다.  비동기 API에서 User 데이터를 가져온 뒤 해당 데이터를 다른 비동기 API에 전달하여 계산을 수행해야하는 경우를 알아보겠습니다.
```
getUserById(id: String) => Promise(User)  
hasPermision(User) => Promise(Boolean)
```

 함수를 몇 개 정의하겠습니다.  우선 유틸리티 함수입니다.  `compose()`  및  `trace()`  :

```javascript
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);

const trace = label => value => {  
  console.log(`${ label }: ${ value }`);  
  return value;  
};
```

함수들을 합성해보겠습니다.

```javascript
{  
  const label = 'API call composition';

  // a => Promise(b)  
  const getUserById = id => id === 3 ?  
    Promise.resolve({ name: 'Kurt', role: 'Author' }) :  
    undefined  
  ;

  // b => Promise(c)  
  const hasPermission = ({ role }) => (  
    Promise.resolve(role === 'Author')  
  );

  // Try to compose them. Warning: this will fail.  
  const authUser = compose(hasPermission, getUserById);

  // Oops! Always false!  
  authUser(3).then(trace(label));  
}
```

`hasPermission()`과  `getUserById()`를 합성하여  `authUser()`를 만드려고 합니다. 그러나  `hasPermission()`이  `User`타입 대신  `Promise(User)`를 받게되는 문제가 발생합니다.  이 문제를 해결하려면 `compose()`  대신    `composePromises()`를 사용해야 합니다.  이는 `.then()`을 사용해 함수를 합성하는 특수한 유틸리티입니다.

```javascript
{  
  const composeM = chainMethod => (...ms) => (  
    ms.reduce((f, g) => x => g(x)[chainMethod](f))  
  );

  const composePromises = composeM('then');

  const label = 'API call composition';

  // a => Promise(b)  
  const getUserById = id => id === 3 ?  
    Promise.resolve({ name: 'Kurt', role: 'Author' }) :  
    undefined  
  ;

  // b => Promise(c)  
  const hasPermission = ({ role }) => (  
    Promise.resolve(role === 'Author')  
  );

  // Compose the functions (this works!)  
  const authUser = composePromises(hasPermission, getUserById);

  authUser(3).then(trace(label)); // true  
}
```
`composeM()`에 대해서는 나중에 다시 알아볼 것 입니다.

모나드의 핵심을 기억하십니까? :

-   함수 맵:  `a => b`
-   컨텍스트가 있는 Functor 맵:   `Functor(a) => Functor(b)` 
-   컨텍스트와 Flatten을 사용하는 모나드 맵:   `Monad(Monad(a)) => Monad(b)`

이 경우, 프라미스가 곧 모나드이기 때문에,  프라미스을 반환하는 함수들을 합성할 때  `hasPermission()`은 `User`타입  대신에  `Promise(User)`를 받게됩니다. 모나드 연산의 왼쪽 항 `Monad(Monad(a))`에서 바깥 쪽  `Monad()`  래퍼를 벗겨낼 경우 `Monad(a) => Monad(b)`가 됩니다.  이는 일반적인 functor `.map()`과 동일합니다. 즉, `Monad(x) => x` 처럼 래퍼를 벗겨낼 수 있다면 모나드 연산을 만들 수 있습니다. 

## 모나드의 구성요소

모나드는 간단한 대칭을 기반으로합니다. 즉, 값을 컨텍스트로 래핑하는 방법과 컨텍스트에서 값의 랩핑을 해제하는 방법입니다.

-   **Lift / Unit :**  어떤 타입을 모나드 컨텍스트로 리프트 :  `a => M(a)`
-   **Flatten / Join :**  컨텍스트에서 타입을 추출 :  `M(a) => a`

그리고 모나드는 펑터에 속하기 때문에 다음과 같이 맵핑 할 수도 있습니다.

-   **Map :**  컨텍스트가 유지되는 맵핑 :  `M(a) -> M(b)`

Flatten과 Map을 결합하면  [하인리히 클레이슬리](https://en.wikipedia.org/wiki/Heinrich_Kleisli)의  이름을 딴 모나드 리프팅 함수들을 합성하는 **Chain**, 일명 클레이슬리 컴포지션을 만들 수 있습니다.

-   **FlatMap / Chain :** Flatten + Map :  `M(M(a)) => M(b)`

모나드의 경우  `.map()`  메소드는 퍼블릭 API에서 생략되는 경우가 많습니다. Lift + flatten을 명시적으로  `.map()`이라고 부르지는 않습니다. 그러나 이를 만드는 일은 간단합니다.  리프트 (aka of/unit)를 한뒤 체인 (aka bind/flatMap)을 하면 `.map()`이 됩니다. 

```javascript
const MyMonad = value => ({  
  // <... insert arbitrary chain and of here ...>  
  map (f) {  
    return this.chain(a => this.constructor.of(f(a)));  
  }  
});
```

따라서   `.of()`  및  `.chain()`/`.join()`을 정의하면  `.map()`을 정의할 수 있습니다. 

리프트는 factory/constructor 이며 `constructor.of()`  메서드입니다.  카테고리 이론에서 "단위^unit^"라고 불립니다.  타입을 모나드의 컨텍스트로 감싸는 일입니다.  a를  `Monad`  of  `a`  로 바꿉니다.

Haskell에서는 리프트를 (매우 헷갈리게)`return`이라고 부르며 거의 모든 사람들이 그것을 함수의 리턴과 혼동합니다. 따라서 저는 말할 때  "들어 올리기, 승격, 리프트" 또는 "타입 리프트"라고 부르며 코드에서는  `.of()`라고 부릅니다.

값을 빼내는 프로세스 (`.chain()`에서 map이 빠진 것)는 일반적으로  `flatten()`  또는  `join()` 이라고 합니다.  `flatten()`/`join()`은  `.chain()/.flatMap()`에 포함되어 있기 때문에 완전히 생략되는 경우가 많습니다.  flattening은 합성과 관련되어 있으므로 매핑과 결합되는 경우가 많습니다.  기억하세요, unwrapping + map은  `a => M(a)` 형식의 함수들을 합성하는데 필요합니다.

어떤 종류의 모나드를 사용 하느냐에 따라 unwrapping 프로세스가 매우 간단해질 수 있습니다.  Identity 모나드의 경우, 결과 값을 다시 모나드 컨텍스트로 가져 오지 않는다는 점을 제외하고는  `.map()`과 같습니다.  래핑의 한 레이어를 버리는 효과가 있습니다.

```javascript
{ // Identity monad  
const Id = value => ({  
  // Functor mapping  
  // Preserve the wrapping for .map() by   
  // passing the mapped value into the type  
  // lift:  
  map: f => Id.of(f(value)),

  // Monad chaining  
  // Discard one level of wrapping  
  // by omitting the .of() type lift:  
  chain: f => f(value),

  // Just a convenient way to inspect  
  // the values:  
  toString: () => `Id(${ value })`  
});

// The type lift for this monad is just  
// a reference to the factory.  
Id.of = Id;
```

그러나 unwrapping 프로세스는 일반적으로 부수효과, 오류,  분기 또는 비동기 I/O와 같은 불순한 요소가 숨겨지는 부분이기도합니다.  합성은 모든 소프트웨어 개발에서 실제로 흥미로운 일들이 일어나는 곳입니다.

예를 들어 프라미스에서 `.chain()`은  `.then()`이라 불립니다. `promise.then(f)`를 호출하면  `f()`가  즉시 실행되지 않습니다.  대신 프라미스을 기다린 뒤  `f()`를  호출합니다.

예:

```javascript
{  
  const x = 20;                 // The value  
  const p = Promise.resolve(x); // The context  
  const f = n =>   
    Promise.resolve(n * 2);     // The function

  const result = p.then(f);     // The application

  result.then(  
    r => console.log(r)         // 40  
  );  
}
```

프라미스의  `.then()`은 `.chain()`과  _거의_  동일합니다.

엄격하게 따졌을 때 프라미스는 모나드가 아니라는 말을 들은적 있을 겁니다.  값이 프라미스일 경우에만 언래핑하며 그렇지 않은 경우  `.then()`은  `.map()`처럼 동작합니다.


즉, 프라미스 값과 다른 값에 대해 다르게 동작하는  `.then()`은 Functor 및  모나드가 충족시켜야 하는  수학 법칙을 엄격하게 따르는 것이 아닙니다.  현실적으로는 여러분이 이러한 원칙과 실제 작동방식을 알고있는 한, 그것들을 둘 중 하나로 취급 할 수 있습니다.  단지 일부 합성 유틸리티들로 프라미스를 합성했을 때 예상대로 작동하지 않을 수 있습니다.

## 모나드 합성하기(클레이슬리^Kleisli^ 합성)

promise-lifting 함수를 작성하는 데 사용한  `composeM`  함수에 대해 자세히 살펴 보겠습니다.

```javascript
const composeM = method => (...ms) => (  
  ms.reduce((f, g) => x => g(x)[method](f))  
);
```

이 이상한 reducer가 의미하는 것은 함수 합성의 대수적 정의입니다 :  `f(g(x))`. 더 쉽게 알아보겠습니다.

```javascript
{  
  // The algebraic definition of function composition:  
  // (f ∘ g)(x) = f(g(x))  
  const compose = (f, g) => x => f(g(x));

  const x = 20;    // The value  
  const arr = [x]; // The container

  // Some functions to compose  
  const g = n => n + 1;  
  const f = n => n * 2;

  // Proof that .map() accomplishes function composition.  
  // Chaining calls to map is function composition.  
  trace('map composes')([  
    arr.map(g).map(f),  
    arr.map(compose(f, g))  
  ]);  
  // => [42], [42]  
}
```

이는  `.map()`  메소드를 제공하는 모든 Functor(e.g., Array)에 대해 작동하는 합성 유틸리티를 작성할 수 있다는 것입니다.

```javascript
const composeMap = (...ms) => (  
  ms.reduce((f, g) => x => g(x).map(f))  
);
```

이것은 일반적인  `f(g(x))`를 약간 재구성한 것 입니다.  `a -> Functor(b)`  유형의 함수가 여러 개 있으면 각 함수를 반복하여 값  `x`를 적용  합니다. `.reduce()`  메소드는 두가지 인자, reducer(이 경우  `f`  )와 배열의 아이템 (`g`)을 받는 함수를 사용합니다.

각 반복에서 다음의  `f`가 되는 새로운 함수  ( `x => g(x).map(f)` )를 반환합니다.  우리는 이미  `x => g(x).map(f)`가  `compose(f, g)(x)`를 functor의 컨텍스트로 들어 올리는 것과 같음을 증명했습니다.  즉, 컨테이너의 값에  `f(g(x))`를 적용하는 것과 같습니다. 이 경우 배열 내의 값에 합성함수를 적용하는 것입니다.

> 성능 경고 : 배열에 대해서는 권장하지 않습니다.  이런 방식으로 함수를 합성하려면 전체 배열(수십만개의 항목이 있을지도 모르는)을 여러번 반복해야 합니다.  배열을 맵핑해야할 경우 간단한  `a -> b`  함수를 먼저 합성한 다음 한 번 매핑하거나  `.reduce()`  또는 트랜스듀서를 사용하여 반복을 최적화하십시오.

배열에 이런식의 동기, 느긋하지 않은^eager^ 방식으로 함수를 적용하는 것은 과합니다.  그러나 많은 비동기, 느긋한 방식들에 대해서 예외 나 null 값을 분기하는 것과 같은 지저분한 작업을 처리해야합니다.

바로 그럴때 모나드가 필요해집니다. 모나드는 합성 체인에서 이전의 비동기 또는 분기 동작에 의존하는 값을 처리할 수 있습니다.  이 경우 일반적인 함수 합성으로는 값을 얻을 수 없습니다.  모나드를 반환하는 작업은  `a => b` 대신  `a => Monad(b)`  형식을 하고 있습니다.

데이터를 받아 API를 실행하고 값을 리턴받아 다시 다른 API를 실행하고 해당 데이터에 대한 계산 결과를 반환하는 함수가 있다면 `a => Monad(b)`  타입의 함수들을 합성해야 합니다.  API 호출은 비동기식이므로 프라미스나 Observable과 같은 값으로 반환 값을 래핑해야합니다.  달리 말하면, 함수들의 서명은 각각  `a -> Monad(b)`6과  `b -> Monad(c)`가 됩니다.

`g: a -> b`  ,  `f: b -> c`타입의 함수들을 합성하는 것은 쉽습니다. 타입들이 정렬된 이상 `h: a -> c`는 단지  `a => f(g(a))`일  뿐이기 때문입니다.

`g: a -> Monad(b)`  ,  `f: b -> Monad(c)` 타입의 함수들을 합성하는 것은 조금 더 어렵습니다. 타입들이 정렬되지 않았고 `f`는  `Monad(b)`가  아닌  `b`를  기대하고 있기 때문에 `h: a -> Monad(c)`  는  `a => f(g(a))`로 표현될 수 없습니다.

좀 더 구체적으로 접근하기 위해 프라미스를 리턴하는 한쌍의 비동기 함수를 합성해 보겠습니다.

```javascript
{  
  const label = 'Promise composition';

  const g = n => Promise.resolve(n + 1);  
  const f = n => Promise.resolve(n * 2);

  const h = composePromises(f, g);

  h(20)  
    .then(trace(label))  
  ;  
  // Promise composition: 42  
}
```

올바른 결과가 나오도록 하려면`composePromises()`를  어떻게 만들어야야할까요?  _힌트 : 이미 본 적이 있습니다._

`composeMap()`  함수를 기억하십니까?  `.map()`을  `.then()`으로 바꾸면됩니다.  `Promise.then()`은 기본적으로 비동기  `.map()`  입니다.

```javascript
{  
  const composePromises = (...ms) => (  
    ms.reduce((f, g) => x => g(x).then(f))  
  );

  const label = 'Promise composition';

  const g = n => Promise.resolve(n + 1);  
  const f = n => Promise.resolve(n * 2);

  const h = composePromises(f, g);

  h(20)  
    .then(trace(label))  
  ;  
  // Promise composition: 42  
}
```

두 번째 함수  `f`(  `g`  다음에 오는)가 받게되는 입력 값은 프라미스입니다. 하지만 `f`가 원하는 타입은 `Promise(b)`가 아니라 `b`였습니다.  무슨 일이 일어난걸까요?

`.then()`  내부에는  `Promise(b) -> b`  를 하는 언래핑 프로세스가 있습니다.  이 작업이 바로  `join`  또는  `flatten`입니다.

즉, `composeMap()`  및  `composePromises()`가 거의 동일한 함수임을 알 수 있습니다.  이 두 가지를 모두 처리 할 수있는 고차 함수를 만들어 보겠습니다.  chain 메서드를 커링하기 위해 대괄호 표기법을 사용하겠습니다.

```javascript
const composeM = method => (...ms) => (  
  ms.reduce((f, g) => x => g(x)[method](f))  
);
```

이제 다음과 같은 특수한 구현들을 만들 수 있습니다.

```javascript
const composePromises = composeM('then');  
const composeMap = composeM('map');  
const composeFlatMap = composeM('flatMap');
```

## 모나드의 법칙

여러분이 어떤 모나드를 만들기 전에 모든 모나드가 충족시켜야 할 세가지 법칙을 알아보겠습니다.

1.  Left identity :  `unit(x).chain(f) ==== f(x)`
2.  Right identity :  `m.chain(unit) ==== m`
3.  결합법칙^Associativity^ :  `m.chain(f).chain(g) ==== m.chain(x => f(x).chain(g))`

### Identity laws

![](https://cdn-images-1.medium.com/max/1600/1*X_bUJJYudP8MlhN0FLEGKg.png)

Left and right identity

모나드는 Functor입니다.  Functor는  `A -> B`라는  카테고리 사이의 사상입니다.  사상은 화살표로 표시됩니다.  객체들 간에 명시적으로 표시된 화살표들 외에도 카테고리의 각 객체에 화살표가 있습니다.  즉, 카테고리의 모든 객체  `X`  에 대해 화살표  `X -> X`가  있습니다.  이 화살표는 ID 화살표로 알려져 있으며 일반적으로 객체 자신을 가리키는 작은 원형 화살표로 그려집니다.

![](https://cdn-images-1.medium.com/max/1600/1*3jcLj7wdwWaUJ22X2iT7OA.png)

항등 사상

### 결합법칙

연산을 할 때 괄호를 어디에 두어도 상관이 없다는 의미입니다.  덧셈의 경우  `a + (b + c)`는  `(a + b) + c`와 결과가 같습니다.  함수 합성에 대해서도 마찬가지입니다.  `(f ∘ g) ∘ h = f ∘ (g ∘ h)`  .

Kleisli 합성에서도 마찬가지입니다. 대신 거꾸로 읽어야합니다.  합성 연산자( `chain` )를 보게되면 다음을 떠올리세요.

```javascript
h(x).chain(x => g(x).chain(f)) ==== (h(x).chain(g)).chain(f)
```

### 모나드 법칙 증명하기

Identity 모나드가 모나드 법칙을 충족시킨다는 것을 증명해보겠습니다.

```javascript
{ // Identity monad  
  const Id = value => ({  
    // Functor mapping  
    // Preserve the wrapping for .map() by   
    // passing the mapped value into the type  
    // lift:  
    map: f => Id.of(f(value)),

    // Monad chaining  
    // Discard one level of wrapping  
    // by omitting the .of() type lift:  
    chain: f => f(value),

    // Just a convenient way to inspect  
    // the values:  
    toString: () => `Id(${ value })`  
  });

  // The type lift for this monad is just  
  // a reference to the factory.  
  Id.of = Id;

  const g = n => Id(n + 1);  
  const f = n => Id(n * 2);

  // Left identity  
  // unit(x).chain(f) ==== f(x)  
  trace('Id monad left identity')([  
    Id(x).chain(f),  
    f(x)  
  ]);  
  // Id monad left identity: Id(40), Id(40)  
  

  // Right identity  
  // m.chain(unit) ==== m  
  trace('Id monad right identity')([  
    Id(x).chain(Id.of),  
    Id(x)  
  ]);  
  // Id monad right identity: Id(20), Id(20)

  // Associativity  
  // m.chain(f).chain(g) ====  
  // m.chain(x => f(x).chain(g)    
  trace('Id monad associativity')([  
    Id(x).chain(g).chain(f),  
    Id(x).chain(x => g(x).chain(f))  
  ]);  
  // Id monad associativity: Id(42), Id(42)  
}
```

## 결론

모나드는 타입 리프팅 함수( `g: a => M(b)`  ,  `f: b => M(c)`  )를 합성하는 방법을 기술한 인터페이스입니다.  이를 위해 모나드는  `f()`를 적용하기 전  `M(b)`를  `b`로  flatten합니다.  즉, funtor는 맵핑할  수 있는 것이며 모나드는 flatten한 뒤 맵핑할 수 있는 것입니다. :

-   함수 맵:  `a => b`
-   컨텍스트가 있는 Functor 맵:   `Functor(a) => Functor(b)` 
-   컨텍스트와 Flatten을 사용하는 모나드 맵:   `Monad(Monad(a)) => Monad(b)`


모나드는 간단한 대칭성을 기반으로합니다. 즉, 값을 컨텍스트로 래핑하는 방법과 컨텍스트에서 래핑을 해제하는 방법입니다.

-   Lift / Unit : 어떤 값을 모나드 컨텍스트로 타입 리프트 :  `a => M(a)`
-   Flatten / Join : 컨텍스트에서 값을 언래핑 :  `M(a) => a`

그리고 모나드는 Functor이기 때문에 다음과 같이 맵핑 할 수도 있습니다.

-   Map : 컨텍스트가 유지되는 맵핑 :  `M(a) -> M(b)`

Flatten과 map을 결합하면 리프팅 함수들을 합성하기위한 chain, 일명 Kleisli 합성을 만들 수 있습니다.

-   FlatMap / Chain : Flatten + map :  `M(M(a)) => M(b)`

모나드는 3 개의 법칙(공리)을 만족해야하며 이는 모나드 법칙으로 불립니다.

-   Left identity :  `unit(x).chain(f) ==== f(x)`
-   Right identity :  `m.chain(unit) ==== m`
-   결합법칙 :  `m.chain(f).chain(g) ==== m.chain(x => f(x).chain(g)`

프라미스, 옵저버블 등 여러분은 매일매일 JavaScript에서 모나드를 사용하고 있습니다.  Kleisli 합성을 사용하면 데이터 타입 API의 세부 사항을 신경쓰지 않고 데이터가 흘러가는 로직을 만들 수 있습니다. 

따라서 모나드는 코드를 단순화하는 매우 강력한 도구입니다.  모나드를 사용하기 위해 내부에서 무슨 일이 벌어지고 있는지 이해하거나 걱정할 필요는 없습니다. 다만, 이제는 모나드에 대해 더 많이 알게었고, 그 속에서 무슨일이 일어나는지 더이상 두려워하지 않게 되었습니다.

레이디 모나드린의 저주를 두려워 할 필요가 없습니다.

[**다음: Mocking은 코드 냄새(Code Smell)입니다 >**](https://midojeong.github.io/2018/04/19/mocking-is-a-code-smell/)