---
title: 함수형 프로그래머를 위한 JavaScript 개요
catalog: true
date: 2018-03-28 13:31:41
subtitle: "A Functional Programmer’s Introduction to JavaScript"
header-img: "bg.jpg"
readingTime: 13
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 이 편은 JavaScript 또는 ES6 +에 익숙하지 않은 독자를 위한 간단한 입문 글입니다. 당신이 초보자이든 숙련된 JavaScript 개발자이든 아마 새로 배울 것들이 있을 겁니다.  사실 이번 편은 단순히 주제의 겉표면을 훑으며 관심을 환기하기 위해 작성됐습니다. 더 많이 알고 싶다면 더 깊게 탐구하면 됩니다. 이 글은 계속 연재될 것이며 아직 다룰 주제들은 많이 남아있습니다. 코딩을 배우는 가장 좋은 방법은 직접 작성해보는 것입니다. CodePen이나  Babel REPL과 같은 대화 형 JavaScript 프로그래밍 환경을 사용하는 것이 좋습니다. NodeJS 또는 브라우저 콘솔의 REPL을 사용해도 됩니다. 표현식과 값Expressions and Values 표현식은 값으로 평가되는 코드 덩어리입니다. 다음은 JavaScript에서 유효한 표현식입니다.
---



> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/a-functional-programmers-introduction-to-javascript-composing-software-d670d14ede30)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/03/24/why-learn-functional-programming-in-javascript/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/03/29/higher-order-functions)

이 편은 JavaScript 또는 ES6 +에 익숙하지 않은 독자를 위한 간단한 입문 글입니다. 당신이 초보자이든 숙련된 JavaScript 개발자이든 아마 새로 배울 것들이 있을 겁니다.  사실 이번 편은 단순히 주제의 겉표면을 훑으며 관심을 환기하기 위해 작성됐습니다. 더 많이 알고 싶다면 더 깊게 탐구하면 됩니다. 이 글은 계속 연재될 것이며 아직 다룰 주제들은 많이 남아있습니다.

코딩을 배우는 가장 좋은 방법은 직접 작성해보는 것입니다.  [CodePen](https://codepen.io/)  이나  [Babel REPL](https://babeljs.io/repl/)  과 같은 대화 형 JavaScript 프로그래밍 환경을 사용하는 것이 좋습니다.

NodeJS 또는 브라우저 콘솔의 REPL을 사용해도 됩니다.

## 표현식과 값Expressions and Values

표현식은 값으로 평가되는 코드 덩어리입니다.

다음은 JavaScript에서 유효한 표현식입니다.
```javascript
  7; 

  7 + 1;  // 8 

  7 * 2;  // 14 

  'Hello'; // Hello
```

표현식의 값에는 이름을 붙일 수 있습니다.  이 때 표현식이 먼저 평가되고 결과 값이 이름에 저장됩니다.  변수를 선언하기 위해  `const`  키워드를 사용합니다.  여러 방법이 있지만, `const`를 가장 많이 사용하게 될 것입니다.  따라서 우리는 지금부터  `const`  를 사용 할 것입니다 :
```javascript
  const hello = 'Hello';   
  hello; // Hello 
```
## var, let 및 const

JavaScript는 `const` 외에도   `var`  과  `let`이라는 두 종류의 변수 선언 키워드가 있습니다.  저는 이들을 순서에 맞게 사용할 것입니다.  기본적으로 가장 엄격한 선언인  `const`를  선택합니다.  `const`  키워드로 선언 된 변수는 재 할당 할 수 없습니다.  즉, 선언할 때 최종 값이 저장됩니다.  이는 엄격하다고 여겨질 수도 있지만, 제약은 좋은 것입니다.  "이 변수에 할당 된 값은 변경되지 않을 것입니다"라는 신호입니다.  함수 전체나 블록 스코프를 찾아볼 필요 없이 변수의 의미를 즉시 이해할 수 있습니다.

변수를 재 할당하는 것이 유용할 때가 있습니다.  예를 들어 함수형으로 접근하지 않고 직접 명령을 반복 실행할 경우 `let`으로  선언된 카운터 변수에 반복하여 할당 할 수 있습니다.

`var`은-이건 적어도 **변수**입니다-라는 약한 의미를 가집니다.  ES6으로 프로그래밍하기 시작한 이후 저는 실제 프로젝트에서  `var`  를 의도적으로 선언 한 적이 없습니다.

`let`  또는  `const` 로 선언된 변수를 다시 선언하면 오류가 발생합니다.  REPL (Read, Eval, Print Loop) 환경에서 실험적인 목적으로 코딩을 할 경우 이들 대신  `var`  를 사용하여 변수를 선언하는게 적합합니다.  

실제 프로그램을 작성할 때 기본적으로 `const`를 사용하기 때문에 이 글에서도 `const`를 사용할 것입니다. 다만 실험을 위해서라면 자유롭게 `var`  을 사용하십시오.
## 타입

지금까지 두 가지 타입을 보았습니다 : 숫자와 문자열.  이 외에도 JavaScript에는 부울 (  `true`  또는  `false`  ), 배열, 객체 등이 있습니다.  우리는 나중에 다른 타입을 얻을 것입니다.

배열은 순서가 있는 목록입니다.  다양한 항목을 담을 수있는 상자라고 생각하면 됩니다.  다음은 배열 리터럴 표기법입니다.

```javascript
  [1, 2, 3]; 
```
변수에 할당할 수 있습니다.
```javascript
  const arr = [1, 2, 3]; 
```
JavaScript의 객체는 key : value 쌍의 모음입니다.  표기법은 다음과 같습니다.
```javascript
  {   
	  key : 'value'   
  } 
```
물론 변수에 할당 할 수 있습니다.
```javascript
  const foo = {   
	  bar : 'bar'   
  } 
```
변수의 이름과 값으로 객체를 생성할 수 있습니다.
```javascript
  const a = 'a';   
  const oldA = {a : a};  // long, redundant way   
  const oA = {a};  // short an sweet! 
```
다시 한번 해보겠습니다.
```javascript
  const b = 'b';   
  const oB = {b}; 
```
객체들로 새로운 객체를 쉽게 합성할 수 있습니다.
```javascript
  const c = {...oA, ...oB};  // {a : 'a', b : 'b'} 
```
`...`은 객체 스프레드 연산자입니다.  `oA` 의 프로퍼티들을 반복하여 새로운 객체에 할당한 다음  `oB`  대해 동일한 작업을 수행합니다. 이 때 키가 중복되는 경우 이미 존재하는 키를 덮어씁니다.  이 글을 쓰는 시점에서 객체 스프레드는 아직 대중적인 브라우저에서 사용할 수 없는 실험적인 기능입니다. 브라우저가 지원하지 않을 경우 `Object.assign()`으로 대체 할 수 있습니다.
```javascript
  const d = Object.assign ({}, oA, oB);  
  // {a : 'a', b : 'b'} 
```
`Object.assign()` 은 객체 스프레드보다 조금만 더 타이핑하면 됩니다. 수많은 객체를 합성해야 하는 경우 타이핑을 줄일 수 있습니다.  `Object.assign()`  을 사용할 때 최종적으로 리턴될 객체를 첫 번째 매개 변수로 전달해야합니다.  속성들을 복사 할 개체 말입니다.  만약 이를 생략하면 첫 번째 인수로 전달한 객체가 변경됩니다.

제 경험상, 새로운 객체를 만드는 것이 아닌 기존의 객체를 변경하는 것은 일반적으로 버그를 일으킬 여지가 많습니다. `Object.assign()`을 사용할 때는 이를 주의하십시오.

## 해체 혹은 비구조화^destructuring^^[MDN 번역문서에는 비구조화라고 번역되어있습니다. 그러나 다른 많은 번역글에서 해체라고 번역이 되었으며 역자는 해체라는 용어를 사용하겠습니다.]

객체와 배열 모두 해체를 지원합니다. 즉, 객체에서 값을 꺼내 변수에 할당 할 수 있습니다.
```javascript
  const [t, u] = [ 'a', 'b'];
  t;  // 'a'   
  u;  // 'b' 

  const blep = {   
	  blop: 'blop'
  };   
  
  // The following is equivalent to
  // const blop = blep.blop;   
  const {blop} = blep;   
  blop;  // 'blop' 

  // Also equivalent to
  // const a = this.state.a;
  const {a} = this.state;
```
위의 배열 예제 처럼 동시에 여러 변수에 할당할 수 있습니다. 다음은 Redux 프로젝트에서 자주 볼 수 있는 코드입니다.
```javascript
  const {type, payload} = action; 
```
Reducer에서 다음과 같이 사용합니다. (Reducer는 나중 글에서 설명할 것입니다)
```javascript
  const myReducer = (state = {}, action = {}) => {   
	  const {type, payload} = action;   
	  switch (type) {   
		  case 'FOO': return Object.assign ({}, state, payload);   
		  default : return state;   
	  }   
  }; 
```
새로운 이름으로 할당 할 수 있습니다.
```javascript
 const { blop: bloop } = blep;   
 bloop; // 'blop'` 
```
`blep.blop`  을  `bloop`에 할당했다 라고 읽으면 됩니다.

## 비교 및 삼항 연산자

값을 비교할 때는 완전 항등 연산자("triple equals"라고 함)를 사용합니다.
```javascript
  3 + 1 === 4;  // true 
```
물론 다른 항등 연산자가 있습니다.  공식적으로 "동등"연산자라고합니다.  비공식적으로 "double equals"라고 합니다. double equals는 한두가지 정도 의미있게 사용할  상황이 있습니다. 그 외에는 항상  `===`  연산자로 비교하는 것이 좋습니다.

이외에도 다음과같은 비교 연산자들이 있습니다.

-   `>`  보다 큼
-   `<`  보다 작음
-   `>=`  크거나 같음
-   `<=`  작거나 같음
-   `!=`  같지 않음
-   `!==` 엄격하게 같지 않음
-   `&&`  논리 곱
-   `||`  논리 합

삼항 표현식은 삼항 연산자를 사용한 표현식입니다. 조건이 참이냐 거짓이냐에 따라 다른 값으로 평가됩니다.
```javascript
  14 - 7 === 7?  'Yep!'  : 'Nope.';  // Yep! 
```
## 함수

JavaScript에는 함수 표현식이 있으며 이는 변수에 할당 할 수 있습니다.
```javascript
  const double = x => x * 2; 
```
위 코드는 수학에서의 함수  `f(x) = 2x`  와 같습니다.  소리내어 읽을 경우 `f` `x`는 `2x` 라고 읽습니다. 이 함수는  `x`에 특정한 값을 적용 할 때만 의미가 생깁니다. 다른 식에서 이 함수를 사용하려면  `f(2)`라고 쓰면 됩니다.  `f(2)`  는  `4`와 같은 의미입니다.

즉,  `f(2) = 4`  입니다.  수학의 함수는 입력에서 출력으로의 매핑이라고 생각할 수 있습니다.  이 경우  `f(x)`  는  `x`  에 대한 입력 값을 입력 값과  `2`  의 곱과 동일한 해당 출력 값에 매핑하는 것입니다.

자바 스크립트에서 함수 표현식의 값은 함수 그 자체입니다.
```javascript
  double;  // [Function : double] 
```
`.toString()`  메서드를 사용하여 함수 정의를 볼 수 있습니다.
```javascript
  double.toString ();  // 'x => x * 2' 
```
특정 인수(값)에 함수를 적용하려면 함수를 호출해야합니다.  함수 호출이란 인수에 함수를 적용하고 평가된 값을 리턴받는 것입니다.

`<functionName>(argument1, argument2, ...rest)` 와 같은 문법으로 함수를 호출 할 수 있습니다.  예를 들어 double 함수를 호출하려면 괄호를 추가하고 double 값을 전달하면됩니다.
```javascript
  double(2);  // 4 
```
일부 함수형 언어들과는 달리 괄호가 필수입니다. 괄호가 없으면 함수는 호출되지 않습니다 :
```javascript
  double 4;  // SyntaxError : Unexpected number 
```
## 서명 혹은 시그니처

함수들은 다음과 같이 서명을 가집니다.

1.  함수 이름(선택사항)
2.  인자 타입 목록(매개 변수의 이름은 선택사항)
3.  리턴 타입

JavaScript의 함수서명에는 타입을 명시하지 않아도 됩니다.  JavaScript 엔진은 런타임에 타입을 파악합니다.  충분한 단서들을 제공할 경우 IDE (Integrated Development Environment) 및  [Tern.js](http://ternjs.net/)  와 같은 개발자 도구에서는 데이터 흐름을 분석하여 서명을 유추해내기도 합니다.

자바 스크립트는 함수 서명에 대한 표준이 없기 때문에 여러 표준들이 경쟁을 하는 상황입니다. JSDoc은 오랫동안 쓰여왔지만 장황하고 다소 어색합니다.  대부분의 사람들은 코드에 대한 주석을 최신화하는데 별로 관심이 없기에 많은 JS 개발자가 더 이상 사용하지 않습니다.

가장 인기 있는 두 표준 TypeScript와 Flow가 있습니다. 저는 둘중 어떠한 것이 더 나은지 확실하지 않습니다. 따라서 저는   [Rtype](https://github.com/ericelliott/rtype)을 사용합니다. 어떤 사람들은 커리^curry^를 위해 하스켈 전용  [Hindley-Milner Type](http://web.cs.wpi.edu/~cs4536/c12/milner-damas_principal_types.pdf) 을 꺼내들기도 합니다. 저는 JavaScript 문서화를 위해 얼른 좋은 표기법이 표준화되어야 한다고 봅니다. 그러나 지금 나와있는 해결책 중 어느것도 완벽하다고 할 수 없습니다.  당분간 여러분이 사용하고있는 것과는 약간 다른 문법으로 쓰인 다양한 유형의 서명들을 이해하기 위해 최선을 다해야 할 것입니다.
```
  functionName (param1 : Type, param2 : Type) => Type 
```
double 함수의 서명은 다음과 같습니다.
```
  double (x : n) => Number 
```
JavaScript는 강제로 주석을 달지 않아도 된다는 사실에도 불구하고, 함수를 사용할 때 그리고 합성할 때 효율적으로 의사소통을 하기 위해 서명에 의미를 재빨리 파악할 수 있는 것이 중요합니다. 함수 합성에 관련된 대부분의 라이브러리들은 동일한 타입의 서명을 가진 함수를 전달할 것을 요구합니다.

## 기본 매개 변수 값^default^ ^parameter^ ^values^

JavaScript는 기본 매개 변수 값을 지원합니다.  다음 함수는 전달받은 값을 그대로 리턴하는 항등^identity^함수입니다.  그러나 `undefined`를 인수로 전달받거나 아무 인수도 전달받지 않은 경우 0을 리턴합니다.
```javascript
  const orZero = (n = 0) => n; 
```
기본값을 설정하려면  `n = 0` 처럼 `=`  연산자를 사용하여 매개 변수에 값을 할당하기만 하면 됩니다.  이런 식으로 기본값을 지정하면  [Tern.js](http://ternjs.net/)  , Flow 또는 TypeScript를 사용할 때 함수의 형식과 서명을 주석으로 명시하지 않아도 이를 자동으로 유추 할 수 있습니다.

기본 매개 변수값을 지정하고 텍스트 에디터나 IDE에 관련 플러그인을 설치하면 함수를 사용하려고 할 때 함수 서명이 표시됩니다. 또한 함수를 사용하는 방법을 파악하기 쉽습니다. 이는 코드 자체가 곧 문서가 될 수 있는 중요한 접근방법입니다.

> 참고 : 기본값이 있는 매개 변수는 함수의  `.length`  속성에서 제외됩니다. 즉 `length`값을 참조하는 autocurry 라이브러리를 사용할 때 문제가 발생할 수 있습니다. 그러나 일부 currying 라이브러리 (예 :  `lodash/curry`  )에서는 함수 인자의 개수^arity^를 임의로 전달 하는 옵션이 있습니다.

## 해체-할당과 기본값

JavaScript 함수에서 객체 리터럴을 받아 해제-할당하면 이를 보통 인수처럼 사용할 수 있습니다. 이 때도 마찬가지로 기본 매개 변수 기능을 사용하여 기본값을 할당 할 수 있습니다.
```javascript
  const createUser = ({   
	  name = 'Anonymous',   
	  avatarThumbnail = '/avatars/anonymous.png'   
  }) => ({   
	  name,   
	  avatarThumbnail   
  }); 

  const george = createUser ({   
	  name : 'George'   
	  avataThumbnail : 'avatars/shades-emoji.png'   
  }); 

  george;   
  /*   
  {   
	  name : 'george'   
	  avatarThumbnail : 'avatars/shades-emoji.png'   
  }   
  */ 
```
## 나머지와 스프레드 연산자

나머지 연산자`...`를 사용해서 함수의 정해지지 않은 인수들을 배열로 참조할 수 있습니다.

예를 들어 다음 함수는 첫 번째 인수를 버리고 나머지를 배열로 리턴합니다.
```javascript
  const aTail = (head, ...tail) => tail;   
  aTail(1, 2, 3);  // [2, 3] 
```
나머지 구문은 개별 요소를 배열로 만듭니다. 스프레드는 반대의 역할을합니다. 즉, 주어진 배열을 개별 요소로 퍼트립니다. 나머지와 스프레드는 같은 형태를 가지지만 사용법이 다릅니다. 이를 주의해서 다음 코드를 참고하세요:
```javascript
  const shiftToLast = (head, ...tail) => [...tail, head];   
  
  /* 첫 번째 ...tail은 나머지연산자가 사용되었고 뒤에는 스프레드
     연산자가 사용되었습니다.*/
  
  shiftToLast(1, 2, 3);  // [2, 3, 1] 
```
JavaScript의 배열에 있는 iterator는 스프레드 연산자가 사용될 때 함께 호출됩니다. iterator는 배열의 요소 값들을 반복해서 리턴합니다. `[...tail, head]`  표현식에서 iterator는 나머지 구문으로 전달받은  `tail` 배열에서 값을 하나씩 꺼내 새로운 배열 리터럴에 복사합니다. `head`는 이미 개별 요소이기 때문에 배열의 끝 부분에 붙이면 됩니다.

## 커링^currying^

커리된 함수는 한 번에 하나씩 여러 인자를 받는 함수입니다. 인자를 받아 그 다음인자의 입력을 기다리는 함수를 리턴합니다. 모든 인자가 채워지면 최종 값이 리턴됩니다.

커링 및 부분 적용^partial^ ^application^은 함수를 반환하는 행위입니다.
```javascript
  const highpass = cutoff => n => n >= cutoff;   
  const gt4 = highpass(4);  // highpass() returns a new function
```
꼭 화살표 구문을 사용할 필요는 없습니다. JavaScript에는  `function`  키워드가 있습니다. 다만 `function`  키워드를 쓰면 타이핑을 조금 더 해야할 뿐입니다. 즉 아래의 `highpass`는 위의 정의와 동일합니다.
```javascript
  const highpass = function highpass(cutoff) {   
	  return function(n) {   
		  return n >= cutoff;   
	  };   
  }; 
```
화살표는 "함수"를 뜻합니다. 그러나 몇 가지 중요한 차이가 있습니다. ( `=>` 은 기본적으로 `this`가 없기 때문에 생성자로 사용할 수 없습니다)  이 주제에 관해선 나중에 더 깊게 알아볼 것입니다. 그러니 지금 당장은  `x => x`  라는 코드를 "  `x` 를 받아  `x`를 리턴하는 **함수**"라고 생각하십시오.  따라서 
```javascript
  const highpass = cutoff => n => n >= cutoff;
```
라는 코드는 다음처럼 읽으면 됩니다:

>`highpass`  는  `cutoff`  를 받고  `n`을 받은 이후에  `n >= cutoff`  의 결과를 리턴하는 함수를 리턴하는 함수

함수를 리턴하는`highpass()` 를 사용해 보다 특화된 함수를 만들 수 있습니다.
```javascript
  const gt4 = highpass(4); 

  gt4(6);  // true   
  gt4(3);  // false 
```
autocurry를 사용하면 최대한 유연한 방식으로 함수를 커링할 수 있습니다. 
```javascript
  const add3 = curry((a, b, c) => a + b + c);
```
autocurry된 add3 함수는 다양한 방법으로 사용할 수 있습니다.
```javascript
  add3(1, 2, 3); // 6   
  add3(1, 2)(3); // 6   
  add3(1)(2, 3); // 6   
  add3(1)(2)(3); // 6 
```
하스켈 팬에게는 죄송한 일이지만, JavaScript에서는 autocurry를 하기 위해 Lodash와 같은 라이브러리를 사용해야 합니다.
```
 $ npm install --save lodash 
```
`npm`으로 설치 한 다음 코드 상단에서 `import`하면 됩니다 :
```javascript
  import curry from 'lodash/curry';
```
아니면 다음처럼 마술을 부리면 됩니다.
```javascript
  // Tiny, recursive autocurry  
  const curry = (f, arr = []) => (...args) => 
	  (a => a.length === f.length ? f(...a) : curry(f, a))
	  ([...arr, ...args]);
```
## 함수 합성

당연히 함수를 합성 할 수 있습니다. 함수 합성은 한 함수의 리턴 값을 다른 함수의 인수로 전달하는 과정입니다.  수학 표기법 :
```
 f . g
```

는 JavaScript에서 다음과 같이 표현됩니다.
```javascript
  f (g (x)) 
```
그리고 내부적으로 다음과 같이 계산됩니다 :

1.  `x`  가 평가됩니다.
2.  `x`에 `g()`가 적용됩니다.
3.  `g(x)`의 반환 값에 `f()`가 적용됩니다.

예 :
```javascript
  const inc = n => n + 1;   
  inc(double(2));  // 5 
```
값  `2`  는  `double()`  으로 전달되어  `4`  를 생성합니다.  `4`  는  `inc()`  로 전달되고  `5`  평가됩니다.

어떤 표현식을 함수의 인수로 전달할 수 있습니다.  이 때 표현식이 먼저 평가된 후 함수가 적용됩니다.
```javascript
  inc(double(2) * double(2));  // 17 
```
`double(2)`  은  `4` 로 평가되므로  `inc(4 * 4)` 가 된 후  `inc(16)`  로 평가되고  `17`이 리턴됩니다.

함수 합성은 함수형 프로그래밍의 핵심입니다.  

## 배열

배열에는 몇 가지 내장 된 메소드가 있습니다.  메소드는 객체와 관련된 함수입니다. 일반적으로 객체의 속성^property^으로 존재합니다.
```javascript
  const arr = [1, 2, 3];   
  arr.map(double);  // [2, 4, 6] 
```
이 경우  `arr`  이 객체이고  `.map()` 함수가 값으로 할당되어있는 속성입니다.    이 함수를 호출하면 인수에 적용될 뿐만 아니라 `this`  라는 특수 매개 변수에도 적용됩니다.  `this`  매개 변수는 메소드가 호출 될 때 자동으로 설정됩니다. `map()` 메소드에서 배열의 값에 접근할 때`this`를 참조합니다.

`double`  함수를 호출하는 `map`에 전달하는걸 잘 보십시오. `map`  은 함수를 인수로 받아여 배열의 각 항목에 적용합니다.  그리고 `double()`  의해 리턴 된 값으로 새로운 배열을 생성하여 리턴합니다.

원래의  `arr`  값은 변하지 않습니다.
```javascript
  arr;  // [1, 2, 3] 
```
## 메소드 체이닝

메소드를 호출을 연결할 수도 있습니다.  메소드 체인은 리턴 값을 따로 변수에 저장하여 다시 참조 할 필요없이 함수의 리턴 값에 메소드를 계속해서 호출하는 프로세스입니다.
```javascript
  const arr = [1, 2, 3];   
  arr.map(double).map(double);  // [4, 8, 12] 
```
**predicate**  는 부울 값 (  `true`  또는  `false`  )을 리턴하는 함수입니다.  `.filter()`  메소드는 predicate를 인수로 받아 배열의 개별 항목에 적용합니다. 이 때 조건을 통과한(`true`를 반환) 항목만 선택하여 새 배열에 포함시켜 리턴합니다.
```javascript
  [2, 4, 6].filter(gt4);  // [4, 6] 
```
배열에서 조건에 맞는 항목을 골라 매핑하는 상황은 자주 발생합니다.
```javascript
  [2, 4, 6].filter(gt4).map(double);  //[8, 12] 
```
참고 : 이 시리즈의 뒷부분에는 선택과 매핑을 동시에 하는 더 효율적인 방법인 `transducer`가 나옵니다.  

## 결론

머리가 핑핑 돌고 있습니까? 걱정하지 마세요. 지금까지 배운 것들은 단지 맛보기에  지나지 않습니다. 앞으로도 계속 이 주제들이 반복적으로 그리고 좀 더 깊은 이해를 위해 등장할 것입니다.   

[**다음: 고차 함수 >**](https://midojeong.github.io/2018/03/29/higher-order-functions)

