03 this
<br></br>
this 는 함수와 메서드의 구분이 느슨한 자바스크립트에서 this 는 실질적으로 이 둘을 구부하는 거의 유일한 기능

1. 상황에 따라 달라지는 this (this 는 함수 호출 시 결정된다.)

   1. 전역 공간에서의 this<br></br>

      전역 공간에서의 this 는 전역 객체를 가리킨다. (전역 켄텍스트를 생성하는 주체가 전역 객체이기 때문)<br></br>

      전역 객체는 브라우저 환경에서는 window, Node js 에서는 global 로 불린다.<br></br>

      +a 전역 공강ㄴ에서만 발생하는 특이 성질<br></br>

      전역 변수 선언 시 자스 엔진은 이를 전역 객체의 프로퍼티로 할당함<br></br>

      따라서 대부분의 경우 전역 공간에서는 var 로 변수를 선언하는 대신 window 프로퍼티에 직접 할당해도 똑같이 동작. 단 삭제 명령은 예외.<br></br>

      처음부터 전역 객체의 프로퍼티로 할당한 경우에는 삭제가 되는 반면 전역변수로 선언한 경우에는 삭제가 되지 않는다. 이는 사용자가 의도치 않게 삭제하는 것을 방지하는 차원에서 만들어진 방어 전략 때문.<br></br>

   2. 메서드로 호출할 때 그 메서드 내부에서의 this<br></br>

      함수 vs 메서드 (미리 정의한 동작을 수행하는 코드 뭉치)<br></br>

      함수 실행하는 두 가지 방법, 유일한 차이는 독립성<br></br>

      함수는 그자체로 독립적인 기능 수행<br></br>

      메서드는 자신을 호출한 대상 객체에 관한 동작 수행<br></br>

      자스에서는 상황별로 this 키워드에 다른 값을 부여함으로써 구현 \* 주의 : 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로 무조건 메소드가 되는 것이 아니라 객체의 메서드로 호출한 경우에만 메서드로 동작, 그렇지 않은 경우 함수로 동작<br></br>

      함수 호출 Vs 메서드로서 호출<br></br>

      함수 호출 시 그 함수 이름 앞에 객체가 명시돼 있는 경우에는 메서드로 호출한 것, 그렇지 않느 경우에는 함수로 호출한 것.<br></br>

      메서드 내부에서의 this : this 에는 호출한 주체에 대한 정보가 담긴다. 어떤 함수를 메서드로서 호출하는 경우 호출 주체는 함수명 앞의 객체이다. (점 표기법의 경우 마지막 점 앞에 명시된 객체)<br></br>

   3. 함수로서 호출할 때 그 함수 내부에서의 this<br></br>

      어떤 함수를 함수로써 호출할 경우에는 this 가 지정되지 않는다. 함수로서 호출하는 것은 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없다. 2장에서 실행 컨텍스트를 활성화할 당시에 this 가 지정되지 않은 경우 this 는 전역 객체를 바라본다고 했으므로 함수에서의 this 는 전역 객체를 가리킨다. - 메서드의 내부 함수에서의 this 를 우회하는 방법<br></br>

      호출 주체가 없을 경우 자동으로 전역객체를 바인딩하지 않고 호출 당시 주변 환경의 this 를 그대로 상속받아 사용하고 싶다면 변수를 활용하자.<br></br>

      변수에 상위 스코프의 this 값을 저장한 다음에 내부함수에서 활용한다. (주로 self 라는 변수명 사용. 컨벤션 준수하기)<br></br>

      ES6에서의 화살표 함수를 이용하면 우회로를 사용하지 않고도 자동으로 상위 스코프의 this 활용 가능<br></br>

   4. 콜백 함수 호출 시 그 함수 내부에서의 this<br></br>

      함수 a의 제어권을 다른 함수/메서드 b에게 넘겨주는 경우 함수 a를 콜백 함수라고 한다. 함수 a는 함수 b의 내부 로직에 따라 실행되며 this 역시 함수 b 내부 로직에서 정한 규칙에 따라 값이 결정된다.<br></br>

      즉 콜백 함수도 함수이므로 기본적으로 this 가 전역 객체를 참조하지만, 제어권을 받은 함수에서 콜백함수에 별도로 this 가 될 값을 지정한 경우에는 그 대상을 참조한다.<br></br>

   5. 생성자 함수 내부에서의 this<br></br>

      생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는데 사용하는 함수이다. (생성자 = 구체적인 인스턴스를 만들기 위한 틀)<br></br>

      js 는 new 명령어와 함께 함수 호출 시 해당 함수가 생성자로 동작한다.<br></br>

      어떤 함수가 생성자 함수로서 호출된 경우, 내부에서 this 는 곧 새로 만들 구체적인 인스턴스 자신이다.<br></br>

1. 명시적으로 this 를 바인딩하는 방법<br></br>

   앞서 살펴 본 규칙들을 깨고 this 에 별도의 대상을 binding 할 수도 있다.<br></br>

   1. call 메서드<br></br>

      call 메서드는 메서드 호출 주체인 함수를 즉시 실행하도록 하는 명령어. 이때 첫 인자를 this 로 바인딩하고, 이후 인자는 매개변수로 사용한다. call 메서드 사용 시 함수 호출을 해도 임의의 객체를 this 로 지정할 수 있다. 메서도 호출 시에도 마찬가지로 임의의 객체를 this 로 바인딩 가능.<br></br>

   2. apply 메서드<br></br>

      call 메서드와 동일한데, 두 번째 인자로 배열을 받아 그 배열 요소를 매개변수로 지정한다는 차이 하나만 있다.<br></br>

   3. call / apply 메서드 활용 1) 유사배열객체에 배열 메서드 적용<br></br>

      본래 객체에서는 배열 메서드를 적용할 수 없다. 그러나 키가 0또는 양의 정수인 프로퍼티가 존재하고 length 프로퍼티가 0이상인, 배열과 유사한 특징을 갖는 객체에서는 call/apply 메서드를 사용해 배열 메서드를 차용할 수 있다. 2) 생성자 내부에서 다른 생성자 호출<br></br>

      생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call/apply를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있다. 3) 여러 인수들을 묶어 하나의 배열로 전달<br></br>

   4. bind 메서드<br></br>

      call과 비슷하지만 즉시 호출하지는 않고 넘겨 받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드<br></br>

      다시 새로운 함수를 호출할 때 인수를 넘기면 그 인수들은 기존 bind 메서드를 호출할 때 전달했던 인수들의 뒤에 이어서 등록된다.<br></br>
