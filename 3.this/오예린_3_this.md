# 3.this

## 상황에 따라 달라지는 this
-----

* 자바스크립트에서 this는 함수를 호출할 때 결정됨 == 함수를 어떤 방식으로 호출하느냐에 따라 값이 달라짐


### 전역 공간에서의 this

* 전역 공간에서 this는 전역 객체를 가리킴
* 전역 객체는 전역 컨택스트를 생성하는 주체!
    - 전역 객체는 런타임 환경에 따라 다른 이름과 정보를 가지고 있음
    - 브라우저 환경에서는 window, Node.js 환경에서는 global임
        ```javascript
        //브라우저 환경에서 this
        console.log(this === window); //true

        //Node.js 환경에서의 this
        console.log(this === global); //true
        ```

    * 자바스크립트의 모든 변수는 특정 객체의 프로퍼티로 동작
      → 실행 컨텍스트는 변수를 수집해 L.E의 프로퍼티로 저장
      → 변수 호출 시 L.E를 조회해 일치하는 프로퍼티가 있다면 그를 반환 (전역 컨텍스트의 경우 L.E는 전역객체를 그대로 참조한다고 함)

    * 전역변수 선언 시 자바스크립트 엔진은 전역변수를 전역객체의 프로퍼티로 할당함
        - 전역 공간에서는 var로 변수를 선언하는 대신 window의 프로퍼티에 직접 할당해도 똑같이 동작을 함
            ```javascript
            var a = 1;
            window.b = 2;
            console.log(a, window.a, this.a); // 1 1 1
            console.log(b, window.b, this.b); // 2 2 2

            window.a = 3;
            b = 4;
            console.log(a, window.a, this.a); // 3 3 3
            console.log(b, window.b, this.b); // 4 4 4
            ```
        - 하지만 삭제 명령에 대해서는 이 말이 통하지 않음
            - 처음부터 전역객체의 프로퍼티로 할당한 경우 삭제가 가능하지만 전역변수로 선언한 경우 삭제가 되지 않음
                - 이는 전역변수로 선언을 하게 되면 자바스크립트 엔진이 자동으로 이를 전역객체 프로퍼티로 할당하며 해당 프로퍼티에 configurable 속성(변경/삭제 가능성)을 false로 지정해놓기 때문
                ```javascript
                    var a = 1;
                    delete window.a; //false. 이는 delete a로 사용 가능
                    console.log(a, window.a, this.a); // 1 1 1

                    window.c = 3;
                    delete window.c; //true. 이는 delete c로 사용 가능
                    console.log(c, window.c, this.c); //uncaught referenceError: c is not defined
                ```

### 메서드로서 호출할 때 그 메서드 내부에서의 this



__함수 vs 메서드__

* 함수와 메서드를 구분하는 유일한 차이는 __독립성__ 임

    - __함수__ 는 _그 자체로 독립적인 기능을 수행_ 하지만 __메서드__ 는 _자신을 호출한 대상 객체에 관한 기능을 수행 함_
    - 자바스크립트에서는 this 키워드를 통해 이 차이를 구현할 수 있음

* 함수로서 호출과 메서드로서 호출 구분하기 by this 키워드

    → 함수 호출 시 함수 앞에 객체가 표현되어 있다면 메서드로 구현한 것! (점 표기법, 대괄호 표기법은 상관 X)

    ```javascript
        var obj = {
            method: function(x);
        };
        obj.method(1); //{method: f} 2
        obj['method'](2);//{method: f} 2. 둘 다 메서드로 구현했음을 알 수 있음
    ```

__메서드 내부에서의 this__

* this에는 호출한 주체에 대한 정보가 담기므로, 메서드로서 호출하는 경우 호출 주체는 함수명 앞의 객체가 됨
    ```javascript
        var obj = {
            method: function(){
                console.log(this);
            },
            inner:{
                methodB: function() {console.log(this);}
            }
        };
        obj.methodA(); //{ methodA: f, innder: {...}} (=== obj에 해당하는 값 출력)
        obj.inner.methodB(); // {methodB: f} (=== obj.inner에 해당하는 값 출력)
    ```


### 함수로서 호출할 때 그 함수 내부에서의 this

__함수 내부에서의 this__

* 함수를 함수로서 호출할 경우 this가 지정되지 않음 → 함수에서의 this는 전역 객체를 가리킴
    - this에는 호출한 주체에 대한 정보가 담긴다고 했는데, 함수로서 호출하는 건 호출 주체를 명시하지 않고 개발자의 관여로 실행한 것이기에 호출 주체의 정보를 알 수 없기 때문임


__메서드의 내부 함수에서의 this__

* this 바인딩에 관해서는 함수를 실행하는 당시의 주변 환경 (메서드 내부 or 함수 내부)은 중요하지 않고, 오직 해당 함수를 호출하는 구문 앞에 점 혹은 대괄호 표기의 유무가 결정을 함 

__메서드의 내부 함수에서의 this를 우회하는 방법__

* __변수를 활용하는 방법__
    ```javascript
        var obj = {
            outer: function(){
                console.log(this);
                var innerFunc1 = function(){
                    console.log(this);
                };
                innerFunc1();

                var self = this;
                var innerFunc2 = function() {
                    console.log(self);
                };
                innerFunc2();
            }
        };
        obj.outer();
    ```
    - innerFunc1 내부에서 this는 전역객체를 가리킴. 하지만 outer 스코프에서 self라는 변수에 this를 저장하고 호출한 innerFunc2의 경우 self에는 객체 obj가 출력이 됨. 즉, 상위 스코프의 this를 저장해 내부함수에서 활용하는 수단임  



* __this를 바인딩하지 않는 함수(ES6 only)__
    - 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하기 위해서 this를 바인딩하지 않는 화살표 함수 (arrow function)을 새로 도입함
    - 화살표 함수란 실행 컨텍스트 생성 시 this 바인딩 과정을 없애 상위 스코프의 this를 그대로 활용할 수 있게 해주는 함수 형태
    - ES6에서만 활용 가능하며, ES5에서는 지원하지 않음



### 콜백 함수 호출 시 그 함수 내부에서의 this

* 콜백 함수란?
    - 어떤 함수 A의 제어권을 다른 함수(혹은 메서드) B에게 넘겨주는 경우 A를 의미

* 콜백 함수도 함수이기 떄문에 this가 전역 객체를 참조하지만, 제어권을 받은 함수에서 콜백 함수에 별도로 this를 지정한 경우 해당 대상을 참조하게 됨 


### 생성자 함수 내부에서의 this

* 생성자 함수란?
    - 공통된 성질을 지니는 객체들을 생성할 때 사용하는 함수
    - 객체지향 언어에서는 생성자를 클래스라고 하며 클래스가 생성한 객체는 인스턴스임
    - 즉, 생성자 함수는 구체적인 인스턴스를 만들기 위한 일종의 틀을 가진 함수라 할 수 있음


* new 명령어와 함께 함수 호출 시 함수는 생성자로서 역할
* 생성이 되었다면 내부에서 this는 곧 새로 만들 구체적인 인스턴스가 됨! 


---

## 명시적으로 this 바인딩하는 방법
---

앞에서는 this에 어떤 값이 바인딩되는지 확인해보았지만, 이러한 규칙을 깨고 this에 별도의 대상을 바인딩하는 방법도 있음!

### call 메서드

* 메서드 호출 주체인 함수를 즉시 실행하도록 하는 명령어
```javascript
    Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```
* call 메서드의 첫 번째 인자를 this로 바인딩하며, 이후 인자들은 호출할 함수의 매개변수
* 임의의 객체를 this로 지정할 수 있음!

```javascript
    var func = function(a, b, c){
        console.log(this, a, b, c);
    };

    func(1, 2, 3); //Window{...} 1 2 3
    func.call({x : 1}, 4, 5, 6); // {x: 1} 4 5 6
```

### apply 메서드

* call 메서드와 기능적으로 완전 동일
```javascript
    Function.prototype.apply(thisArg[, argsArray])
```
* 다만, call과 다르게 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정함


```javascript
    var func = function(a, b, c){
        console.log(this, a, b, c);
    };

    func.call({x : 1}, [4, 5, 6]); // {x: 1} 4 5 6
```


### call / apply 메서드 활용
* call이나 apply 메서드 활용 시 자바스크립트 더욱 다채롭게 활용 가능함!:) <br>

__유사배열객체(array-like object)에 배열 메서드 적용__

* 키가 0 또는 양의 정수인 프로퍼티가 존재하고 length 프로퍼티 값이 0 또는 양의 정수인 객체의 경우 call 또는 apply 메서드를 통해 배열 메서드를 차용할 수 있음

```javascript
    var obj ={
        0: 'a',
        1: 'b',
        2: 'c',
        length: 3
    };
    
    Array.prototype.push.call(obj, 'd'); //배열 메서드인 push를 객체 obj에 적용해 프로퍼티 3에 d 추가
    console.log(obj); //{ 0: 'a', 1: 'b', 2: 'c', 3: 'd', length: 4}

    var arr = Array.prototype.slice.call(obj); //slice 메서드를 적용해 객체를 배열로 전환해 리턴
    console.log(arr); // {'a', 'b', 'c', 'd'}
```
cf) slice 메서드는 시작 인덱스값과 마지막 인덱스 값을 받아 시작값부터 마지막값의 앞 부분까지 배열 요소를 추출하는 메서드. 매개변수가 없을 경우 원본 배열의 얕은 복사본을 리턴해줌. 여기서도 매개변수가 없는 형태임!

* 뿐만 아니라 NodeList, 문자열에도 call과 apply 메서드가 활용 가능함
* ES6에서는 이에 유사배열객체 혹은 순회 가능한 모든 종류의 데이터 타입을 배열로 전화해주는 __Array.from__ 메서드를 지원함
    ```javascript
        var obj = {
            0: 'a',
            1: 'b',
            2: 'c',
            length : 3
        };
        var arr = Array.from(obj);
        console.log(arr); //['a', 'b', 'c']
    ```


__생성자 내부에서 다른 생성자 호출하기__

* 생성자 내부에서 다른 생성자와 공통된 경우가 있을 때, call이나 apply를 이용해 다른 생성자를 호출함으로서 반복을 줄일 수 있음 

```javascript
    function Person(name, gender){
        this.name = name;
        this.gender = gender;
    }
    function Student(name , gender, school){
        Person.call(this, name, gender); //call로 다른 생성자 호출
        this.school = school;
    }
    function Employee(name, gender, company){
        Person.apply(this, [name, gender]);//apply로 다른 생성자 호출
        this.company = company;
    }
    var by = new Student('보영', 'female', '이대');
    var jn = new Employee('재난', 'male', '구글');
```

__여러 인수 묶어 하나의 배열로 전달할 때 → apply 사용__

* 여러 인수를 받는 메서드에게 하나의 배열로 인수들 전달하고 싶을 때 apply 메서드 활용 가능
    - 훨씬 간결한 코드 활용 가능
    - 가독성도 높일 수 있음
    - 다만 this를 예측하기 어려운 경우를 만들어낼 수 있기 떄문에 코드 해석이 어려워질 수 있음

    ```javascript
        var numbers = [10, 20, 3, 16, 23];
        var max = Math.max.apply(null, numbers);
        var min = Math.min.apply(null, numbers);
        console.log(max, min); // 45 3 

        //ES6의 경우 펼치기 연산자를 제공하는데, 이는 apply를 적용하는 것보다 간단함
        const numbers = [10, 20, 3, 16, 23];
        const max = Math.max(...numbers);
        const min = Math.min(...numbers);
        console.log(max, min); // 45 3
    ```


### bind 메서드

```javascript
    Function.prototype.bind(thisArg[, arg1[, arg2[, ...]]])
```

* call과 비슷하지만 즉시 호출 X, 넘겨받은 this 및 인수들로 새로운 함수를 리턴해주는 메서드
* 새로운 함수 호출해 인수 넘길 경우 기존 bind 메서드를 호출할 때 전달한 인수들 뒤에 등록됨
* 함수에 this를 미리 적용하고 부분 적용 함수를 구현하는 목적을 가짐

```javascript
    var func = function(a, b, c, d){
        console.log(this, a, b, c, d);
    };
    func(1, 2, 3, 4); //Window{ ... } 1 2 3 4 

    var bindFunc1 = func.bind({x : 1}); //this를 {x:1}로 지정한 함수 bindFunc1
    bindFunc1(5, 6, 7, 8); // {x : 1} 5 6 7 8 

    //부분 적용 함수 구현
    var bindFunc2 = func.bind({x: 1}, 4, 5);//this를 {x:1}로 지정하고 매개변수 4, 5를 담은 함수 bindFunc2
    bindFunc2(6, 7); //this 값 변경한 걸 제외하고 최초 함수에 4, 5, 6, 7을 넘긴 것과 동일
    bindFunc2(8, 9); // 마찬가지
```
__name 프로퍼티__

* name 프로퍼티에 **bound**라는 접두어가 붙음
* 이는 원본 함수에 bind 메서드를 적용한 새로운 함수를 의미

__상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기__

* 앞에서 언급된 우회법보다 call, apply, bind 메서드를 이용시 더 깔끔함
    ```javascript  
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

* 콜백 함수를 인자로 받는 함수/메서드 중에서 기본적으로 콜백 함수 내에서의 this에 관여하는 함수/메서드에 대해서 bind 메서드 적용시 this 값을 사용자가 쉽게 접근 가능함

### 화살표 함수의 예외 사항 

* 화살표 함수의 경우 실행 컨텍스트 생성 시 this를 바인딩하는 과정이 제외됨 → this가 아예 없음!
    - this 접근 시 스코프 체인 상 가장 가까운 this에 접근함
* 별도의 변수로 우회하거나, call/apply/bind 메서드를 이용해 우회하는 것보다 더 간단한 방법

```javascript
    var obj = {
        outer: function(){
            console.log(this);
            var innerFunc = () => {
                console.log(this);
            };
            innerFunc();
        }
    };
    obj.outer(); //별도 변수로 우회 X, call/apply/bind 사용 X. 간단함!!
```

### 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

* 콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(thisArg)를 인자로 지정 가능
* 보통 여러 내부 요소에 대해 같은 동작을 반복하는 **배열 메서드에 많이 존재**
    - 동일한 이유로 ES6의 Set, Map 메서드에서도 존재함
