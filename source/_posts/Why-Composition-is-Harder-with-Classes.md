---
title: 클래스로 합성하기가 까다로운 이유
catalog: true
date: 2018-04-12 13:35:23
subtitle: Why Composition is Harder with Classes
header-img: "bg.jpg"
readingTime: 13
tags:
  - 자바스크립트
  - 함수형
catagories:
- 개발
preview: 이전 글에서 우리는 팩토리 함수라는 주제를 살펴봤고 여기에 함수형 믹스인도 함께 사용해서 얼마나 쉽게 객체를 합성할 수 있는지 알게 됐습니다. 이제 클래스에 대해 좀 더 자세히 알아보겠습니다. `class`의 어떤 매커니즘이 합성과 연관되어 있는지 살펴 보겠습니다. 또한 클래스를 유용하게 사용한 사례와 클래스를 안전하게 사용하는 방법에 대해 살펴 보겠습니다. ES6에는 우리에게 익숙한  `class`구문이 있습니다. 팩토리 같은걸 왜 신경 써야하는지 궁금 할 수 있습니다.  가장 분명한 차이점은 생성자 함수와  `class`가  `new`  키워드를 필요로 한다는 것입니다.  그렇다면 실제로  `new`는 무슨일을 하는 걸까요?
---


> 이 글은  [Eric Elliott](https://medium.com/@_ericelliott?source=post_header_lockup) 이 **medium**에서 연재하는 Composing Software 시리즈를 번역한 것입니다.  [[원문보기]](https://medium.com/javascript-scene/why-composition-is-harder-with-classes-c3e627dcd0aa)

![](https://cdn-images-1.medium.com/max/1600/1*uVpU7iruzXafhU2VLeH4lw.jpeg)

*Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)*

>참고 : 이 글은 JavaScript ES6+의 함수형 프로그래밍 및 소프트웨어 합성 방법론을 기초부터 다루는 "소프트웨어 합성"시리즈의 일부 입니다.  앞으로 계속하여 연재될 것입니다.
> [<이전](https://midojeong.github.io/2018/04/08/javaScript-factory-function-with-es6/)  |  [<< Part 1에서 다시 시작](https://midojeong.github.io/2018/03/16/composing-software-intro/)  |  [다음>](https://midojeong.github.io/2018/04/14/composable-datatype-with-functions/)

이전 글에서 우리는 팩토리 함수라는 주제를 살펴봤고 여기에 함수형 믹스인도 함께 사용해서 얼마나 쉽게 객체를 합성할 수 있는지 알게 됐습니다. 이제 클래스에 대해 좀 더 자세히 알아보겠습니다. `class`의 어떤 매커니즘이 합성과 연관되어 있는지 살펴 보겠습니다.

또한 클래스를 유용하게 사용한 사례와 클래스를 안전하게 사용하는 방법에 대해 살펴 보겠습니다.

ES6에는 우리에게 익숙한  `class`구문이 있습니다. 팩토리 같은걸 왜 신경 써야하는지 궁금 할 수 있습니다.  가장 분명한 차이점은 생성자 함수와  `class`가  `new`  키워드를 필요로 한다는 것입니다.  그렇다면 실제로  `new`는 무슨일을 하는 걸까요?

-   새 객체를 만들고 이를 생성자 함수의  `this`  객체에 바인딩합니다.
-   명시적으로 어떤 객체를 리턴하지 않으면  암시적으로 `this` 객체를 리턴합니다.
-   인스턴스의 `[[Prototype]]` (내부 참조)을  `Constructor.prototype`  으로 설정합니다. 즉,  `Object.getPrototypeOf(instance) === Constructor.prototype`이 됩니다.
-   `instance.constructor === Constructor`이 되도록 설정합니다.

이 모든 것은 팩토리 함수와 다르게 클래스는 함수형 믹스인을 구성하는 좋은 솔루션이 아니라는 것을 의미합니다.  `class` 사용하여 합성을 할 수는 있지만 훨씬 복잡한 과정입니다. 이 과정에서 드는 추가 비용을 따져봤을 때 별다른 노력을 기울일 가치가 없습니다.

## 프로토타입 위임^delegation^

결국 클래스를 팩토리 함수로 리팩토링해야 할 수도 있습니다. 이 때 다른 코드에 있는 호출자가 `new`  키워드를 사용하고 있었다면 리팩토링이 우리가 모르는 사이에 여러가지 방법으로 클라이언트 코드를 손상시킬 수 있습니다.  우선, 클래스 및 생성자 함수와 달리 팩토리 함수는 프로토타입 링크를 자동으로 연결하지 않습니다.

프로토타입 위임에는  `[[Prototype]]`  링크가 사용됩니다. 수백만개의 객체가있는 경우 메모리를 절약하는 괜찮은 방법입니다. 16ms 렌더링 루프 사이클 안에 객체의 수만 가지 속성에 액세스해야 하는 경우 프로그램 성능을 미세하게 쥐어 짜는데 사용됩니다.  

메모리나 성능을 미세한 부분까지 최적화할 필요가  없다면,  `[[Prototype]]`  링크는 이익보다 해를 더 끼칠 수 있습니다.  JavaScript의 `instanceof` 연산자는 프로토타입 체인으로 동작하지만, 불행하게도  `instanceof`는 두 가지 방법으로 사용자를 속일 수 있습니다.

ES5의  `Constructor.prototype`  링크는 동적이며 재구성 가능했습니다. 추상 팩토리를 만들어야 하는 경우 편리한 기능이 될 수 있었습니다. 그러나 이 기능을 사용했을 때 만약  `Constructor.prototype`이 인스턴스의  `[[Prototype]]`과 같은 메모리를 참조하고 있지 않은 경우  `instanceof`는  `false`를 리턴하게 됩니다. :

```javascript
class User {  
  constructor ({userName, avatar}) {  
    this.userName = userName;  
    this.avatar = avatar;  
  }  
}

const currentUser = new User({  
  userName: 'Foo',  
  avatar: 'foo.png'  
});

User.prototype = {};

console.log(  
  currentUser instanceof User, // <-- false -- Oops!

// But it clearly has the correct shape:  
  // { avatar: "foo.png", userName: "Foo" }  
  currentUser  
);
```

Chrome에서 `Constructor.prototype`  속성을  `configurable: false`로 설정하면 이러한 문제를 해결할 수 있습니다.  그러나 Babel은 현재 이 동작을 미러링하지 않으므로 Babel로 컴파일된 코드는 ES5 생성자처럼 동작합니다. V8 엔진에서 `Constructor.prototype` 속성을 재설정 하려고 하면 자동으로 실패합니다.  어느 쪽이든, 당신은 예상 하지 못한 결과를 얻게 됩니다. 더 안좋은 점은 같은 동작을 하지 않는다는 것입니다.  왠만하면 `Constructor.prototype`을 재할당하지 마세요.

보다 일반적인 문제는 JavaScript에 여러 실행 컨텍스트(동일한 코드가 서로 다른 물리적 메모리 주소에 액세스하는 메모리 샌드 박스)가 있다는 것입니다. 예를 들어 부모 프레임에 생성자가 있고 자식인  `iframe`에 동일한 생성자가있는 경우 부모 프레임의  `Constructor.prototype`은  `iframe`의 `Constructor.prototype`과 동일한 메모리 위치를 참조하지 않습니다.  JavaScript에서 객체의 값을 얻기 위해 메모리를 참조하는 것은 내부적으로 감춰진 채 작동하며 서로 다른 프레임은 물리적 메모리에서 서로 다른 위치를 가리키므로  `===` 검사는 실패합니다.

`instanceof`의  또 다른 문제점은 구조적인 타입 체크가 아닌 명칭 타입 체크라는 점입니다. 즉,  `class`로 시작한 다음 나중에 추상 팩토리로 전환하면  `instanceof`를 사용하는 모든 클라이언트 코드는 새 구현을 이해하지 못합니다.  동일한 인터페이스 계약을 만족시킨다해도 말이지요.  예를 들어, 음악 플레이어 인터페이스를 구축해야 한다고 가정해 보겠습니다.  나중에 제품 팀에서 비디오에 대한 지원을 추가하라고 지시합니다.  나중에 360도 동영상에 대한 지원을 요청합니다. 그들은 모두 동일한 컨트롤을 필요로 합니다 : 재생, 정지, 되감기, 빨리 감기.

그러나  `instanceof`검사를 사용하는 경우 비디오 인터페이스 클래스의 멤버는   이미 코드베이스에있는`foo instanceof AudioInterface` 검사를 만족하지 않습니다.

제대로 구현되었다면 `false`를 리턴할 것입니다. 다른 언어의 공유 인터페이스는 클래스가 특정 인터페이스를 구현했다고 선언하도록 하여 이 문제를 해결합니다.  JavaScript에서는 현재 불가능합니다.

JavaScript에서  `instanceof`를 처리하는 가장 좋은 방법은 프로토타입 링크 위임이 필요하지 않은 경우 이를 중단하고 모든 호출에 대해  `instanceof`  실패하게 만드는 것입니다. 그렇게하면 신뢰성이 확보될 것입니다.

> 처음부터 `instanceof`가 하는 말을 듣지 않으면 그것은 결코 거짓말하지 않을 것이다.

## .constructor 속성

`.constructor`는 자주 사용하는 속성은 아닙니다. 그러나 매우 유용하게 활용 할 수 있으며 객체 인스턴스에 포함시키는 것이 좋습니다.  이를 타입 검사(`instanceof`가 안전하지 않은 것과 같은 이유 때문에 안전하지 않은)용도로 사용하려고 하지 않으면 대부분 무해합니다.

**이론적으로**  `.constructor`는 전달받은 객체의 새 인스턴스를 리턴하는 제네릭 함수를 만드는 데 유용합니다.

**실제로** JavaScript에는 새 인스턴스를 만드는 여러 가지 방법이 있습니다. 생성자를 이해한다고 해서 이를 사용해 새 객체를 인스턴스화하는 방법을 아는 것은 아닙니다.  주어진 객체로부터 빈 인스턴스를 생성하는 것 같은 사소한 용도로 사용할 때 조차도 문제가 발생할 수 있습니다. :

```javascript
// Return an empty instance of any object type?  
const empty = ({ constructor } = {}) => constructor ?  
  new constructor() :  
  undefined  
;

const foo = [10];

console.log(  
  empty(foo) // []  
);
```

`Array` 객체에 대해선 잘 작동하는 것 처럼 보입니다. 그렇다면 `Promise`로 시도해 보겠습니다.

```javascript
// Return an empty instance of any type?  
const empty = ({ constructor } = {}) => constructor ?  
  new constructor() :  
  undefined  
;

const foo = Promise.resolve(10);

console.log(  
  empty(foo) // [TypeError: Promise resolver undefined is  
             //  not a function]  
);
```

코드에 있는  `new`  키워드가 보입니까?  그것이 대부분의 문제를 일으킵니다.  `new` 키워드를 팩토리 함수에서 사용하는 것은 안전하지 않습니다.  때로는 오류가 발생할 수 있습니다.

이 작업을 올바르게 수행하려면  `new` 키워드가 필요 없는 표준 팩토리 함수를 사용하여 값을 새 인스턴스에 전달해야 합니다.  이 작업에 대한 스펙이 있습니다: 모든 팩토리 함수나 생성자에서 호출될 수 있는 `.of()`라는 정적 메소드입니다.  [`.of()`](https://github.com/fantasyland/fantasy-land) 메소드는 어떤 데이터를 전달받든 간에 이를 포함한 새 인스턴스를 리턴하는 팩토리입니다.

`.of()`  를 사용하여 더 나은 버전의 generic  `empty()`  함수를 만들 수 있습니다.

```javascript
// Return an empty instance of any type?  
const empty = ({ constructor } = {}) => constructor.of ?  
  constructor.of() :  
  undefined  
;

const foo = [23];

console.log(  
  empty(foo) // []  
);
```

안타깝게도 정적 메소드  `.of()`는 이제서야 관심과 지원을 받기 시작했습니다. `Promise`는 `.of()`처럼 동작하는 정적 메소드를 가지고 있지만 이는 `.resolve()`라는 이름을 가지고 있습니다.  따라서 프로미스는 우리의 generic `empty()`함수에서 작동하지 않습니다.

```javascript
// Return an empty instance of any type?  
const empty = ({ constructor } = {}) => constructor.of ?  
  constructor.of() :  
  undefined  
;

const foo = Promise.resolve(10);

console.log(  
  empty(foo) // undefined  
);
```

마찬가지로 이 글을 쓰는 시점에서 JavaScript의 `String`,  `Number`,  `Object`, `Map`, `WeakMap` 또는 `Set`에도 아직 `.of()`가 없습니다. 

`.of()` 메소드가 표준 데이터 타입들을 지원하기 시작하면`.constructor`  속성은 언어의 훨씬 유용한 기능이 될 수 있습니다.  함수자^Functor^, 모나드^Monad^ 등 다양한 대수적 자료형^Algebraic^ ^datatype^을 다루는 함수형 유틸리티 라이브러리를 빌드하는 데 사용할 수 있습니다.

 `.constructor`  및  `.of()`를 팩토리에 쉽게 추가 해보겠습니다.

```javascript
const createUser = ({  
  userName = 'Anonymous',  
  avatar = 'anon.png'  
} = {}) => ({  
  userName,  
  avatar,  
  constructor: createUser  
});

createUser.of = createUser;

// testing .of and .constructor:  
const empty = ({ constructor } = {}) => constructor.of ?  
  constructor.of() :  
  undefined  
;

const foo = createUser({ userName: 'Empty', avatar: 'me.png' });

console.log(  
  empty(foo), // { avatar: "anon.png", userName: "Anonymous" }  
  foo.constructor === createUser.of, // true  
  createUser.of === createUser       // true  
);
```

`.constructor` 속성이 이터레이터에 노출되지 않기 위해선  `Object.create()`를 사용해 이를 프로토타입 체인에 연결하면 됩니다.

```javascript
 const createUser = ({  
  userName = 'Anonymous',  
  avatar = 'anon.png'  
} = {}) => Object.assign(  
  Object.create({  
    constructor: createUser  
  }), {  
    userName,  
    avatar  
  }  
);
```

## 클래스에서 팩토리로

팩토리는 다음과 같은 방법으로 코드의 유연성을 높일 수 있습니다.

-   클라이언트 코드에서 구현 세부사항을 분리합니다.
-   임의의 객체를 생성할 수 있습니다. e.g. 가비지 콜렉터를 길들이기 위한 객체 풀을 사용할 때
-   어떤 종류의 타입 검사라도 제공하지 않는 것이 좋습니다. 이는 실행 컨텍스트에 걸쳐 코드를 망가트릴 수 있습니다. 추상 팩토리로 전환할 때도 바람직하지 않습니다.  API 클라이언트가  `instanceof`  및 기타 신뢰할 수 없는 타입 검사를 사용할 여지를 남기면 안됩니다. 
-   팩토리는 타입을 보증하지 않으므로 동적으로 구현을 변경하여 추상 팩토리로 재사용할 수 있습니다.  e.g. 미디어 플레이어의 `.play()` 메소드가 여러 미디어 유형에서 동작 할 수 있도록 구현하기
-   팩토리는 쉽게 합성할 수 있습니다.

위의 기능들을 클래스로도 구현할 수 있으나 팩토리로 하는 것이 더 쉽습니다. 버그가 숨어있을 가능성이 낮고, 단순하며, 코드가 짧습니다.

이러한 이유 때문에  `class`를 팩토리로 리팩토링하는 것이 바람직합니다. 그러나 이 과정에서 복잡한 오류들이 발생할 수 있습니다.  클래스에서 팩토리로 리팩토링하는 것은 모든 객체 지향 언어에서 공통적으로 필요한 부분입니다. 이 주제에 대해 관심있으면 Martin Fowler, Kent Beck, John Brant, William Opdyke 및 Don Roberts의  ["Refactoring: Improving the Design of Existing Code"](https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672/ref%3Das_li_ss_tl%3Fie%3DUTF8%26linkCode%3Dll1%26tag%3Deejs-20%26linkId%3De7d5f652bc860f02c27ec352e1b8342c)를 살펴보기 바랍니다.

`new`는  함수 호출 동작을 변경하기 때문에 클래스 또는 생성자 함수를 팩토리 함수로 변경하는 것은 잠재적으로 큰 변화를 일으킬 수 있습니다. 다시 말해서,  클라이언트에게 `new`를 사용하도록 강요하는 것은 객체 생성 코드의 구현방식에 종속되는 것 입니다.  이 경우 `new` 키워드로 인해 호출 API가 먹통이 되어버릴 수 있습니다.

다음과 같은 암시적인 동작들은 코드의 제어흐름을 크게 바꿔버립니다.

-   팩토리 인스턴스에  `[[Prototype]]`  링크가 없으면 호출자는  `instanceof`검사를 할 수 없습니다.
-   팩토리 인스턴스에  `.constructor`  속성이 없으면 이 인스턴스에 의존하는 코드가 손상 될 수 있습니다.

위 두 문제는 수동으로 속성을 연결하여 해결할 수 있습니다.

또한 `new` 사용하지 않을 경우 `this`가  팩토리를 사용하는 코드에서 동적으로 바인딩 될 수 있음을 염두에 두어야합니다.  팩토리 함수의 정적 속성에 추상 팩토리 프로토타입을 저장하려는 경우 문제가 복잡해 질 수 있습니다.

또 다른 문제가 있습니다. 모든 `class` 호출에는  `new` 가 필요합니다.  `new`를 빼먹으면 ES6은 다음과같이 에러를 던집니다.

```javascript
class Foo {};

// TypeError: Class constructor Foo cannot be invoked without 'new'  
const Bar = Foo();
```

ES6+에서는 일반적으로 화살표 함수를 사용해서 팩토리를 구현합니다. 그러나 화살표 함수는 `this`를 바인딩 하지 않기 때문에 `new`  로 화살표 함수를 호출하면 오류가 발생합니다.

```javascript
const foo = () => ({});

// TypeError: foo is not a constructor  
const bar = new foo();
```

따라서 클래스를 화살표 함수 팩토리로 리팩토링하려고 하면 ES6는 오류를 발생시키며 이는 정상입니다.  실패하는 것은 좋은 일입니다.

그러나 화살표 함수를 표준 함수로 컴파일하면  **_실패하지 않습니다_** ^fail^ ^to^ ^fail^.  오류를 발생시키지 않으며 이는 좋지 않습니다.  앱을 개발하는 동안에는 "작동"하지만 언제 실패할지 모르는 코드가 됩니다.

코드를 변경하지 않고 컴파일러(e.g. Babel) 설정을 변경하는것 만으로도 애플리케이션이 손상 될 수 있습니다.  주의하십시오 :

> **_경고 :_**  `class`를  화살표 함수 팩토리로 리팩토링하는 것은 컴파일러(Babel)에서는 작동하는 것 처럼 보일 수 있습니다. 그러나 팩토리 코드가 기본 화살표 함수로 컴파일되면 호출 코드에서 `new`  사용할 수 없기 때문에 앱이 중단됩니다.

### new가 필요한 코드는 개방 / 폐쇄 원칙을 위반합니다

 API는 확장성에대해 열려있고 변경-취약성에 대해 닫혀있어야 합니다. 클래스를 확장하는 일반적인 방법은 유연한 팩토리로 바꾸는 것이지만 이는 코드를 망가트릴 수도 있습니다. `new` 키워드가 필요한 코드는 확장성에 대해 닫혀있고 취약성에 대해 열려있습니다. 즉, 우리가 원하는 상황의 정 반대입니다.

이는 생각보다 큰 문제입니다.  `class`  API가 공개되어 있거나 거대한 앱을 여러 팀이 함께 작업하는 경우 리팩토링이 사용자가 알지 못하는 코드를 깨뜨릴 수 있습니다. 따라서 클래스 방식을 _완전히_ 폐기하고 팩토리 함수로 대체하는 것이 더 좋습니다.

이 과정을 사소하게 여길 경우 조용히 해결할 수있는 작은 기술적인 문제를 훨씬 비싼 리팩토링으로 바꿉니다!

저는  `new`가 일으키는 두통이 상당히 비싸다는 것을 알았습니다. 

> 클래스대신에 팩토리를 내보내십시오(export).

## `class`  키워드 및 확장

JavaScript는 `class`  키워드를 보다 아름다운 객체 생성 패턴으로 만들 예정이었습니다. 그러나 실패했습니다.

### 친숙한 구문

JavaScript에서 `class`의 주된 목적은 다른 언어를 닮은 친숙한 구문을 제공하는 것이 었습니다. 그러나 과연 다른 언어의  `class`를 모방 할 필요가 있을까요?

우리는 팩토리를 투명하고 친숙한 구문으로 구현할 수 있습니다.  종종 객체 리터럴로 충분하며 많은 인스턴스를 만들어야하는 경우 팩토리를 사용하면 됩니다.

Java 및 C ++에서 팩토리는 클래스보다 복잡합니다. 그러나 훨씬 유연하기 때문에 어쨌든 사용할 가치가 있습니다.  JavaScript에서 팩토리는 클래스보다 덜 복잡하고 더 유연합니다.

Class  vs:

```javascript
class User {  
  constructor ({userName, avatar}) {  
    this.userName = userName;  
    this.avatar = avatar;  
  }  
}

const currentUser = new User({  
  userName: 'Foo',  
  avatar: 'foo.png'  
});
```

Factory (기능은 동일합니다)

```javascript
const createUser = ({ userName, avatar }) => ({  
  userName,  
  avatar  
});

const currentUser = createUser({  
  userName: 'Foo',  
  avatar: 'foo.png'  
});
```

JavaScript와 화살표 함수에 익숙하다면 팩토리 구문이 명확하고 읽기가 쉬울 것 입니다.  어쩌면 당신은 그저  `new`  키워드를 보고싶어 하는 것일 수도 있습니다.  `new`  키워드를 피해야 하는 좋은 이유가 있습니다.  [[Familiarity bias may be holding you back]](https://medium.com/javascript-scene/familiarity-bias-is-holding-you-back-its-time-to-embrace-arrow-functions-3d37e1a9bb75).

다른 논쟁거리들을 더 알아보겠습니다.

## 성능 및 메모리

> 프로토타입 위임을 제대로 사용하는 사례는 드뭅니다.

`class` 구문은  ES5의 생성자함수 구문보다 약간 좋지만 기본 목적은 프로토타입 체인을 연결하는 것이며 프로토타입 위임을 제대로 사용하는 사례는 거의 없습니다.  이는 본질적으로 퍼포먼스에 관한 문제입니다.

`class`는 두 종류의 성능 최적화를 제공합니다. 속성 접근 최적화 및 프로토타입의 공유 메모리입니다.

대부분의 최신 장치는 기가 바이트 단위의 RAM을 가지며 모든 유형의 클로저 스코프 및 속성 접근은 수십만 ops/초 단위로 측정되므로 일반적인 애플리케이션의 맥락에선 성능 차이를  _거의 측정 할 수 없습니다_.

물론 예외가 있습니다.  RxJS는 `class` 인스턴스를 사용합니다. 클로저 스코프에 접근하는것보다 빠르기 때문입니다. 그러나 RxJS는 16ms 렌더 루프내에 수십만개의 연산을 처리할지도 모르는 범용 유틸리티 라이브러리입니다.

ThreeJS는 클래스를 사용하지만 마찬가지로 16ms마다 수천 개의 객체를 조작하는 게임 엔진에 사용할 수있는 3D 렌더링 라이브러리입니다.

ThreeJS 및 RxJS와 같은 라이브러리는 가능한 극단적으로 최적화하는 것이 이치에 맞습니다.

애플리케이션을 설계할 때는 때이른 최적화^premature^ ^optimization^를 지양하고 큰 영향을 줄 수있는 곳에서만 노력을 집중해야합니다.  예를 들자면 애플리케이션의 네트워크 호출 및 페이로드, 애니메이션, 애셋 캐싱 전략 등이 있습니다.

성능 문제가 눈에 보이고, 앱 코드를 프로파일링해 실제 병목 현상을 찾아낸 경우가 아니라면 성능을 미세 조정하지 마십시오.

대신 유지 관리 및 유연성을 위한 최적화를 해야합니다.

## 타입 검사

JavaScript의 클래스는 동적이며  `instanceof` 는 실행 컨텍스트에 종속되어 있기 때문에  `class` 기반의 타입 검사는 신뢰할 수 없습니다. 이로 인해 버그가 발생하고 애플리케이션이 너무 경직될 수 있습니다. 

## `extends`를 사용한 클래스 상속

클래스 상속은 몇 가지 잘 알려진 문제를 반복적으로 일으킵니다.

-   **단단한 결합 문제** ^The^ ^tight^ ^coupling^ ^problem^ :  자식 클래스는 부모 클래스의 구현에 의존하기 때문에 클래스 상속은 객체 지향 디자인에서 사용할 수있는 가장 조밀한 결합입니다.
-   **깨지기 쉬운 기초 클래스 문제**^The^ ^fragile^ ^base^ ^class^ ^problem^  : 긴밀한 결합으로 인해  기초 클래스가  변경되면 잠재적으로 제 3자가 관리하는 코드에서 많은 수의 클래스가 손상 될 수 있습니다.  작성자는 알지 못하는 코드를 깨뜨릴 수 있습니다.
-   **경직된 계층 구조 문제**^The^ ^inflexible^ ^hierarchy^ ^problem^ :  단일 조상으로 시작해 충분한 시간과 진화가 이루어진 후에는 사실상 새로운 유스 케이스에 대해  잘못된 클래스 이름을 가지게 될 것입니다.
-   **중복 필요성 문제**^The^ ^duplication^ ^by^ ^necessity^ ^problem^ : 경직된 계층 구조로 인해 새로운 유스 케이스가 종종 확장이 아닌 복제에 의해 구현되고 이로 인해 불필요한 유사한 클래스들이 나타나게 됩니다. 유사한 클래스들이 존재하면 상속의 기준을 무엇으로 잡을지 불투명해 집니다.
-   **고릴라 / 바나나 문제**^The^ ^gorilla/banana^ ^problem^ :  "... 객체 지향 언어의 문제점은 객체가 모든 암묵적인 환경을 함께 가질 수 있다는 것입니다.  당신은 바나나를 원했지만 바나나와 정글 전체를 들고있는 고릴라가있었습니다. "~ Joe Armstrong,  ["Coders at Work "](http://www.amazon.com/gp/product/1430219483%3Fie%3DUTF8%26camp%3D213733%26creative%3D393185%26creativeASIN%3D1430219483%26linkCode%3Dshr%26tag%3Deejs-20%26linkId%3D3MNWRRZU3C4Q4BDN)

`extends` 의 유일한 쓰임새는 단일 조상^single-ancestor^ 클래스 분류법을 만드는 것입니다.  영리한 해커가 이것을 읽고 말합니다. "글쎄..  클래스를 합성하면 되잖아. " 저는 이렇게 대답하겠습니다. "이제는 클래스 상속 대신 객체 합성을 사용하고 있으며 JavaScript는  `extends` 없이도 이를 쉽게 할 수 있습니다. "

## 충분히 조심한다면 클래스를 사용해도 괜찮습니다.

지금까지 수많은 주의사항들을 봤습니다. 이제는 클래스를 안전하게 사용하는데 도움이 되는 몇 가지 명확한 가이드라인을 알아봅시다.

-   `instanceof` 피하기 - JavaScript는 동적이고 여러 실행 컨텍스트를 가지고 있기 때문에 진실된 답변을 내놓지 않습니다. 코드를 추상 팩토리로 전환하는 과정에서도 문제가 발생할 수 있습니다.
-   `extends`  피하기 - 단일 계층을 두 번 이상 상속하지 마십시오.   "클래스 상속보다는 객체 합성을 우선해라", Gang of Four,  ["디자인 패턴 : 재사용 가능한 객체 지향 소프트웨어의 요소"](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4)
-   `export class` 피하기 - 성능을 위해 내부적으로는  `class`   사용하되 인스턴스를 생성하는 팩토리를 내보내십시오. 사용자가 클래스를 상속하지 못하게하고 호출자가  `new`를 사용하지 못하도록 해야합니다.
-   `new`  피하기 -  의미상 자연스러울 지라도 직접 사용하는 것을 피하고, 다른 사람이 이를 사용하도록 강요하지 마십시오. (팩토리를 내보내면 됩니다)

다음은 클래스를 사용해도 괜찮은 경우입니다.

-   **React 또는 Angular 같은 UI 프레임워크를 제작중인 경우**  이들은 모두 컴포넌트 클래스를 팩토리로 감싸고 인스턴스 생성을 책임지게 합니다. 즉, 사용자가  `new`를 사용할 필요가 없습니다.
-   **클래스 상속을 사용하지 않을 경우**  그대신 객체 합성, 함수 합성, 고차 함수, 고차원 컴포넌트 또는 모듈을 사용해보십시오. 이 모든 것은 클래스 상속보다 더 나은 코드 재사용 패턴입니다.
-   **성능을 최적화하는 경우**  그대신 호출자가  `new`를 사용할 필요가 없어야 하며  `extends`라는 함정에 빠지지 않도록 팩토리를 `export`해야 합니다.

이 세가지 경우를 제외한 대부분의 상황에선 팩토리가 더 나은 방법이 될 것 입니다.

팩토리는 JavaScript의 클래스 또는 생성자 함수보다 간단합니다.  항상 가장 간단한 솔루션부터 시작하여 필요한 경우에만 보다 복잡한 솔루션으로 나아가야 합니다.

[**다음: 함수형 자료구조 >**](https://midojeong.github.io/2018/04/14/composable-datatype-with-functions/)