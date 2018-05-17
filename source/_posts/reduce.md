---
title: Reduce
catalog: true
date: 2018-04-01 06:56:14
subtitle: "함수형 프로그래밍의 강력한 도구"
header-img: "bg.jpg"
readingTime: 6
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 함수형 프로그래밍에서 자주 보이는 Reduce(일명 fold, accumulate)는 배열을 순회하며 각 항목을 누적해서 리턴하는 함수입니다. 이 때 누적된 값을 변수로 저장해놓고 배열의 항목과 누적 값을 어떤 함수에 반복해서 전달합니다. 그 함수는 새로운 누적 값을 리턴하는 임의의 함수입니다. reduce를 사용하여 유용한 기능들을 구현할 수 있는데, 이는 보통 어떤 아이템 콜렉션을 가지고 중요한 계산을 수행하는 가장 우아한 방법입니다. reduce는 reducer 함수와 초기 값을 인자로 받고 누적 값을 리턴합니다. Array.prototype.reduce()에서 배열은 this로 참조할 수 있기 때문에 인자로 넣어줄 필요가 없습니다.
---


> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/reduce-composing-software-fe22f0c39a1d)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/03/29/higher-order-functions/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/04/07/functors-and-categories/)


함수형 프로그래밍에서 자주 보이는  **Reduce**  (일명 : fold, accumulate)는 배열을 순회하며 각 항목을 누적해서 리턴하는 함수입니다. 이 때 누적된 값을 변수로 저장해놓고 배열의 항목과 누적 값을 어떤 함수에 반복해서 전달합니다. 그 함수는 새로운 누적 값을 리턴하는 임의의 함수입니다. reduce를 사용하여 유용한 기능들을 구현할 수 있는데, 이는 보통 어떤 아이템 콜렉션을 가지고 중요한 계산을 수행하는 가장 우아한 방법입니다.

reduce는 reducer 함수와 초기 값을 인자로 받고 누적 값을 리턴합니다. `Array.prototype.reduce()`에서 배열은  `this`로 참조할 수 있기 때문에 인자로 넣어줄 필요가 없습니다.
```
array.reduce(  
  reducer: (accumulator: Any, current: Any) => Any,  
  initialValue: Any  
) => accumulator: Any
```
배열의 총합을 구해보겠습니다.
```javascript
[2, 4, 6].reduce((acc, n) => acc + n, 0); // 12
```
배열의 각 요소가 현재 값이 되어 누적 값과 함께 reducer에 전달됩니다. reducer의 역할은 어떻게든 현재 값을 누적 값으로 "폴드"하는 것입니다.  reducer 함수의 역할은 누적되는 방식을 정의하는 것 입니다.  reducer가 새 누적 값을 리턴한 후  `reduce()`는 배열의 다음 값으로 이동합니다.  reducer는 누적될 초기 값이 필요하며 이는 매개 변수로 전달됩니다.

위 코드에서 총합을 계산하는 reducer가 처음 호출되면  `acc`에  `0`  (두 번째 매개 변수로  `.reduce()`  전달 된 값)이 할당됩니다.  reducer는  `0`  +  `2` (배열의 첫 번째 요소)를 계산하여  `2`를 리턴합니다.  다음 호출에는  `acc = 2, n = 4`가 되며 reducer는  `2 + 4`( `6` )를 리턴합니다.  마지막 반복에서는  `acc = 6, n = 6`이 되고 reducer는  `12`반환합니다.  반복이 완료되면  `.reduce()`는 최종 누적 값  `12`리턴합니다.

이 경우 익명 함수를 전달했지만 기명 함수를 전달 할 수도 있습니다.
```javascript
  const summingReducer = (acc, n) => acc + n;   
  [2, 4, 6].reduce(summingReducer, 0);  // 12 
```
일반적으로  `reduce()`는 왼쪽에서 오른쪽으로 작동합니다.  JavaScript에는 그 반대로 작동하는  `[].reduceRight()`도 있습니다.  즉,  `[2, 4, 6]`에  `.reduceRight()`을 적용하면 `n`은  `6`으로 시작해서  `2`로 끝납니다.

## 다양한 용도로 사용할 수 있습니다.

Reduce는 다재다능합니다.  reduce로  `map()`  ,  `filter()`  ,  `forEach()` 등 다양한 함수들을 쉽게 정의 할 수 있습니다.

**Map:**
```javascript
  const map = (fn, arr) => arr.reduce((acc, item, index, arr) => {   
  return acc.concat(fn(item, index, arr));   
  }, []); 
```
map의 누적 값은 새로운 배열이 됩니다.  새 값은 `arr`의 각 요소에 맵핑 함수 (`fn`)을 적용한 값입니다. 즉, 현재 요소에  `fn`을 적용한 결과를 새 배열 `acc`에 추가하는 방식으로 누적합니다.

**Filter:**
```javascript
  const filter = (fn, arr) => arr.reduce((newArr, item) => {  
  return fn(item) ? newArr.concat([item]) : newArr;  
}, []);
```
필터는 predicate함수를 사용하고 요소가 조건에 맞을경우 (`fn(item)`이  `true`를 리턴 함) 새 배열에 추가합니다. 맵과 거의 동일하게 작동합니다.

여러분에게 임의의 데이터 리스트가 주어진다면 위 예제들을 활용해서 데이터들을 필터링하고 함수를 적용하고 결과를 특정한 값으로 누적할 수 있습니다. 수많은 응용 프로그램애플리케이션들이 이러한 방식으로 데이터를 다룹니다. 만약 데이터가 값이 아닌 함수일 경우는 어떻게 해야 할까요?

**Compose:**

Reduce를 사용하면 손쉽게 함수를 합성할 수도 있습니다.  함수 합성 :  `x`에  `g`를 적용하고 다시 그 결과에 함수  `f`  를 적용하는 것을  `f . g`라고 하며 JavaScript에서 다음처럼 표현됩니다.
```javascript
  f(g(x)) 
```
Reduce를 사용하면 합성 과정을 추상화해서 다음과 같은 함수를 쉽게 정의할 수 있는데, 
```javascript
  f(g(h(x))) 
```
그렇게하기 위해서는 reduce를 역으로 실행해야합니다.  즉, 왼쪽에서 오른쪽 방향이 아닌 오른쪽에서 왼쪽 방향입니다.  다행히 JavaScript에는  `.reduceRight()`메소드가 있습니다.
```javascript
  const compose = (...fns) => 
                  x => fns.reduceRight ((v, f) => f (v), x); 
```
> 참고 : JavaScript엔진 버전이  `[].reduceRight()`를  지원하지 않을 경우  `reduce()`를  사용하여  `reduceRight()`를  구현할 수 있습니다.  그 방법은 찾아내는 것은 여러분들에게 맡기겠습니다.

**Pipe:**

`compose()`는 안에서-밖으로 즉 수학 표기법으로 함수를 합성하려는 경우 유용합니다.  그러나 당신이 함수가 순서대로 적용되는 것을 일련의 사건들로 생각하고 싶다면 어떨까요?

어떤 숫자에  `1`을 더한 다음 두 배로하고 싶다고 상상해보십시오를 하려면 어떻게 해야 할까요. `compose()`를 사용하면 다음처럼 됩니다.
```javascript
const add1 = n => n + 1;  
const double = n => n * 2;

const add1ThenDouble = compose(  
  double,  
  add1  
);

add1ThenDouble(2); // 6  
// ((2 + 1 = 3) * 2 = 6)
```
문제점이 보입니까?  첫 번째 단계가 마지막에 나열되므로 함수가 적용되는 순서를 이해하려면 목록의 맨 아래에서 시작하여 위쪽으로 읽어야합니다.

따라서 이 문제를 해결하려면 오른쪽에서 왼쪽으로 reduce하지 않고 평소처럼 왼쪽에서 오른쪽으로하면 됩니다.
```javascript
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);
```
이제 다시  `add1ThenDouble()`를 만들어 보겠습니다.
```javascript
const add1ThenDouble = pipe(  
  add1,  
  double  
);

add1ThenDouble(2); // 6  
// ((2 + 1 = 3) * 2 = 6)
```
합성의 순서가 달라지면에 따라 다른 결과가 나올 수 있기 때문에 중요합니다.
```javascript
const doubleThenAdd1 = pipe(  
  double,  
  add1  
);

doubleThenAdd1(2); // 5
```
나중에  `compose()`및  `pipe()`에 대해 자세히 설명하겠습니다.  지금 당장은 `reduce()`가 매우 강력한 도구이며, 실제로 그것을 잘 익혀야 한다는 것만 이해하면 됩니다. reduce가 매우 까다로울 수 있습니다.  하지만 어떤 사람들은 전혀 따라 가지도 못한다는 것을 명심하십시오.

## Redux에 관하여

Redux에서 중요 상태 업데이트 비트를 설명하는 데 "reducer"라는 용어를 사용합니다.  이 글을 쓰는 시점에서, Redux는 React와 Angular(`ngrx/store`의 후속작)를 사용하여 구축 된 웹 어애플리케이션을 위한 가장 인기있는 상태 관리 라이브러리 / 아키텍처입니다.

Redux는 reducer함수를 사용하여 애플리케이션 상태를 관리합니다.  Redux 스타일 reducer는 현재 상태와 액션 오브젝트를 받아서 새 상태를 리턴합니다.
```
reducer(state: Any, action: { type: String, payload: Any}) 
   => (newState: Any)
```
Redux에는 다음과 같은 몇 가지 reduce 규칙이 있습니다.

1.  매개 변수없이 호출 된 reducer는 초기 상태를 그대로 리턴해야합니다.
2.  reducer가 액션을 처리하지 않으면 상태를 리턴해야합니다.
3.  Redux reducer는 **순수 함수**여야합니다.

덧셈 reducer를 Redux 스타일 reducer로 다시 작성해 보겠습니다.
```javascript
const ADD_VALUE = 'ADD_VALUE';

const summingReducer = (state = 0, action = {}) => {  
  const { type, payload } = action;

  switch (type) {  
    case ADD_VALUE:  
      return state + payload.value;  
    default: return state;  
  }  
};
```
Redux의 reducer는  또한 `[].reduce()`포함하여 reducer 함수의 서명를 잘 따르는  `reduce()` 구현에 연결할 수 있습니다. 즉, 일련의 액션 오브젝트 배열을 만들어 reduce하면 상태의 스냅샷을 확보 할 수 있습니다. 
```javascript
const actions = [  
  { type: 'ADD_VALUE', payload: { value: 1 } },  
  { type: 'ADD_VALUE', payload: { value: 1 } },  
  { type: 'ADD_VALUE', payload: { value: 1 } },  
];

actions.reduce(summingReducer, 0); // 3
```
따라서 Redux 스타일 reducer는 유닛테스트가 매우 쉽습니다.
## 결론

당신은 reduce가 매우 유용하고 다재다능한 추상화라는 것을 알아야합니다. 맵이나 필터보다 이해하기가 약간 까다롭긴 해도 함수형 프로그래밍의 필수 도구입니다. 다른 많은 훌륭한 도구를 만드는 데 사용할 수 있습니다.

[**다음: Functor와 카테고리 >**](https://midojeong.github.io/2018/04/07/functors-and-categories/)