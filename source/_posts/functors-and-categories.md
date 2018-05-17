---
title: Functors와 카테고리
catalog: true
date: 2018-04-07 14:43:48
subtitle: Functors and categories
header-img: "bg.jpg"
readingTime: 8
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: Functor는 사용자가 맵핑 할 수있는 데이터 타입입니다.  내부의 값에 함수를 적용하는 인터페이스가 있는 컨테이너입니다.  functor를 발견하면  "mappable" 한 무언가라고 생각하면 됩니다. functor 타입은 일반적으로 객체처럼 구현되며 구조를 유지한채 입력에서 출력으로 맵핑하는  `.map()`  메소드를 가집니다.  이 때 "구조 유지"란 동일한 유형의 functor를 리턴한다는 것을 의미합니다 (컨테이너 내부의 값은 다른 유형 일 수 있음).functor는 무언가를 담을 수 있는 상자^box^와 맵핑^mapping^ 인터페이스를 제공합니다.  배열^Array^은 functor의 좋은 예이며 promise, 스트림, 트리 등 다양한 종류의 객체 또한 _"mappable"_ 한 것들입니다. JavaScript에 내장 된 배열 및 promise 객체는 functor처럼 작동합니다. 콜렉션(배열, 스트림 등)은 일반적으로  `.map()`을 사용해서 콜렉션을 순회하며 주어진 함수를 각 값에 적용하지만 모든 functor가 콜렉션처럼 순회하지는 않습니다. 사실 functor는 특정 문맥^context^에서 함수를 적용하는 것에 관한 것입니다.
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/functors-categories-61e031bac53f)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/03/31/reduce/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/04/07/functional-mixins/)

**Functor는**^[펑터라고 발음합니다. 함수자 혹은 함자로 번역이 되는데 그보다는 단어를 그대로 사용하기로 결정했습니다 - 역자] 사용자가 맵핑 할 수있는 **데이터 타입**입니다.  내부의 값에 함수를 적용하는 인터페이스가 있는 **컨테이너**입니다.  functor를 발견하면  _"mappable"_ 한 무언가라고 생각하면 됩니다. functor 타입은 일반적으로 객체처럼 구현되며 구조를 유지한채 입력에서 출력으로 맵핑하는  `.map()`  메소드를 가집니다.  이 때 "구조 유지"란 동일한 유형의 functor를 리턴한다는 것을 의미합니다 (컨테이너 내부의 값은 다른 유형 일 수 있음).

functor는 무언가를 담을 수 있는 상자^box^와 맵핑^mapping^ 인터페이스를 제공합니다.  배열^Array^은 functor의 좋은 예이며 promise, 스트림, 트리 등 다양한 종류의 객체 또한 _"mappable"_ 한 것들입니다. JavaScript에 내장 된 배열 및 promise 객체는 functor처럼 작동합니다. 콜렉션(배열, 스트림 등)은 일반적으로  `.map()`을 사용해서 원소들을 순회하며 주어진 함수를 각 값에 적용하지만 모든 functor가 콜렉션처럼 순회하지는 않습니다. 사실 functor란 특정 문맥^context^에서 함수를 적용하는 것에 관한 개념입니다.

Promise는  `.map()`  대신  `.then()`을 사용합니다.  일반적으로  `.then()`을 비동기식  `.map()`메서드로 생각할 수 있습니다. 단, 중첩된 promise가 있는 경우는 예외이며, 자동으로 외부 promise를 처리하지 않습니다.  다시 말하자면 promise가 아닌 값에 대해서  `.then()`은 비동기 `.map()`과 같은 역할을 합니다. 반면에 promise값의 경우  `.then()`은 모나드의 `.chain()` 메서드(`.bind()`  또는  `.flatMap()`이라고도 함)처럼 동작합니다.   따라서 promise은 functor가 아니고 모나드도 아닙니다. 그러나 실제로는 그 둘 중 하나로 취급 할 수 있습니다. 모나드가 무엇인지 몰라도 걱정하지 마십시오. 모나드는 일종의 functor이며, 우리는 먼저 functor에 대해 알아볼 것입니다.

다양한 것들을 functor로 만들어주는 라이브러리들이 있습니다.

Haskell에서 functor 타입은 다음과 같이 정의됩니다.
```
 fmap :: (a -> b) -> fa -> fb
```
a를 받아 b를 리턴하는 함수를 인자로 받습니다. 그리고 a가 담긴 functor를 받아 b가 담긴 functor를 리턴합니다.  `fa`  와  `fb`는  "a의 functor",  "b의 functor"로 읽을 수 있습니다. 즉,  `fa`에는 `a`가 담긴 상자가 있고  `fb`에는 `b`가 담긴 상자가 있습니다.

functor를 사용하는 것은 간단합니다.  `map()`을 호출하면 됩니다.
```javascript
  const f = [1, 2, 3];   
  f.map(double);  //[2, 4, 6] 
```
## Functor's Law

카테고리^[수학의 범주론에 나오는 개념입니다. 이에 대해 [Eugenia Cheng](https://www.amazon.com/s/ref=dp_byline_sr_book_1?ie=UTF8&text=Eugenia+Cheng&search-alias=books&field-author=Eugenia+Cheng&sort=relevancerank)의 How To Bake PI라는 비교적 쉽게 쓰인 입문서가 있습니다. -역자] 에는 두 가지 중요한 속성이 있습니다.

1.  항등^identity^
2.  합성^composition^

functor는 카테고리들 사이의 맵핑이기 때문에, functor는 항등과 합성을 지원해야 합니다. 이 두가지는 functor의 법칙으로 알려져 있습니다.

### 항등Identity

임의의 functor `f`에 항등함수(`x => x`)를 맵핑시키면 동일한 `f`가 리턴되어야 합니다: 
```javascript
  const f = [1, 2, 3];   
  f.map(x => x);  // [1, 2, 3] 
```
### 합성Composition

functor는 합성이 가능해야 합니다.  `F.map(x => f(g(x)))`  는  `F.map(g).map(f)`  와 동일합니다.

```
Functor.map( f . g ) === Functor.map(g).map(f)
```

함수 합성이란 어떤 함수의 출력을 다른 함수에 넣는 것입니다. 예를 들어,  인수로 `x`를 가지는 함수 `f`와 `g`가 합성된 `(f ∘ g)(x)`는 `f(g(x))`를 의미합니다.   

함수형 프로그래밍에 나오는 용어는 대부분 범주론^category^ ^theory^에서 왔습니다. 범주론의 핵심은 합성입니다.  범주론이 처음에는 무서워보일 수 있지만 알고보면 쉽습니다. 다이빙 보드에서 뛰어 내리거나 롤러 코스터를 타는 것과 같습니다.  다음은 범주론의 몇 가지 중요한 핵심과 이론적 기초입니다.

-   카테고리는 객체와 객체들간의 화살표의 모음입니다. ( "객체"은 문자 그대로 객체^object^ 입니다)^[이때 객체란 어떤 실행 프로세스 내부의 추상화된 인스턴스가 아니라 사물, 어떤 것이라고 이해하면 됩니다. -역자]
-   화살표는 사상^morphism^입니다^[맵핑과 동일합니다. -역자]. 사상은 코드에서 함수로 구현됩니다.
-   객체들이 `a -> b -> c`처럼 연결됐을 때  합성을 통해 `a -> c`로 직접 맵핑시킬 수 있어야 합니다.
-   모든 화살표는 컴포지션으로 나타낼 수 있습니다 (단지 객체 자신을 가리키는 항등 화살표일지라도).  카테고리의 모든 객체에는 항등 화살표가 있습니다.

`a`를 취하여  `b` 를 리턴하는 함수  `g`  가 있고  `b`를 취하여  `c`리턴하는 또 다른 함수  `f`가 있을 때  `f`  와  `g`의 합성을 나타내는 함수  `h`도 있어야합니다.  그러므로  `a -> c`는  `f ∘ g`라는 합성 (`f`  _after_  `g`)이며 `h(x) = f(g(x))`와 같습니다.  함수는 왼쪽에서 오른쪽으로 합성되지 않고 오른쪽에서 왼쪽으로 되기 때문에  `f ∘ g`  는 종종  `f`  _after_  `g`라고 읽습니다.

합성은 **결합법칙**^associative^ ^law^이 적용됩니다.  간단하게 말하자면 함수를 합성할 때 기본적으로 괄호가 필요 없다는 뜻 입니다.
```
 h∘(g∘f) = (h∘g)∘f = h∘g∘f
```
 JavaScript 코드로 합성을 다시 한 번 살펴 보겠습니다.

`F`라는 functor가 있을 때:

```javascript
  const F = [1, 2, 3]; 
```
다음 두 줄의 코드는 같은 표현입니다.
```javascript
F.map(x => f(g(x)));

// is equivalent to...

F.map(g).map(f);
```
## Endofunctors^[functor에 붙은 접두사 endo-는 "inside, within, internal,"라는 뜻을 가지고 있습니다. 즉, 닫힌계라고 생각하시면 됩니다. 엔도펑터라고 발음합니다. -역자]

endofunctor는 카테고리에서 다시 같은 카테고리로 맵핑되는 functor입니다.

Functor는 카테고리에서 카테고리로 맵핑 할 수 있습니다.  `X -> Y`

endofunctor는 동일한 카테고리로 맵핑합니다.  `X -> X`

모나드는 endofunctor입니다.  기억나십니까^[전 글 [왜 자바스크립트로 함수형 프로그래밍을 배우는가](https://midojeong.github.io/2018/03/24/why-learn-functional-programming-in-javascript/)에 나옵니다]:

> _"모나드는 endofunctor라는 카테고리에 속한 한 monoid에 불과해. 뭐가 문제야?"_
> _“A monad is just a monoid in the category of endofunctors. What’s the problem?”_

그 말이 조금 더 이해되었기를 바랍니다. monoid와 monads에 대해선 나중에 알아볼 것입니다.

## Functor 구현하기

여기 간단한 functor가 하나 있습니다 :

```javascript
const Identity = value => ({  map: fn => Identity(fn(value))});
```
아래 코드를 보면 `Identity`가 functor 법칙을 만족시키는걸 알 수 있습니다 :
```javascript
// trace() is a utility to let you easily inspect  
// the contents.  
const trace = x => {  
  console.log(x);  
  return x;  
};

const u = Identity(2);

// Identity law  
u.map(trace);             // 2  
u.map(x => x).map(trace); // 2

const f = n => n + 1;  
const g = n => n * 2;

// Composition law  
const r1 = u.map(x => f(g(x)));  
const r2 = u.map(g).map(f);

r1.map(trace); // 5  
r2.map(trace); // 5
```
이제 배열을 맵핑하는 것처럼 어떤 데이터 타입이라도 맵핑 할 수 있습니다. 좋군요!

이는 JavaScript로 구현한 **가장** 단순한 functor입니다. 그러나 JavaScript의 데이터 타입들이 지원하는 몇 가지 기능이 빠져 있습니다. 그것들을 추가합시다.  `+`  연산자가 숫자와 문자열 값을 둘 다 처리 할 수 ​​있다면 멋지지 않겠습니까?

따라서 우리가해야 할 일은  `.valueOf()`  구현하는 것이며  `.valueOf()`는 또한 functor에서 값을 푸는^unwrap^ 편리한 방법처럼 보입니다 :
```javascript
const Identity = value => ({  
  map: fn => Identity(fn(value)),

  valueOf: () => value,  
});

const ints = (Identity(2) + Identity(4));  
trace(ints); // 6

const hi = (Identity('h') + Identity('i'));  
trace(hi); // "hi"
```
> _valueOf 메소드는 식이 평가될 때 자동으로 호출됩니다. -역자_

좋습니다.  그러나 콘솔에서  `Identity` 인스턴스를 검사하려면 어떻게해야할까요? `> "Identity(value)"`이라고 프린트되면 멋질 것입니다.  `.toString()`  메소드를 추가해봅시다.
```javascript
toString: () => `Identity(${value})`,
```
괜찮네요. 이제 JS의 표준 순회 프로토콜을 구현하겠습니다.  커스텀 반복자를 추가하면됩니다 :
```javascript
[Symbol.iterator]: function* () {  
  yield value;  
}
```

이제 다음과 같은 작업을 수행 할 수 있습니다.

```javascript
// [Symbol.iterator] enables standard JS iterations:  
const arr = [6, 7, ...Identity(8)];  
trace(arr); // [6, 7, 8]
```
만약 `Identity(n)`을 받아  `n + 1`  ,  `n + 2`등을 포함하는 `Identity` 배열을 리턴하려면 어떻게 해야 합니까? 참 쉽죠 ?
```javascript
const fRange = (  
  start,  
  end  
) => Array.from(  
  { length: end - start + 1 },  
  (x, i) => Identity(i + start)  
);
```
> _Array.from은 첫번째 인자로 유사배열( length속성이 있는 객체)를 받고 두번째 인자(옵션)로 생성시 맵핑할 함수를 받습니다. -역자_

자 이제, 만약 `fRange`가 임의의 functor에 대해 기능하게 하고싶습니다.  데이터 타입의 각 인스턴스가 생성자`constructor`에 대한 참조를 가져야 한다는 스펙이 있다면 어떨까요?  바꿔봅시다 :
```javascript
const fRange = (  
  start,  
  end  
) => Array.from(  
  { length: end - start + 1 },  
    
  // change `Identity` to `start.constructor`  
  (x, i) => start.constructor(i + start)  
);

const range = fRange(Identity(2), 4);  
range.map(x => x.map(trace)); // 2, 3, 4
```
값이 functor인지 테스트 하려면 어떻게해야할까요?  이를 위해  `Identity`에 `is(x)`라는 정적 메소드와 `.toString()` 정적 메소드를 하나 추가해줍니다.
```javascript
Object.assign(Identity, {  
  toString: () => 'Identity',  
  is: x => typeof x.map === 'function'  
});
```
```javascript
const a = Identity(5);
Identity.is(a) // true;
```

이 모든 것들을 하나로 합쳐보겠습니다.

```javascript
const Identity = value => ({  
  map: fn => Identity(fn(value)),  
  valueOf: () => value,  
  toString: () => `Identity(${value})`,  
  [Symbol.iterator]: function* () {  
    yield value;  
  },  
  constructor: Identity  
});

Object.assign(Identity, {  
  toString: () => 'Identity',  
  is: x => typeof x.map === 'function'  
});
```
functor 나 endofunctor에 속하기 위해 위 코드들이 전부 필요하지는 않습니다. 편의성을 위해 추가한 것들일 뿐입니다.  functor가 되기 위해서는 functor법칙 두가지를 충족 시키는 `.map()` 인터페이스만 있으면 됩니다.

## 왜 Functors를 사용할까요?

Functor를 사용하는데는 여러 이유가 있습니다. 무엇보다 중요한 것은 다양한 데이터 유형에 대해 작동하는 공통 인터페이스를 구현하는 것입니다.  예를 들어, functor내의 값이  `undefined`거나  `null`이 아닌 경우에만 연산이 되게 하려면 어떡해야 할까요?
```javascript
// Create the predicate  
const exists = x => (x.valueOf() !== undefined 
                  && x.valueOf() !== null);

const ifExists = x => ({  
  map: fn => exists(x) ? x.map(fn) : x  
});

const add1 = n => n + 1;  
const double = n => n * 2;

// Nothing happens...  
ifExists(Identity(undefined)).map(trace);  
// Still nothing...  
ifExists(Identity(null)).map(trace);

// 42  
ifExists(Identity(20))  
  .map(add1)  
  .map(double)  
  .map(trace)  
;
```
마지막으로 함수형 프로그래밍의 주요 관심사는 작은 함수들을 조합하여 높은 수준으로 추상화된 코드를 작성하는 것입니다.  따라서 어떤 functor에서도 작동하는 _generic_ `map`을 만들어 보겠습니다. `fn` 함수를 인수로 부분적용해서 functor를 받는 새 함수를 리턴하게 하면 됩니다.

쉽습니다. 좋아하는 `auto-curry`라이브러리를 가져오거나 이전에 사용했던 마법을 쓰면 됩니다.
```javascript
const curry = (  
  f, arr = []  
) => (...args) => (  
  a => a.length === f.length ?  
    f(...a) :  
    curry(f, a)  
)([...arr, ...args]);
```
이제 `map`을 우리가 원하는데로 다룰 수 있습니다.
```javascript
 const map = curry((fn, F) => F.map(fn));

 const double = n => n * 2;

 const mdouble = map(double);   
 mdouble(Identity(4)).map(trace); // 8
```
### 결론

Functor는 우리가 맵핑할 수 있는 것들 입니다. 형식적으로 말하자면, functor는 카테고리에서 카테고리로의 맵핑입니다.  어떤 functor는 카테고리에서 다시 같은 카테고리로 맵핑 될 수 있습니다. (_endofunctor_)

카테고리는 객체의 집합이며 객체를 연결하는 화살표가 있습니다.  화살표는 morphisms(혹은 함수 또는 조합^[composition을 여기서는 조합이라 번역했습니다])을 뜻합니다. 카테고리의 각 객체는 항등 morphism(`x => x`)을 가집니다.  `A -> B -> C`로 객체가 사상될 경우,  `A -> C`로의 연결이 있어야합니다.

functor는 모든 데이터 유형에서 작동하는 다양한 공용 함수를 만들 수있는 훌륭한 고차원 추상화입니다.

[**다음: 함수형 믹스인 >**](https://midojeong.github.io/2018/04/07/functional-mixins/)