---
title: 고차 함수
catalog: true
date: 2018-03-29 13:48:39
subtitle: "Higher Order Functions"
header-img: "bg.jpg"
readingTime: 13
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 고차 함수는 함수를 인수로 받거나 함수를 리턴하는 함수입니다.  반면에 1차 함수는 함수를 인수로 사용하거나 함수를 출력으로 리턴하지 않습니다. 이전 글에서 우리는  .map()  과  .filter() 예제를 보았습니다.  둘 다 인수로 함수를 사용합니다.  즉, 둘 다 고차 함수입니다. 단어 목록에서 네 글자로 이루어진 단어를 선택하는 1차 함수의 예를 살펴 보겠습니다. 이제 's'로 시작하는 모든 단어를 선택하려면 어떻게 해야 할까요? 또 다른 함수를 만들면 됩니다 딱봐도 두 함수가 동일한 코드를 많이 반복하고 있습니다. 코드를 더 일반화된 해결책으로 추상화하는 패턴이 있습니다.  두 함수는 공통점이 많습니다.  둘 다 목록을 순회^iterate^하고 주어진 조건으로 필터링합니다.
---


> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99)



![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/03/28/a-functional-programmers-introduction-to-javascript/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/03/31/reduce/)


**고차 함수^higher^ ^order^ ^function^**  는 함수를 인수로 받거나 함수를 리턴하는 함수입니다.  반면에 1차 함수^first^ ^order^ ^function^는 함수를 인수로 사용하거나 함수를 출력으로 리턴하지 않습니다.

이전 글에서 우리는  `.map()`  과  `.filter()` 예제를 보았습니다.  둘 다 인수로 함수를 사용합니다.  즉, 둘 다 고차 함수입니다.

단어 목록에서 네 글자로 이루어진 단어를 선택하는 1차 함수의 예를 살펴 보겠습니다.

```javascript
const censor = words => {  
  const filtered = [];  
  for (let i = 0, { length } = words; i < length; i++) {  
    const word = words[i];  
    if (word.length !== 4) filtered.push(word);  
  }  
  return filtered;  
};

censor(['oops', 'gasp', 'shout', 'sun']);  
// [ 'shout', 'sun' ]
```
이제 's'로 시작하는 모든 단어를 선택하려면 어떻게 해야 할까요? 또 다른 함수를 만들면 됩니다 :

```javascript
const startsWithS = words => {  
  const filtered = [];  
  for (let i = 0, { length } = words; i < length; i++) {  
    const word = words[i];  
    if (word.startsWith('s')) filtered.push(word);  
  }  
  return filtered;  
};

startsWithS(['oops', 'gasp', 'shout', 'sun']);  
// [ 'shout', 'sun' ]
```
딱봐도 두 함수가 동일한 코드를 많이 반복하고 있습니다. 코드를 더 일반화된 해결책으로 추상화하는 패턴이 있습니다.  두 함수는 공통점이 많습니다.  둘 다 목록을 순회^iterate^하고 주어진 조건으로 필터링합니다.

순회와 필터링을 위한 코드가 자기들을 추상화 해달라고 구걸하고 있습니다.  모든 종류의 유사한 함수들을 작성할 때 공유하고 재사용해달라고 말합니다. 사실 어떤 목록에서 물건을 선택하는 것은 매우 일반적인 작업입니다.

다행스럽게도 JavaScript의 함수는 일급^first^ ^class^입니다.  그게 무슨 뜻이냐구요? 숫자, 문자열 또는 객체와 마찬가지로 함수는 다음과 같은 일을 할 수 있습니다.

-   식별자 (변수)값으로 할당
-   객체 속성 값에 할당 
-   인수로 전달
-   함수에서 리턴됨

기본적으로 프로그램에 있는 다른 데이터들처럼 함수를 사용할 수 있으므로 추상화하기가 훨씬 쉬워졌습니다.  예를 들어 목록을 순회하는 과정을 추상화하고  데이터를  처리하는 함수인 **reducer**를 전달하여 리턴 값을 누적하는 함수를 만들 수 있습니다.  이 함수를 **reduce**  라고 부릅니다.

```javascript
const reduce = (reducer, initial, arr) => {  
  // shared stuff  
  let acc = initial;  
  for (let i = 0, { length } = arr; i < length; i++) {

    // unique stuff in reducer() call  
    acc = reducer(acc, arr[i]);

  // more shared stuff  
  }  
  return acc;  
};

reduce((acc, curr) => acc + curr, 0, [1,2,3]); // 6
```
`reduce()`함수는 reducer 함수, 누적값^accumulator^의 초기값 그리고 순회할 배열을 인자로 받습니다. 배열의 각 항목마다 reducer가 호출되어 누적값과 현재 배열 요소를 전달합니다.  누적값에는 계속해서 값이 누적되며 배열의 모든 요소에 대해 순회한 이후 최종적인 누적값이 리턴됩니다.

맨 아래줄에서는 reducer함수로 `(acc, curr) => acc + curr`를 전달하는데 이는 배열 요소를 계속하여 누적하는 프로세스가 됩니다. 다음으로 초기 값인  `0`  과 순회할 데이터 배열을 전달합니다.

반복과 누적^accumulation^이 추상화되면서 이제는 좀 더 일반화 된  `filter()`  함수를 구현할 수 있습니다.

```javascript
const filter = (  
  fn, arr  
) => reduce((acc, curr) => fn(curr) ?  
  acc.concat([curr]) :  
  acc, [], arr  
);
```
`filter()`에서는 인수로 전달 된  `fn()`함수를 제외한 모든 것이 다른 곳에서 재사용될 수 있는 것들입니다.  이 때 `fn()`은 술어^predicate^라고합니다.  **술어**  는 부울 값을 리턴하는 함수입니다.

전달받은 배열에서 값을 하나씩 순회하며 `fn()`을 적용합니다.  `fn(curr)`  테스트가  `true`  리턴하면  `curr`  값을 빈 배열에 계속하여 연결합니다.  테스트가 실패할경우 현재 배열값을 넘깁니다.

이제  `filter()`  를 사용해  네 글자로 이루진 단어를 필터링하는 `censor()`를 구현해보겠습니다.
```javascript
const censor = words => filter(  
  word => word.length !== 4,  
  words  
);
```
인상적이지 않습니까? 다양한 작업(필터링, 순회)들이 추상화되었고  `censor()`  는 아주 짧은 함수가 됐습니다.

`startsWithS()`도  마찬가지입니다.

```javascript
 const censorstartsWithS = words => filter(  
  word => word.length !== 4startsWith('s'),  
  words  
);
```

몇몇 독자들은 이미 JavaScript가 이러한 추상화를 제공한다는걸 알고 있을 겁니다.  `Array.prototype`메서드에는 `.reduce()`  `.filter()`   `.map()`와 같은 다양한 함수이 이미 존재합니다.

고차 함수는 다양한 데이터유형에서 동일하게 작동하도록 추상화하는데도 사용됩니다.  예를 들어  `.filter()`가 꼭 문자열 배열에서만 작동하라는 법은 없습니다. 인자로 전달하는 함수가 다른 데이터 유형을 처리하게만 하면 됩니다.  `highpass()`  예제를 기억하십니까?

```javascript
const highpass = cutoff => n => n >= cutoff;  
const gt3 = highpass(3);  
[1, 2, 3, 4].filter(gt3); // [3, 4];
```

즉, 고차 함수를 사용하여 함수에 다형성을 부여할 수 있습니다. 보시다시피 고차 함수는 1차 함수보다 훨씬 다재다능합니다. 일반적으로, 실제 응용 프로그램애플리케이션은 고차함수와 매우 간단한 1 차 함수를 함께 사용합니다.

[**다음: Reduce >**](https://midojeong.github.io/2018/03/31/reduce/)

