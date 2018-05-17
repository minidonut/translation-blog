---
title: ES6+와 팩토리 함수
catalog: true
date: 2018-04-08 09:17:04
subtitle: JavaScript Factory Functions with ES6+
header-img: "bg.jpg"
readingTime: 10
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 팩토리 함수는 (새로운)객체를 리턴하는 함수입니다. 그러나 클래스나 생성자 함수는 아닙니다.  JavaScript에서는 모든 함수가 객체를 리턴할 수 있습니다. 이 때 `new` 키워드가 없으면 팩토리 함수입니다. JavaScript에서 팩토리 함수는 항상 매력적입니다. 클래스와  `new`  키워드의 복잡함 없이 객체 인스턴스를 쉽게 생성 할 수 있기 때문입니다.
---

> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/04/07/functional-mixins/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/04/12/Why-Composition-is-Harder-with-Classes/)

## Factory Function

**팩토리 함수**는 (새로운)객체를 리턴하는 함수입니다. 그러나 클래스나 생성자 함수는 아닙니다.  JavaScript에서는 모든 함수가 객체를 리턴할 수 있습니다. 이 때 `new` 키워드가 없으면 팩토리 함수입니다.

JavaScript에서 팩토리 함수는 항상 매력적입니다. 클래스와  `new`  키워드의 복잡함 없이 객체 인스턴스를 쉽게 생성 할 수 있기 때문입니다.

JavaScript는 매우 유용한 객체 리터럴 구문을 제공합니다.  다음과 같습니다.
```javascript
const user = {  
  userName: 'echo',  
  avatar: 'echo.png'  
};
```
자바 스크립트의 객체 리터럴 표기법은 JSON과 마찬가지로  `:`  의 왼쪽은 속성의 이름이고 오른쪽은 값입니다.  점 표기법으로 속성에 액세스 할 수 있습니다.

```javascript
console.log(user.userName); // "echo"
```

대괄호 표기법을 사용하여 계산 된 속성 이름에 액세스 할 수 있습니다.

```javascript
const key = 'avatar';

console.log( user[key] ); // "echo.png"
```

스코프에 객체의 속성 이름과 동일한 이름의 변수가있는 경우, 객체를 리터럴로 생성할 때 콜론과 값을 생략할 수 있습니다.

```javascript
const userName = 'echo';  
const avatar = 'echo.png';

const user = {  
  userName,  
  avatar  
};

console.log(user);  
// { "avatar": "echo.png",   "userName": "echo" }
```

객체 리터럴은 간결한 메소드 구문을 지원합니다.  `.setUserName()`  메소드를 추가 해 보겠습니다.

```javascript
const userName = 'echo';  
const avatar = 'echo.png';

const user = {  
  userName,  
  avatar,

  setUserName (userName) {  
    this.userName = userName;  
    return this;  
  }  
};

console.log(user.setUserName('Foo').userName); // "Foo"
```

이 때,  `this`는 메소드를 호출하는 오브젝트를 참조합니다.  객체의 메소드를 호출하려면 객체 점 표기법을 사용하여 메소에 액세스하고 괄호를 붙여 호출하면 됩니다.  예를 들어  `game.play()`는  `game`객체에  `.play()`를 적용합니다.  점 표기법을 사용하여 메소드를 적용하려면 해당 메소드가 해당 오브젝트의 속성이어야합니다.  함수 프로토타입 메소드인  `.call()`  ,  `.apply()`  또는  `.bind()`를 사용하여 임의의 객체에 메소드를 적용 할 수도 있습니다.

이 경우  `user.setUserName('Foo')`는  `.setUserName()`을  `user`에게 적용합니다.  이 때 `this`는 `user`가 되며  `.setUserName()`  내부의  `this`를 통해  `user` 객체의  `.userName`  속성을 변경합니다. 마지막으로 메소드 체이닝을 할 수 있게 하기 위해 동일한 객체 인스턴스를 리턴하게 했습니다.

## Literals for One, Factories for Many

객체를 반복해서 생성해야 하는 경우 객체 리터럴과 팩토리 함수의 기능을 결합해야합니다.

팩토리 함수를 사용하여 원하는 만큼 많은 객체를 생성 할 수 있습니다.  예를 들어 채팅 앱을 만드는 경우 사용자 본인을 나타내는 사용자 객체가 필요합니다. 그 외에 현재 로그인되어 있고 채팅중인 다른 모든 사용자를 나타내는 많은 다른 사용자 객체가 필요합니다. 

`user`  객체를  `createUser()`  팩토리로 바꿔보겠습니다.

```javascript
const createUser = ({ userName, avatar }) => ({  
  userName,  
  avatar,  
  setUserName (userName) {  
    this.userName = userName;  
    return this;  
  }  
});

console.log(createUser({ userName: 'echo', avatar: 'echo.png' }));

/*  
{  
  "avatar": "echo.png",  
  "userName": "echo",  
  "setUserName": [Function setUserName]  
}  
*/
```

## 암시적인 객체 리턴

화살표 함수 ( `=>` )는 암시적인 리턴을 합니다. 함수 본문이 단일 식으로 구성되어 있으면  `return`  키워드를 생략 할 수 있습니다.  `() => 'foo'`는 아무 인자도 받지 않고 `'foo'`를 리턴합니다.

객체 리터럴을 리턴할 때 주의해야 합니다.  기본적으로 JavaScript는 중괄호를 발견하면 당신이 함수 본문을 만들려고 한다고 여깁니다(e.g.`{ broken: true }`). 객체 리터럴을 암시적으로 반환하고 싶으면 중괄호 바깥을 괄호로 감싸주어야 합니다.
```javascript
const noop = () => { foo: 'bar' };  
console.log(noop()); // undefined

const createFoo = () => ({ foo: 'bar' });  
console.log(createFoo()); // { foo: "bar" }
```

첫 번째에서  `foo:`는 레이블로 해석되고  `bar`는 할당되거나 리턴되지 않는 표현식으로 해석됩니다. 따라서 이 함수는  `undefined`를 리턴합니다.

`createFoo()` 예제에서 괄호는 그 안에 있는 것이 함수 본문 블록이 아닌 평가되어야 할 식으로 해석하도록 합니다.

## 해체 Destructuring^[해체, 파괴, 비구조화, 디스트럭쳐링 등 다양한 용어로 번역되고 있습니다. -역자]

함수 서명을 주의깊게 보시기 바랍니다.

```javascript
const createUser = ({ userName, avatar }) => ({
```

이 줄에서, 중괄호 (  `{`  ,  `}`  )는 객체를 해체하라는 의미로 해석됩니다.  이 함수는 하나의 인수 (객체)를 받지만 이를 해체하여  `userName`  과  `avatar`라는 두 매개변수에 값을 바인딩 합니다.  이러한 매개 변수는 함수 범위에서 변수로 사용될 수 있습니다.  배열을 해체할 수도 있습니다.

```javascript
const swap = ([first, second]) => [second, first];

console.log( swap([1, 2]) ); // [2, 1]
```

그리고 나머지 구문과 스프레드 연산자를(`...varName`  ) 사용하여 배열 (또는 인수 목록)에서 나머지 값을 수집 한 다음 해당 배열 요소를 개별 요소로 다시 펼칠 수 있습니다.

```javascript
const rotate = ([first, ...rest]) => [...rest, first];

console.log( rotate([1, 2, 3]) ); // [2, 3, 1]
```

## 계산된 속성 키

앞에서 우리는 대괄호를 사용해 계산된 속성에 엑세스했습니다. 다시 말하자면 액세스 할 속성을 동적으로 결정했습니다.

```javascript
const key = 'avatar';

console.log( user[key] ); // "echo.png"
```

그렇다면 계산된 값을 키로 할당 할 수도 있습니다.

```javascript
const arrToObj = ([key, value]) => ({ [key]: value });

console.log( arrToObj([ 'foo', 'bar' ]) ); // { "foo": "bar" }
```

이 예제에서  `arrToObj`는 키/값 쌍 (일명 튜플)으로 구성된 배열을 가져 와서 객체로 변환합니다.  키의 이름을 알지 못하기 때문에 객체의 키/값 쌍을 설정하려면 속성 이름을 계산해야합니다.  이를 위해 대괄호 표기법을 빌려 객체 리터럴을 작성하는데 다시 사용합니다.

```javascript
  { [key]: value } 
```

보간이 끝나면 객체가 완성됩니다:

```javascript
  { "foo": "bar"} 
```

## 기본 매개 변수^Default^ ^Parameters^

JavaScript의 함수는 기본 매개 변수 값을 지원하며 다음과 같은 몇 가지 이점이 있습니다.

1.  사용자는 적절한 기본값으로 매개 변수를 생략 할 수 있습니다.
2.  기본값은 예상되는 입력의 예를 제공하기 때문에 이 함수는 더 자체적으로 문서화됩니다.
3.  IDE 및 정적 분석 도구는 기본값을 사용하여 매개 변수에 필요한 유형을 추론 할 수 있습니다.  예를 들어, 기본값  `1`은 매개 변수가  `Number`타입의 멤버를 사용할 수 있음을 의미합니다.

다음 예제는 기본 매개 변수를 사용하여  `createUser` 팩토리의 예상 인터페이스를 문서화하고 사용자 정보가 제공되지 않은 경우  세부 정보를 `'Anonymous'`로 하여 자동으로 채 웁니다.

```javascript
const createUser = ({  
  userName = 'Anonymous',  
  avatar = 'anon.png'  
} = {}) => ({  
  userName,  
  avatar  
});

console.log(  
  // { userName: "echo", avatar: 'anon.png' }  
  createUser({ userName: 'echo' }),

  // { userName: "Anonymous", avatar: 'anon.png' }  
  createUser()  
);
```

함수 서명의 마지막 부분은 아마도 약간 우습게 보일 것입니다.

```javascript
  } = {}) => ({ 
```

인수 서명이 닫히기 직전 마지막에 있는  `= {}`는 `createUser`에게 아무 것도 전달되지 않았을 경우 `undefined`나 `null`을 매개 변수에 전달하는 것이 아닌 빈 객체를 전달하라는 의미 입니다. 빈 객체에 대해 기본값을 해체하여 할당하게되면 자동으로 속성값 설정됩니다. 즉 기본 값이 하는 일은 다음과 같습니다.

> `undefined` 를 미리 정의 된 값으로 바꿉니다.

`= {}`가 없으면  `undefined`의 속성에 액세스하려는 과정에서 `createUser()`  가 오류를 발생시킵니다.

## 타입 추론

JavaScript는이 글을 쓰는 시점에서 타입 주석 체계가 내장되어있지 않습니다. 그러나 JSDoc(점점 사용자가 줄고 있음),  Facebook의 [Flow](https://flow.org/),  Microsoft의 [TypeScript](https://www.typescriptlang.org/) 등의 몇 가지 경쟁 포맷이 갭을 메우기 위해 수년 동안  [급성장하고](https://www.typescriptlang.org/)  있습니다.  저는  [rtype](https://github.com/ericelliott/rtype)을 문서화에 사용합니다 - 이는 함수형 프로그래밍을 위한 TypeScript보다 훨씬 더 읽기 쉬운 표기법입니다.

이 글을 쓰는 시점에는 타입 주석에 대해 확실한 승자가 없습니다.  JavaScript 스펙에 의해 어떠한 대안도 선택을 받지 못했으며, 각각에 명확한 결점이있는 것으로 보입니다.

타입 추론은 데이터가 사용 된 문맥을 기반으로 유추하여 타입을 추론하는 프로세스입니다. JavaScript에서 이는 타입 주석의 좋은 대안입니다.

표준 JavaScript 함수 서명으로 타입 추론을 위한 충분한 단서를 제공하면 비용이나 위험없이 타입 주석의 이점 대부분을 얻을 수 있습니다.

TypeScript 또는 Flow와 같은 도구를 사용하기로한 경우에도 타입 추론으로 가능한 많은 작업을 수행하고 타입 추론이 부족한 상황에서 타입 주석을 달아야합니다.  예를 들어 JavaScript에는 공유 인터페이스를 정의하는 방법이 없습니다. 그러나 TypeScript 또는 rtype에서는 쉽고 유용합니다.

[Tern.js](http://ternjs.net/)  는 다양한 텍스트 에디터와 IDE 용 플러그인이있는 JavaScript용 타입 추론 도구입니다.

Microsoft의 Visual Studio Code는 TypeScript의 타입 추론 기능을 일반 JavaScript 코드로 가져 오기 때문에 Tern이 필요하지 않습니다.

JavaScript에서 함수의 기본 매개 변수를 지정하면 Tern.js, TypeScript 및 Flow와 같은 유형 유추가 가능한 도구는 작업 도중 올바르게  API를 사용할 수 있도록 IDE 힌트를 제공합니다.

기본 값이 없다면 IDE(그리고 사람)는 매개 변수 타입을 파악할 수 있는 힌트를 충분히 확보하지 못합니다.

![](https://cdn-images-1.medium.com/max/1600/1*2sP_9k1e0dkgYqdEPs0G3g.png)

기본값이 없으면`userName`의 타입을 알 수 없습니다.

기본값을 사용하면 IDE(그리고 사람)가 예제를 보고 유추 할 수 있습니다.

![](https://cdn-images-1.medium.com/max/1600/1*tFUXCLA8ClAzsPgZXGGR9A.png)

기본값을 보고, IDE는`userName` 매개 변수가 문자열을 기다리고 있다고 제안 할 수 있습니다.

매개 변수를 고정 유형으로 제한하는 것이 의미가 있다는 것은 아닙니다(일반 함수와 고차 함수를 만들기 어려워 집니다). 하지만 적절히 사용한다면 기본 매개 변수만큼 유용한 것도 없습니다. TypeScript 또는 flow를 사용하고 있어도 마찬가지입니다.

## 믹스인 합성을 위한 팩토리 함수

팩토리는 API를 멋지게 호출하여 객체를 꺼내오는데 유용합니다. 일반적으로 이 정도면 충분하지만, 한 번에 여러 유형의 객체에 비슷한 기능을 구현하려는 경우가 있습니다. 이러한 기능을 보다 쉽게 재사용하려면 함수형 믹스인으로 추상화하면 됩니다.

이 때야 말로 함수형 믹스인이 빛나는 곳입니다.  모든 객체 인스턴스에  `withConstructor`  속성을 추가하려면  `withConstructor`  믹스인을 빌드하십시오.

`with-constructor.js`  :

```javascript
const withConstructor = constructor => o => {  
  const proto = Object.assign({},  
    Object.getPrototypeOf(o),  
    { constructor }  
  );

  return Object.assign(Object.create(proto), o);  
};
```

이것을 가져와 다른 믹스인과 함께 사용할 수 있습니다.

```javascript
import withConstructor from './with-constructor';

const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);  
// or `import pipe from 'lodash/fp/flow';`

// Set up some functional mixins  
const withFlying = o => {  
  let isFlying = false;

  return {  
    ...o,  
    fly () {  
      isFlying = true;  
      return this;  
    },  
    land () {  
      isFlying = false;  
      return this;  
    },  
    isFlying: () => isFlying  
  }  
};

const withBattery = ({ capacity }) => o => {  
  let percentCharged = 100;

  return {  
    ...o,  
    draw (percent) {  
      const remaining = percentCharged - percent;  
      percentCharged = remaining > 0 ? remaining : 0;  
      return this;  
    },  
    getCharge: () => percentCharged,  
    get capacity () {  
      return capacity  
    }  
  };  
};

const createDrone = ({ capacity = '3000mAh' }) => pipe(  
  withFlying,  
  withBattery({ capacity }),  
  withConstructor(createDrone)  
)({});

const myDrone = createDrone({ capacity: '5500mAh' });

console.log(`  
  can fly:  ${ myDrone.fly().isFlying() === true }  
  can land: ${ myDrone.land().isFlying() === false }  
  battery capacity: ${ myDrone.capacity }  
  battery status: ${ myDrone.draw(50).getCharge() }%  
  battery drained: ${ myDrone.draw(75).getCharge() }%  
`);

console.log(`  
  constructor linked: ${ myDrone.constructor === createDrone }  
`);
```

보시다시피 재사용 가능한  `withConstructor()` 믹스인은 다른 믹스인과 함께 파이프 라인으로 간단히 합쳐집니다.  `withBattery()`는 로봇, 전기 스케이트 보드 또는 보조 배터리와 같은 다른 종류의 객체와 함께 사용할 수 있습니다.  `withFlying()`  은 날아다니는 자동차, 로켓 또는 에어벌룬을 모델링하는데 사용될 수 있습니다.

**Composition**은 특별한 코딩 기술이라기 보다는 사고 방식에 가깝습니다.  여러분은 여러 가지 방법으로 이를 적용 할 수 있습니다.  함수 합성은 기초부터 다시 만들 때 사용할 수 있는 가장 쉬운 방법이며, 팩토리 함수는 구현의 세부 사항을 친숙한 API로 포장하는 간단한 방법입니다.

## 결론

ES6가 제공하는 편리한 구문들은 객체 생성 및 팩토리 함수를 다루기 쉽게 만듭니다. 대부분이 경우에는 이정도로 충분하지만, JavaScript이기 때문에 Java와 같은 느낌을 주는 또 다른 접근 방식이 있습니다 :  `class`  키워드.

JavaScript에서 클래스는 팩토리보다 더 장황하고 제한적이며, 리팩토링할 때는 거의 지뢰밭을 걷는 느낌을 주지만 React와 Angular와 같은 주요 프론트 엔드 프레임 워크에 의해 사용되어 왔으며 몇 가지 드문 용도(복잡함을 감수할만한 가치가 있는 경우)가 있습니다.

> "때때로 우아한 구현은 단지 함수뿐입니다.  메소드가 아닙니다. 클래스도 아닙니다. 프레임워크도 아니고 그저 함수입니다."~ John Carmack

가장 간단한 구현으로 시작하고 필요한 경우에만보다 복잡한 구현으로 이동하십시오.  객체를 다루는 관점에서는 다음과 같습니다.

`Pure function -> factory -> functional mixin -> class`

[**다음: 클래스로 합성하기가 까다로운 이유 >**](https://midojeong.github.io/2018/04/12/Why-Composition-is-Harder-with-Classes/)
