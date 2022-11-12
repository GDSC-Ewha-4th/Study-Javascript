# this

## 상황에 따라 달라지는 this

- 다른 대부분의 객체지향 언어에서 `this`는 클래스로 생성한 인스턴스 객체를 의미하며 클래스 내에서만 사용 가능 vs 자바스크립트의 `this`는 어디서든 사용 가능
- `this`는 실행 컨텍스트가 생성될 때 함께 결정 == 함수를 호출할 때 결정

### 전역 공간에서의 this

전역 공간에서의 `this` == 전역 객체 == 전역 컨텍스트를 생성하는 주체

- 전역 객체: 브라우저 환경 - `window` / Node.js 환경 - `global`

```jsx
// 브라우저 환경
console.log(this === window); // true

// Node.js
console.log(this === global); // true
```

자바스크립트의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작 → 이때 특정 객체란 실행 컨텍스트의 `LexicalEnvironment`(L.E) → 실행 컨텍스트는 변수를 수집해서 L.E의 프로퍼티로 저장, 전역 컨텍스트의 경우 L.E는 전역 객체를 그대로 참조

> 💡 전역 컨텍스트의 LexicalEnvironment(L.E)
전역 컨텍스트의 L.E → GlobalEnv 참조 → GlobalEnv가 전역 객체를 참조


전역 변수를 선언하면 자바스크립트 엔진은 이를 전역 객체의 프로퍼티로 할당한다.

```jsx
var a = 1;
cocnsole.log(a); // 1 -> 전역 스코프의 L.E 즉, 전역 객체에서 프로퍼티 a를 발견해 출력
console.log(window.a); // 1
console.log(this.a); // 1
```

전역 공간에서는 `var`로 변수를 선언하는 대신 `window` 프로퍼티에 직접 할당하더라도 결과적으로 동일하게 동작한다. 하지만 `delete` 연산자의 경우 처음부터 전역객체의 프로퍼티로 할당한 경우에는 삭제가 되지만, 전역변수로 선언한 경우에는 삭제가 되지 않는다. 이는 전역변수를 선언할 때, 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 `configurable` 속성(변경 및 삭제 가능성)을 `false`로 정의하기 때문이다.

이처럼 `var`로 선언한 전역변수와 전역객체의 프로퍼티는 호이스팅 여부 및 `configurable` 여부에서 차이가 있다.

```jsx
var a = 1;
window.b = 2;
console.log(a, window.a, this.a); // 1 1 1
console.log(b, window.b, this.b); // 2 2 2

delete window.a; // false
delete a; // false
delete window.b; // true
```

### 메서드로서 호출할 때 그 메서드 내부에서의 this

- **함수**를 실행하는 일반적인 방법 두 가지 - **독립성**의 차이
    - **함수**로 호출 : 그 자체로 독립적인 기능을 수행
    - **메서드**로 호출 : 자신을 호출한 대상 객체에 관한 동작을 수행
- **메서드**: 객체의 프로퍼티에 할당된 함수, 어떤 함수를 객체의 프로퍼티로 할당한다고 해서 그 자체로 무조건 메서드가 되는 것이 아니라 **객체의 메서드로서 호출할 경우에만 메서드로 동작**

```jsx
var func = function (x) {
	console.log(this, x);
};
func(1); // Window { ... } 1

var obj = {
	method: func
};
obj.method(2); // { method: f } 2
```

- 위의 경우 원래의 익명함수는 그대로인데 이를 변수(`func`)에 담아 호출하는 경우와 `obj` 객체의 프로퍼티에 할당해서 호출한 경우 `this`가 달라진다.
- 어떤 함수를 호출할 때 그 함수 앞에 객체가 명시되어 있는 경우에는 메서드로 호출한 것.
- `this` 에는 호출한 주체에 대한 정보가 담김. 어떤 함수를 메서드로 호출한 경우 호출 주체는 바로 함수명 앞의 객체
    
    ```jsx
    var obj = {
        methodA: function () {console.log(this);},
        inner: {
            methodB: function () {console.log(this);}
        }
    };
    obj.methodA(); // obj 해당하는 값 출력
    obj.inner.methodB(); // obj.inner 해당하는 값 출력
    ```
    

### 함수로서 호출할 때 그 함수 내부에서의 this

함수를 함수로서 호출할 경우에는 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없고 따라서 `this`가 지정되지 않아 **전역 객체**를 바라본다.

#### 메서드 내부함수에서의 `this`

```jsx
var obj1 = {
    outer: function () {
        console.log(this); // --- (1)
        var innerFunc = function () {
            console.log(this); // --- (2), (3)
        }
        innerFunc(); // --- 2

        var obj2 = {
            innerMethod: innerFunc
        };
        obj2.innerMethod(); // --- 3
    }
};
obj1.outer(); // --- 1
```

1. `obj1.outer()` 호출 → 메서드로서 호출한 것이므로 `this` == `obj1` → (1)번에서 `obj1`의 객체 정보 출력
2. `innerFunc()` 호출 → 함수로서 호출한 것이므로 this == 전역객체 → (2)번에서 window 객체 정보 출력
3. `obj2.innerMethod()` 호출 → 메서드로서 호출한 것이므로 this == obj2 → (3)번에서 obj2의 객체 정보 출력

#### 메서드 내부 함수에서의 `this`를 우회하는 방법

호출 주체가 없는 경우 (함수로 호출하는 경우) `this`에 자동으로 전역객체를 바인딩하지 않고 호출 당시 직전 컨텍스트의 `this`를 그대로 상속받아 사용하고 싶을 때는 어떻게 해야 할까?

- ES5까지는 자체적으로 내부함수에 `this`를 상속할 방법이 없어 변수를 사용하여 우회하는 방법을 사용한다.
    
    ```jsx
    var obj1 = {
        outer: function () {
            console.log(this); // --- (1) obj1
            var innerFunc = function () {
                console.log(this); // --- (2) 전역 객체 window
            }
            innerFunc();
    
    				var self = this; // obj1
            var innerFunc2 = function () {
    						console.log(self); // --- (3) obj1
    				}
    				innerFunc2();
        }
    };
    obj1.outer();
    ```
    

#### `this`를 바인딩하지 않는 함수

ES6에서는 `this`를 바인딩하지 않는 화살표 함수(arrow function)을 새로 도입

- 실행 컨텍스트를 생성할 때 `this` 바인딩 과정 자체가 빠지게 되어, 상위 스코프의 `this`를 그대로 활용할 수 있다.

```jsx
var obj1 = {
    outer: function () {
        console.log(this); // --- (1) obj1
        var innerFunc = () => {
            console.log(this); // --- (2) obj1
        }
        innerFunc();
    }
};
obj1.outer();
```

### 콜백 함수 호출 시 그 함수 내부에서의 this

- 함수 A의 제어권을 다른 함수(또는 메서드) B에게 넘겨주는 경우 함수 A를 **콜백 함수**라 한다.
- 콜백 함수도 함수이기 때문에 기본적으로 `this`가 전역객체를 참조하지만, 제어권을 받은 함수에서 콜백 함수에 별도로 `this`가 될 대상을 지정한 경우에는 그 대상을 참조한다.

```jsx
setTimeout(function () {console.log(this);}, 300); // window 전역 객체

[1, 2, 3, 4, 5].forEach(function (x) {
    console.log(this, x); // window 전역 객체
});

document.body.innerHTML += '<button id="a">클릭</button>';
document.body.querySeelctor('#a')
    .addEventListener('click', function (e) {
        console.log(this, e); // document.body.querySelector('#a')
    })
```

- `setTimeout` 함수와 `forEach` 메서드는 그 내부에서 콜백 함수를 호출할 때 대상이 될 `this`를 지정하지 않지만 `addEventListener` 메서드는 콜백 함수를 호출할 때 자신의 `this`를 상속하도록 정의되어 있다.

### 생성자 함수 내부에서의 this

**생성자 함수**는 객체를 생성하는 데 사용하는 함수로 객체지향 언어에서는 생성자를 **클래스(class)**, 클래스를 통해 만든 객체를 **인스턴스(instance)**라고 부른다.

- `new` 명령어와 함께 함수를 호출하면 해당 함수가 생성자로서 동작
- 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 `this`는 새로 만들 구체적인 인스턴스 자신이 된다.
- 생성자 함수 호출 → 생성자의 `prototype` 프로퍼티를 참조하는 `__proto__` 라는 프로퍼티가 있는 객체(인스턴스 생성) → 미리 준비된 공통 속성 및 개성을 해당 객체(`this`)에 부여

```jsx
var Cat = function (name ,age) {
    this.bark = '야옹';
    this.name = name;
    this.age = age;
};
var choco = new Cat('초코', 7); // 생성자 함수 내부의 this == choco 인스턴스
var nabi = new Cat('나비', 5); // 생성자 함수 내부의 this == nabi 인스턴스
```

## 명시적으로 this를 바인딩하는 방법

### call 메서드

`Function.prototype.call(thisArg[, arg1, arg2[, …]]])`

`call` 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령으로 `call` 메서드의 첫 번째 인자를 `this`로 바인딩하고 이후의 인자들을 호출할 함수의 매개변수로 전달한다.

```jsx
var func = function (a, b, c) {
		console.log(this, a, b, c);
}

func(1, 2, 3); // window 1 2 3
func.call({x: 1}, 4, 5, 6); // {x: 1} 4 5 6
```

### apply 메서드

`Function.prototype.apply(thisArgs[, argsArray])`

`apply` 메서드는 `call` 메서드와 기능적으로 동일하지만 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 이용한다는 점에서만 차이가 있다.

```jsx
var func = function (a, b, c) {
		console.log(this, a, b, c);
}

func(1, 2, 3); // window 1 2 3
func.apply({x: 1}, [4, 5, 6]); // {x: 1} 4 5 6
```

### call / apply 메서드의 활용

#### 유사배열객체(array-like object)에 배열 메서드를 적용

객체에는 배열 메서드를 직접 적용할 수 없지만 키가 0또는 양의 정수인 프로퍼티가 존재하고 `length` 프로퍼티의 값이 0 또는 양의 정수인 객체 (유사배열객체)의 경우 `call`과 `apply`를 이용해 배열 메서드를 차용할 수 있다.

```jsx
var obj = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
Array.prototype.push.call(obj, 'd');
console.log(obj); // { 0: 'a', 1: 'b', 2: 'c', 3: 'd', length: 4}

var arr = Array.prototype.slice.call(obj);
console.log(arr); // ['a', 'b', 'c', 'd']
```

- `slice` 메서드는 원래 시작 인덱스값과 마지막 인덱스값을 받아 시작부터 마지막까지의 배열 요소를 추출하는 메서드인데 매개변수를 아무것도 넘기지 않을 경우 원본 배열의 얕은 복사본을 배열로 반환
- 함수 내부에서 접근 가능한 `arguments` 객체도 유사배열객체이므로 배열로 전환해서 활용 가능
- `querySelectorAll`, `getElementsByClassName` 등의 Node 선택자로 선택한 결과인 `NodeList`도 마찬가지

```jsx
var str = 'abc def';

Array.prototype.push.call(str, ', pushed string');
// Error: Cannot assign to read only property 'length' of object [object String]

Array.prototype.concat.call(str, 'string'); // [String {"abc def"}, "string"]

Array.prototype.every.call(str, function(char) {
  return char !== ' ';
}); // false

Array.prototype.some.call(str, function(char) {
  return char === ' ';
}); // true

var newArr = Array.prototype.map.call(str, function(char) {
  return char + '!';
});
console.log(newArr); // ['a!', 'b!', 'c!', ' !', 'd!', 'e!', 'f!']

var newStr = Array.prototype.reduce.apply(str, [
  function(string, char, i) {
    return string + char + i;
  },
  '',
]);
console.log(newStr); // "a0b1c2 3d4e5f6"
```

- 문자열에도 배열 메서드를 적용 가능하지만 `length` 프로퍼티가 읽기 전용이기 때문에 원본 문자열에 변경을 가하는 메서드는 에러를 던지며, `concat`처럼 대상이 반드시 배열이어야 하는 경우에는 에러는 나지 않지만 정확한 결과를 얻을 수 없다.

**ES6**에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 `Array.from` 메서드를 지원

```jsx
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3,
};
**var arr = Array.from(obj);**
console.log(arr); // ['a', 'b', 'c']
```

#### 생성자 내부에서 다른 생성자를 호출

생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 `call` 또는 `apply`를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있다.

```jsx
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}

function Student(name, gender, school) {
  Person.call(this, name, gender);
  this.school = school;
}
```

#### 여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 - `apply` 활용

```jsx
var numbers = [10, 20, 3, 16, 45];
var max = Math.max.apply(null, numbers);
var min = Math.min.apply(null, numbers);
console.log(max, min); // 45 3

// ES6 에서는 펼치기 연산자(spread operator) 이용
const max = Math.max(...numbers);
const min = Math.min(...numbers);
console.log(max, min); // 45 3
```

`call`/`apply` 메서드는 명시적으로 별도의 `this`를 바인딩하면서 함수 또는 메서드를 실행하는 훌륭한 방법이지만 오히려 `this`를 예측하기 어렵게 만들어 코드 해석을 방해한다는 단점이 있다. 하지만 ES5 이하의 환경에서는 마땅한 대안이 없기 때문에 실무에서 광범위하게 활용된다.

### bind 메서드

`Function.prototype.bind(thisArgs[, arg1[, arg2[, …]]])`

`bind` 메서드는 ES5에서 추가된 기능으로, 넘겨받은 `this` 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다. 함수에 `this`를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적을 모두 지닌다.

```jsx
var func = function(a, b, c, d) {
  console.log(this, a, b, c, d);
};
func(1, 2, 3, 4); // Window{ ... } 1 2 3 4

var bindFunc1 = func.bind({ x: 1 });
bindFunc1(5, 6, 7, 8); // { x: 1 } 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6, 7); // { x: 1 } 4 5 6 7
bindFunc2(8, 9); // { x: 1 } 4 5 8 9
```

`bindFunc1` 함수를 만들 때 `bind`는 `this`만을 지정한 것이고, `bindFunc2` 함수를 만들 때 `bind`는 `this` 지정과 함께 부분 적용 함수를 구현한 것이다.

#### `name` 프로퍼티

`bind` 메서드를 적용해서 새로 만든 함수는 `name` 프로퍼티에 `‘bound’`라는 접두어가 붙어 기존의 `call`이나 `apply`보다 코드를 추적하기에 더 수월하다.

```jsx
var func = function(a, b, c, d) {
  console.log(this, a, b, c, d);
};
var bindFunc = func.bind({ x: 1 }, 4, 5);

console.log(func.name); // func
console.log(bindFunc.name); // bound func
```

#### 상위 컨텍스트의 `this`를 내부함수나 콜백 함수에 전달하기

앞서 설명한 `self` 등의 변수를 활용한 우회법보다 `call`, `apply`, `bind` 메서드를 이용하면 더 깔끔하게 처리할 수 있다.

```jsx
var obj1 = {
  outer: function() {
    console.log(this);
    var innerFunc = function() {
      console.log(this);
    };
    **innerFunc.call(this); // call을 사용해서 전달**
  },
};
obj1.outer();

var obj2 = {
  outer: function() {
    console.log(this);
    var innerFunc = function() {
      console.log(this);
    }**.bind(this)**; // bind를 사용해서 전달
    innerFunc();
  },
};
obj2.outer();
```

또한 콜백 함수를 인자로 받는 함수나 메서드 중에서 기본적으로 콜백 함수 내에서의 `this`에 관여하는 함수 또는 메서드에 대해서도 `bind` 메서드를 이용하면 `this` 값을 사용자의 입맛에 맞게 바꿀 수 있다.

```jsx
var obj = {
  logThis: function() {
    console.log(this);
  },
  logThisLater1: function() {
    setTimeout(this.logThis, 500);
  },
  logThisLater2: function() {
    setTimeout(this.logThis.bind(this), 1000);
  },
};
obj.logThisLater1(); // Window { ... }
obj.logThisLater2(); // obj { logThis: f, ... }
```

### 화살표 함수의 예외사항

ES6에 새롭게 도입된 화살표 함수는 실행 컨텍스트 생성 시 `this`를 바인딩하는 과정이 제외되었기 때문에 함수 내부에는 `this`가 아예 없으며, 접근하고자 하면 스코프체인상 가장 가까운 `this`에 접근한다.

이렇게 하면 별도의 변수로 `this`를 우회하거나 `call`/`apply`/`bind`를 적용할 필요가 없어 더욱 간결하고 편리하다.

```jsx
var obj = {
  outer: function() {
    console.log(this);
    **var innerFunc = () => {
      console.log(this);
    };**
    innerFunc();
  },
};
obj.outer();
```

### 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

콜백 함수를 인자로 받는 메서드 중 일부는 추가로 `this`로 지정할 객체(`thisArgs`)를 인자로 지정하여 콜백 함수 내부에서 `this` 값을 원하는 대로 변경할 수 있다. 이런 형태는 여러 내부 요소에 대해 같은 동작을 반복 수행해야 하는 배열 메서드에 많이 포진되어 있으며, 같은 이유로 ES6에서 새로 등장한 `Set`, `Map` 등의 메서드에도 일부 존재한다.

```jsx
var report = {
  sum: 0,
  count: 0,
  add: function() {
    var args = Array.prototype.slice.call(arguments);
    args.forEach(function(entry) {
      this.sum += entry;
      ++this.count;
    }, **this**); // this 가 forEach의 콜백함수의 this로 바인딩된다.
  },
  average: function() {
    return this.sum / this.count;
  },
};
report.add(60, 85, 95);
console.log(report.sum, report.count, report.average()); // 240 3 80
```

## 정리

#### 명시적 `this` 바인딩이 없는 경우

- 전역공간에서의 `this`는 전역객체(브라우저 - `window`, Node.js - `global`)를 참조
- 함수를 메서드로서 호출한 경우 `this`는 메서드 호출 주체(메서드명 앞의 객체)를 참조
- 함수를 함수로서 호출한 경우 `this`는 전역객체를 참조. 메서드의 내부함수에서도 동일
- 콜백 함수 내부에서의 `this`는 해당 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우에는 전역객체 참조
- 생성자 함수에서의 this는 생성될 인스턴스를 참조

#### 명시적 `this` 바인딩

- `call` / `apply` 메서드는 `this`를 명시적으로 지정하면서 함수 또는 메서드를 호출
- `bind` 메서드는 `this` 및 함수에 넘길 인수를 일부 지정해서 새로운 함수를 생성
- 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 `this`를 받기도 한다.
