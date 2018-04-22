---
title: 함수형 자료구조
catalog: true
date: 2018-04-15 00:21:45
subtitle: Composable Datatypes with Functions
header-img: "bg.jpg"
readingTime: 5
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: JavaScript에서 무언가를 합성^compose^하는 가장 쉬운 방법은 함수 합성이며 함수는 객체이기 때문에 메소드가 추가 될 수 있습니다 `t`는 숫자형 인스턴스를 생성하는 팩토리입니다.  그러나 `t`의 인스턴스는 단순한 객체가 아닙니다.  **함수**입니다. 즉, 일반적인 함수와 마찬가지로 합성할 수 있습니다.  기본적으로 멤버를 더하는 함수라고 가정 해 봅시다.  어쩌면 합성될 때 더해지는 것이 합리적 일 수 있습니다. 먼저 몇 가지 규칙을 세우겠습니다. ( `====`는  "동등한"을 의미).
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/composable-datatypes-with-functions-aec72db3b093)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)  |  [<< Part 1에서 다시 시작](https://medium.com/javascript-scene/composing-software-an-introduction-27b72500d6ea)  |  [다음>](https://medium.com/javascript-scene/reduce-composing-software-fe22f0c39a1d)

## 함수형 자료구조

JavaScript에서 무언가를 합성^compose^하는 가장 쉬운 방법은 함수 합성이며 함수는 객체이기 때문에 메소드가 추가 될 수 있습니다 :

```javascript
const t = value => {  
  const fn = () => value;

  fn.toString = () => `t(${ value })`;

  return fn;  
};  
  

const someValue = t(2);

console.log(  
  someValue.toString() // "t(2)"  
);
```

`t`는 숫자형 인스턴스를 생성하는 팩토리입니다.  그러나 `t`의 인스턴스는 단순한 객체가 아닙니다.  **함수**입니다. 즉, 일반적인 함수와 마찬가지로 합성할 수 있습니다.  기본적으로 멤버를 더하는 함수라고 가정 해 봅시다.  어쩌면 합성될 때 더해지는 것이 합리적 일 수 있습니다.

먼저 몇 가지 규칙을 세우겠습니다. ( `====`는  "동등한"을 의미).

-   `t(x)(t(0)) ==== t(x)`
-   `t(x)(t(1)) ==== t(x + 1)`

`.toString()`  메소드를 사용하면 다음과 같습니다.

-   `t(x)(t(0)).toString() === t(x).toString()`
-   `t(x)(t(1)).toString() === t(x + 1).toString()`

이 규칙들을 바탕으로 간단한 유닛 테스트를 작성해보겠습니다 :

```javascript
const assert = {  
  same: (actual, expected, msg) => {  
    if (actual.toString() !== expected.toString()) {  
      throw new Error(`NOT OK: ${ msg }  
        Expected: ${ expected }  
        Actual:   ${ actual }  
      `);  
    }

    console.log(`OK: ${ msg }`);  
  }  
};  
  

{  
  const msg = 'a value t(x) composed with t(0) ==== t(x)';  
  const x = 20;  
  const a = t(x)(t(0));  
  const b = t(x);  
  assert.same(a, b, msg);  
}

{  
  const msg = 'a value t(x) composed with t(1) ==== t(x + 1)';  
  const x = 20;  
  const a = t(x)(t(1));  
  const b = t(x + 1);  
  assert.same(a, b, msg);  
}
```
처음에는 실패합니다.
```javascript
 NOT OK: a value t(x) composed with t(0) ==== t(x)   
		 Expected: t(20)   
		 Actual: 20
```

> 이는 `fn`이 `t` 인스턴스가 아니라 실제 값인 `value`를 리턴하기 때문입니다.  flatten되는 것이지요. 계속되는 논의에서 저자는 이를 monoid 형태로 만들어갑니다 -역자

그러나 다음 세가지 단계를 거쳐 간단하게 통과시킬 수 있습니다.

1.  `fn`  함수를  `t(value + n)`를 리턴하는  `add`  함수로 변경합니다. 여기서  `n`은 전달 된 인수입니다.
2.  새로운  `add()`함수가  `t()` 인스턴스를 인수로 취할 수 있도록 하겠습니다.   이를 위해 `.valueOf()`메소드가  `t` 타입을 풀어줄 수 있게합니다. 그리고 `+`  연산자는 두 번째 피연산자로  `n.valueOf()`의 결과를 사용합니다.
3.  `Object.assign()`  을 사용하여  `add()`  함수에 메소드들을 추가합니다.

새로 작성한 코드입니다.
```javascript
const t = value => {  
  const add = n => t(value + n);

  return Object.assign(add, {  
    toString: () => `t(${ value })`,  
    valueOf: () => value  
  });  
};
```
이번에는 테스트가 통과합니다.
```javascript
"OK: a value t(x) composed with t(0) ==== t(x)"  
"OK: a value t(x) composed with t(1) ==== t(x + 1)"
```
이제  `t()`값을 함수 합성으로 연산할 수 있습니다.
```javascript
// Compose functions from top to bottom:  
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);

// Sugar to kick off the pipeline with an initial value:  
const sumT = (...fns) => pipe(...fns)(t(0));

sumT(  
  t(2),  
  t(4),  
  t(-1)  
).valueOf(); // 5
```
## 모든 데이터 유형에 대해 가능합니다

합성 자체에 특정한 의미를 부여할 수 있는 한 데이터가 어떤 모양을 하고있는지는 중요하지 않습니다.  배열 또는 문자열의 경우 연결^concatenation^ 일 수 있습니다.  DSP의 경우 신호 합계가 될 수 있습니다.  수많은 예들이 있을 겁니다.  어떤 연산 혹은 작업이 합성이란 개념과 잘 맞는가라는 질문이 핵심이 됩니다. 즉, 어떤 작업이 다음처럼 표현하기 좋을까요?
```javascript
const result = compose(  
  value1,  
  value2,  
  value3  
);
```
## Composable Currency 예제

[Moneysafe](https://github.com/ericelliott/moneysafe)는 함수형 자료구조로 구현된 오픈 소스 라이브러리입니다.  JavaScript의  `Number`는 소숫점 자리의 달러를 정확하게 나타낼 수 없습니다.
```javascript
  .1 + .2 === .3 // false 
```
Moneysafe는 달러를 센트로 변환해 문제를 해결합니다.
```bash
 $ npm install --save moneysafe
```
사용법은 다음과 같습니다:
```javascript
import { $ } from 'moneysafe';

$(.1) + $(.2) === $(.3).cents; // true
```
렛저^ledger^(장부)라는 간단한 함수 합성 유틸리티가 있습니다. 렛저 구문은 Moneysafe가 값을 승급^lift^하여 함수처럼 다룰 수 있다는 사실을 이용합니다.  
```javascript
import { $ } from 'moneysafe';  
import { $$, subtractPercent, addPercent } from 'moneysafe/ledger';

$$(  
  $(40),  
  $(60),  
  // subtract discount  
  subtractPercent(20),  
  // add tax  
  addPercent(10)  
).$; // 88
```
리턴 값은 처음에 승급된 화폐 값입니다.  내부적으로  부동 소수점 센트 값을 달러로 변환하여 가장 가까운 센트로 반올림하며 이를 `.$`라는 getter를 사용해 받을 수 있습니다.

즉, 장부처럼 금전계산을 할 수있는 직관적인 인터페이스입니다.

### 실습해보기

Moneysafe를 git에서 받아옵니다 :

```bash
$ git clone git@github.com:ericelliott/moneysafe.git 
```

디펜던시들을 설치 합니다.

```bash
$ npm install
```

watch 콘솔을 사용하여 단위 테스트를 실행하십시오.  모두 통과해야합니다 :

```bash
$ npm run watch
```

새 터미널을 열고 구현된 코드들을 삭제하겠습니다.

```bash
$ rm source/moneysafe.js && touch source/moneysafe.js
```

watch 콘솔 테스트를 다시 한 번 살펴보십시오.  이제는 오류가 표시되어야합니다.

여러분의 임무는 unit test와 documentation을 참고해서 `moneysafe.js`를 처음부터 다시 구현하는 것입니다.

회원들을 위해 7개의 파트로 구성된 튜토리얼 시리즈를 녹화했습니다.  여기 첫 번째 에피소드가 있습니다.

Moneysafe 튜토리얼은  [Shotgun 시리즈](https://ericelliottjs.com/premium-content/shotgun-the-moneysafe-series/)에서 볼 수 있습니다.

회원이 아니십니까?  [지금 가입하세요](https://ericelliottjs.com/product/lifetime-access-pass/).

[**다음: JavaScript 모나드 >**](https://medium.com/javascript-scene/javascript-monads-made-simple-7856be57bfe8)