# 3.this

- 화살표 함수 :  [https://tooo1.tistory.com/561](https://tooo1.tistory.com/561)

     

- 함수와 객체(메서드)의 구분이 느슨한 자바스크립트에서 this는 실직적으로 이 둘을 구분하는 거의 유일한 기능
- 자바스크립트에서 this는 기본적으로 실행 컨텍스트가 생성될 때 결정됨
    - this는 함수를 호출할 때 결정된다
    

### 전역공간에서의 this

- 전역 공간에서 this는 전역 객체를 가리킴
- 전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 가잠
- 브라우저 환경에서 전역 객체는 `window` 이고 Node.js 환경에서는 `global` 임
- 전역 공간에서만 발생하는 특이한 성질
    - 전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당
    - 변수이면서 객체의 프로퍼티
        - **자바스크립트의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작**
        
        ```jsx
        var a =1;
        console.log(a); //1
        console.log(window.a); //1
        console.log(this.a); //1
        ```
        
        - 사용자가 var 연산자를 이용해 변수를 선언하더라도 실제 자바스크립트 엔진은 어떤 특정 객체의 프로퍼티로 인식하는 것 (특정객체란  실행 컨테스트의 LexicalEnvironment ,L.E)
        1.  실행 컨텍스트는 변수를 수집해서 L.E의 프로퍼티로 저장함
        2. 이후 어떤 변수를 호출하면 L.E를 조회해서 일치하는 프로퍼티가 있을 경우 , 해당 값을 반환
- **전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당함**
- 전역 공간에서는 var로 변수를 선언하는 대신 window의 프로퍼티를 직접 할당하더라도 결과적으로 var을 선언한 것도 똑같이 동작

```jsx
//전역변수로 할당한 경우
var a = 1;
delete window.a; //false
console.log(a, window.a ,this.a); //1 1 1

// 전역객체로 선언한 경우
window.c =3;
delete window.c; //true
console.log(c , window.c ,this.c); //Uncaught ReferenceError : c is not defined
```

- 전역변수의 경우 ,사용자가 의도치 않게 삭제하는 것을 방지
- 전역변수로 선언하면 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 항당하면서 추가적으로 해당 프로퍼티의 configurable속성( 변경 및 삭제 가능성)을 false로 정의
- var로 선언한 전역변수와 전역객체의 프로퍼티는 호이스팅 여부 및 configurable여부에서 차이를 보임

## 메서드로 호출할 때 그 메서드 내부에서의 this

- 함수는 그 자체로 독립적인 기능을 수행하는 반면 , 메서드는 자신을 호출할 대상 객체에 관한 동작을 수행함
- 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 매서드가 되는 것이 아니라 객체의 메서드로서 호출한 경우에만 메서드로 동적하고 그렇지 않으면 함수로 동작

```jsx
//함수로서 호출
var func = function(x){
   console.log(this.x);
};
func(1); //window {.....}

//메서드로서 호출
var obj = {
    method : func
};
obj.method(2); // { method: f} 2
obj['method'](2); //{ method: f} 2
```

- 함수 앞에 점(.)이 있으면 메서드, 없으면 함수
- 어떤 함수를 호출할 때 그 함수 이름(프로퍼티 명) 앞에 갹체가 명시돼 있는 경우에는 메서드로 호출한  것이고 , 그렇지 않은 모든 경우에는 함수로 호출한 것임

### 메서드 내부에서의 this

- this에는 호출한 주체에 대한 정보가 담겨있음
- 어떤 함수를 메서드로서 호출하는 경우 , 호출 주체는 바로 함수명(프로퍼티명) 앞의 객체임

## 함수로서 호출할 때 그 함수 내부에서의 this

### 함수 내부에서의 this

- 어떤 함수를 함수로서 호출한 경우에는 this가 지정되지 않음
- this에는 호출한 주체에 대한 정보다 담기는데 함수로서 호출은 호출 주체(객체 지향 언어에서의 객체)를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이어서 호출 정보를 알 수 없음
- 함수에서의 this는 전역 객체를 가리킴
    - 더글라스 크락포드가 설계상의 오류라고 지정함

### 메서드의 내부함수에서의 this

```jsx
1.var obj1 ={
2.    outer : function(){
3.      console.log(this);        //(1)
4.      var innerFunc = function(){
5.          console.log(this);    //(2) , (3)
6.       }
7.       innerFunc();
8.
9.      var obj2 = {
10.            innerMethod: innerFunc
11.        };
12.       obj2.innerMethod();
13.      }
14.};
15. obj1.outer();

```

(1):obj1 , (2): 전역객체(window) ,(3):obj2

1. 객체를 생성하는데 , 이때 객체 개부에서는 outer라는 프로퍼티가 있으며 , 여기에는 익명함수가 연결됩니다. 이렇게 생성한 객체를 변수 obj1에 할당합니다.

15.  obj.outer을 호출합니다.

1. obj1.outer함수의 실행 컨텍스트가 생성되면서 호이스팅하고 , 스코프 체인 정보를 수집하고 this를 바인딩합니다. 이 함수는 호출할 때 함수명인 outer 앞에 점(.)이 있었으므로 매서드로서 호출한것입니다. 따라서 this에는 마지막 점 앞의 객체인 obj1이 바인딩됩니다.
2. obj1의 객체 정보가 출력됩니다.
3. 호이스팅된 변수 innerFunc는 outer 스코프 내에서만 접근할 수 있는 짖역변수입니다. 이 지역변수에 익명함수를 할당합니다.

  7. innerFunc를 호출합니다.

1. innerFunc 함수의 실행 컨텍스트가 생성되면서 호이스팅 , 스코프 체인 수집 , this 바인딩 등을 수행합니다. 이 함수를 호출할 때 함수명 앞에는 점(.)이 없었습니다. 즉 함수로서 호출한 것이므로 this가 지정됮 않았고 ,따라서 자동으로 스코프 체인상의 최상위 객체인 전역객체(window)가 바인딩 됩니다.
2. window 객체 정보가 출력됩니다.

 9. 호이스팅된 변수obj2 역시 outer스코프 내에서만 접근할 수 있는 지역변수입니다. 여기에는 다시 객체를 할당하는데 그 객체에는 innerMethod라는 프로퍼티가 있으며 , 앞서 정의된 변수 innerFunc와 연결된 익명 함수가 연결됩니다.

1. obj2.innerMethod를 호출합니다.

 9. obj2.innerMethod 함수의 실행 컨텍스트가 생성됩니다. 이 함수는 호출할 때 함수명인   innerMethod 앞에 점(.)이 있었으므로 메서드로서 호ㅜㄹ한 것입니다. 따라서 this에는 마지막 점 앞 의 객체인 obj2가 바인딩됩니다.

1. obj2 객체 정보가 출력됩니다.

**this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경(메서드 내부인지 , 함수 내부인지) 은 중요하지 않고, 오직 해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다.**

### this를 바인딩하지 않는 함수

- 화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어 , 상위 스코프의 this를 그대로 활용할 수 있음 (ES6에서만)

```jsx
var innerFunc = () =>{
   .....};
```

함수 A의 제어권을 다른 함수(또는 메서드) B에게 넘겨주는 경우 함수 A를 콜백 함수라고 한다.

이때 A는 B의 내부 로직에 따라 실행되며 , this 역시 함수B 내부 로직에서 정한 규칙에 따라 값이 결정됨

콜백 함수의 제어권을 가지는 함수(메서드)가 콜백 함수에서의 this를 무엇으로 할지를 결정하며 , 특별히 정의하지 않은 경우에는 기본적으로 함수와 마찬가지로 전역 객체를 바라봄

### 생성자 함수 내부에서의 this

- 생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용하는 함수
- 객체지향 언어에서는 생성자를 클래스(class), 클래스를 통해 만든 객체를 인스턴스(instance)라고 함
- 각 인스턴스들은 공통점들도 있지만 저마다의 개성도 존재할 수 있음
- 프로그래밍적으로 ‘생성자’는 **구체적인 인스턴스를 만들기 위한 일종의 틀**입니다.
    - 이 틀에는 해당 클래스의 공통 속성들이 미리 준비돼 있고, 여기에 구체적인 인스턴스 개성을 더해 개별 인스턴스를 만들 수 있음
- 자바스크립트는 함수에 생성자로서의 역할을 함께 부여함
- `new` 명령어와 함께 함수를 호출하면 해당 함수를 생성자로서 동작하게 됨
- 어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 `this` 는 곧 새로 만들 구체적인 인스턴스 자신이 됨
- 생성자 함수를  호출(`new` 명령어와 함께 함수를 호출)하면 우선 생성자의 prototype 프로퍼티를 참조하는 `__proto__` 라는 프로퍼티가 있는 객체(인스턴스)를 만들고, 미리 준비된 공통 속성 및 개성을 해당 객체(this)에 부여함

## 명시적으로 this를 바인딩하는 방법

### call 메서드

- call 메서드는 호출 주체인 함수를 즉시 실행하도록 하는 명령
- 이때 call 메서드의 첫번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 함
- 함수를 그냥 실행하면 this는 전역객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있음

```jsx
var obj = {
    a:1,
    method : function(x,y){
      console.log(this,a,x,y);
    }
};

obj.method(2,3);           //1 2 3
obj.method.call({a:4},5,6);  //4 5 6 
```

### apply 메서드

- apply 메서드는 call 메서드와 기능적으로 완전 동일
- call 메서드는 첫 전째 인자를 제외한 나머지 모든 인자들을 호출할 함수의 매개변수로 지정하는 반면, apply 메서드는 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정한다는 차이점이 있음

```jsx
var func = function(a,b,c){
    console.log(this,a,b,c);
};
func.apply({x:1},[4,5,6]); //{x:1} 4 5 6

var obj ={
    a:1,
    method: function(x,y){
          console.log(this.a,x,y);
   }
};
obj.method.apply({a:4},[5,6]); //4 5 6
```

### call/apply 메서드의 활용

1. **유사 배열객체(array-like object)에 배열 메서드를 적용**
    - 객체에는 배열 메서드를 직접 적용할 수 없음
    - 그러나 키가 0 또는 양의 정수인 프로퍼티가 존재하고 `length` 프로퍼티의 값이 0 또는 양의 정수인 객체, 즉 배열의 구조와 유사한 객체의 경우(유사 배열 객체) `call` 또는 `apply` 메서드를 차용할 수 있음
    - 유사배열객체에는 `call`/ `apply` 메서드를 이요해 모든 배열 메서드를 적용할 수 있음
        - 배열처럼 인덱스와 length 프로퍼티를 지니는 문자열에 대해서도 마찬가지이긴 하나 문자멸의 경우 length 프로퍼티가 읽기 전용이기 때문에 원본 문자열에 변경을 가하는 메서드(push , pop, shift, unshift , splice등)는 에러남
        - concat처럼 대상이 반드시 배열이어야하는 경우에는 에러가 나지 않지만 제대로 된 결과를 얻을 수 없음
    - ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는  `Array.from` 메서드를 새로 도입함
    
    ```jsx
    var obj ={
        0 : 'a',
        1 : 'b',
        2 : 'c',
        length :3
    };
    var arr = Array.from(obj);
    console.log(arr);  //['a','b','c']
    ```
    
    1. **생성자 내부에서 다른 생성자를 호출**
        - 생성자 내부에 다른 생서자와 공통된 내용이 있을 경우 `call` 또는 `apply` 를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있음
        
        ```jsx
        function Person(name , gender){
            this.name = name;
            this.gender = gender;
        }
        function Student(name , gender, school){
             Person.call(this , name, gender);
             this.school = school;
        }
        function Employee(name , gender, company){
            Person.apply(this , [name,gender]);
            this.company = company;
        }
        var by = new Student('보영','female','이화여대');
        var jn = new Employee('재난' , 'male' , '구글');
        ```
        
        1. **여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 - apply 활용**
        - 여러 개의 인수를 받는 메서드에게 하나의 배열로 인수를 전달하고 싶을 때 , `apply` 메서드를 사용하면 좋음
        
        ```jsx
        var numbers = [10,20,3,16,45];
        var max = Math.max.apply(null,numbers);
        var min = Math.min.apply(null,numbers);
        console.log(max,min);
        
        //ES6에서는 펼치기 연산자 활용
        const numbers = [10,20,3,16,45];
        const max = Math.max(...numbers);
        const min = Math.min(...numbers);
        console.log(max,min);
        ```
        
        - `call`/ `apply` 메서드는 명시적으로 별도의 this를 바인딩하면서 함수 또는 메서드를 실행하는 훌륭한 방법이지만 오히려 이로 인해 this를 예측하기 어렵게 만들수도 있음
        - 그럼에도 불구하고 ES5 이하의 환경에서는 마땅한 대안이 없어 실무에서 광범위하게 사용됨

### bind 메서드

- `bind` 메서드는 ES5에서 추가된 기능으로 , `call` 과 비슷하지만 즉시 호출하지는 않고 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다.
- 다시 새로운 함수를 호출할 때 인수를 넘기면 그 인수들을 기존 bind 메서드를 호출할 때 전달했던 인수들의 뒤에 이어서 등록한다.
- 즉 bind 메서드는 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적을 모두 지닌다.

**name 프로퍼티**

- `bind` 메서드를 적용해서 새로 만든 함수는 `name` 프로퍼티에 동사 bind의 수동태인 ‘bound’라는 접두어가 붙음
- 어떤 함수의 name 프로퍼티가 ‘bound xxx’라면 이는 곧 함수명이 xxx인 원본 함수에 bind 메섣를 적용한 새로운 함수라는 뜻임 - call / apply  보다 코드를 추적하기 수월함

**상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기**

- call, apply, bind 메서드를 이용하면 더 깔끔하게 처리할 수 있음

```jsx
//call로 내부함수에 this 전달
var obj = {
    outer: function(){
      console.log(this);
      var innerFunc = function(){
          console.log(this);
      };
      innerFunc.call(this);
    }
};
obj.outer();

//bind로 내부함수에 this 전달
var obj = {
    outer: function(){
    console.log(this);
    var innerFunc = function(){
         console.log(this);
    }.bind(this);
   innerFunc();
   }
};
obj.outer();
```

- 콜백 함수를 인자로 받는 함수나 메서드 중에서 기본적으로 콜백 함수 내에서의 this에 관여하는 함수 또는 메서드에 대해서도 bind 메서드를 이용하면 this 값을 사용자의 마음대로 바꿀 수 있음

### 화살표 함수의 예외사항

- ES6에 새롭게 도입된 화살표 함수는 실행 컨텍스트 생성 시 this를 바인딩하는 과정이 제외됨
- 즉 이 함수 내부에는 this가 아예 없으며 , 접근하고자 하면 스코프체인상 가장 가까운 this에 접근하게 됨

```jsx
var obj = {
    outer: function(){
       console.log(this);
       var innerFunc = () = {
           console.log(this);
      };
     innerFunc();
}
};
obj.outer();
```

- 별도의 변수로 this를 우회하거나 call/apply/bind를 적용할 필요가 없어 더욱 간결해짐

### 별도의 인자로 this를 받는 경우 (콜백 함수 내에서의 this)

- 콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg)를 인자로 지정할 수 있는 경우가 있음
- 이러한 메서드의 thisArg 값을 지정하면 콜백 함수 내부에서 this 값을 원하는 대로 변경할 수 있음
- 이런 형태는 여러 내부 요소에 대해 같은 동작을 반복 수행해야하는 배열 메서드에 많이 포진되어 있으며 , 같은 이유로 `Set` , `Map` 등의 메서드에서 일부 존재함

### 정리

- 다음 규칙은 this 바인딩이 없는 한 늘 성립함
    - 전역공간에서의 this는 전역객체(브라우저는 window, Node.js에서는 global)를 참조한다.
    - 어떤 함수를 메서드로서 호출한 경우 this는 메서드 호출 주체(메서드 앞의 객체)를 참조한다.
    - 어떤 함수를 함수로서 호출한 경우 this는 전역객체를 참조한다. 메서드 내부함수에서도 같다.
    - 콜백 함수 내부에서의 this는 해당 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우에는 전역객체를 참조한다
    - 생성자 함수에서의 this는 생성될 인스턴스를 참조함

- 명시적 this 바인딩4
    - call , apply 메서드는 this를 명시적으로 지정하면서 함수 또는 메서드를 호출한다.
    - bind 메서드는 this 및 함수를 넘길 인수를 일부 지정해서 새로운 함수를 만든다.
    - 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 this를 받기도 한다.