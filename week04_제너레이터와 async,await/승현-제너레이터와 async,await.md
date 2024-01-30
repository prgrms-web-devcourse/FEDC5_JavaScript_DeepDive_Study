# 46장 제너레이터와 async/await

## 46.1 제너레이터란?
ES6에서 도입된 **`제너레이터(generator) 함수`**는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수입니다.

#### 지금부터 제네레이터 함수와 일반 함수의 차이를 알아보도록 하겠습니다.
1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있습니다.
- 일반 함수 : 호출하면 제어권이 함수에게 넘어가고 함수 코드를 일괄 실행합니다. 즉, 호출자는 함수를 호출한 이후 함수 실행을 제어할 수 없습니다.
- 제너레이터 : 제너레이터 함수는 함수 실행을 함수 호출자가 제어할 수 있습니다. 이는 함수 호출자가 함수 실행을 일시 중지시키거나 재개시킬 수 있습니다. **함수의 제어권을 함수가 독점하는 것이 아니라 함수 호출자에게 양도(yield)할 수 있다**는 것을 의미합니다.
2. 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있습니다.
- 일반 함수 : 매개변수를 통해 외부에서 값을 주입받고 함수 코드를 일괄 실행하여 결괏값을 함수 외부로 반환합니다.
- 제너레이터 : 함수 호출자와 양방향으로 함수의 상태를 주고받을 수 있습니다. 제너레이터 함수는 **함수 호출자에게 상태를 전달할 수 있고 함수 호출자로부터 상태를 전달받을 수도 있습니다.**
3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환합니다.
- 일반 함수 : 호출하면 함수 코드를 일괄 실행하고 값을 반환합니다.
- 제너레이터 : 함수를 호출하면 함수 코드를 실행하는 것이 아니라 **이터러블이면서 이터레이터인 재너레이터 객체를 반환**합니다.

- <br/>

## 46.2 제너레이터 함수의 정의
제너레이터 함수는 **`function*`** 키워드로 선언합니다. 그리고 함수 내부에는 하나 이상의 **`yield`** 표현식을 포함해야 합니다.
```js
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genDecFunc = function* () {
  yield 1;
}

// 제너레이터 메서드
const obj = {
  * genObjMethod() {
    yield 1;
  }
}

// 제너레이터 클래스 메서드
class MyClass {
  * genClassMethod() {
    yield 1;
  }
}
```

<br/>

애스터리스크(*)의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관 없지만, 일관성을 유지하기 위해 **function 키워드 바로 뒤에 붙이는 것을 권장**합니다.
```js
function* genFunc() {
  yield 1;
}
```

<br/>

#### 제너레이터 함수는 화살표 함수로 정의할 수 없습니다.
```js
const GenArrowFunc = * () => {
 yield 1;
}; // SyntaxError : Unexpected token '*'
```

<br/>

#### 제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없습니다.
```js
function* genFunc() {
  yield 1;
}
new genFunc(); TypeError : genFunc is not a constructor
```

<br/>

## 46.3 제너레이터 객체
**제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환합니다. 제너레이터 함수가 반환한 제너레이터 객체는 이터러블(iterable)이면서 동시에 이터레이터(iterator)입니다.**

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환합니다.
const generator = genFunc();

// 제너레이터 객체는 이터러블이면서 동시에 이터레이터입니다.
console.log(Symbol.iterator in genrator); // true

// 이터레이터는 next 메서드를 갖는다.
console.log('next' in generator); // true
```

<br/>

제너레이터 객체는 **next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 `return`, `throw` 메서드를 추가로 갖습니다.**
- `next 메서드`를 호출하면 제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고 yield된 값을 value 프로퍼티 값으로, flase를 done 프로퍼티 값으로 갖는 이터레이터 객체를 반환합니다.
- `return 메서드`를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 객체를 반환합니다.
```js
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (error) {
    console.error(error);
  }
}

const generator = genFunc();

console.log(generator.next()); // { value: 1, done: false }
console.log(generator.return('End!')); // { value: 'End!', dont: true }
```

<br/>

- `throw` 메서드를 호출하면 인수로 전달받은 에러를 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 객체를 반환합니다.
```js
function* genFunc(){
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (error) {
    console.error(error);
  }
}

const generator = genFunc();

console.log(generator.throw('Error!')); // { value: 'Error!', done: true }
```

<br/>

## 46.4 제너레이터의 일시 중지와 재개
제너레이터 함수는 `yield` 키워드와 `next` 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개할 수 있습니다.

일반 함수는 호출 이후 제어권을 함수가 독점 하지만 **제너레이터 함수는 호출자에게 제어권을 양도(`yield`)하여 필요한 시점에 함수 실행을 재개할 수 있습니다.**

**`yield` 키워드는 제너레이터 함수의 실행을 일시 중지시키거나 yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환합니다.**

다음 예제를 통해 제너레이터 함수 동작을 알아보겠습니다.
```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체가 반환 됩니다.
// 제너레이터 객체는 이터러블이면서 이터레이터인 next, return ,throw 메서드를 갖습니다.
const generator = genFunc();

// 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 함수가 일시 중지됩니다.
// next 메서드는 { value, done } 프로퍼티를 반환합니다.
// value 프로퍼티는 yield 표현식에서 yield 된 값 1이 할당됩니다.
// done 프로퍼티는 제너레이터 함수가 끝까지 실행되었는지를 boolean 값으로 나타냅니다.
console.log(generator.next()); // { value: 1, done: flase }

// 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 함수가 실행되고 일시 중지됩니다.
console.log(generator.next()); { value: 2, done: false }

// 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 함수가 실행되고 일시 중지됩니다.
console.log(generator.next()); { value: 3, done: false }

// 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행됩니다.
// 이때 제너레이터 함수가 끝까지 실행 되었으므로 done 프로퍼티는 ture로 할당됩니다.
console.log(generator.next()); { value: undefined, done: true }
```
**제너레이터 객체의 `next 메서드`를 호출하면 `yield 표현식`까지 실행되고 일시 중지됩니다. 이때 함수의 제어권이 호출자로 양도(`yield`)됩니다.**

제너레이터 객체의 next 메서드는 { value, done } 프로퍼티를 갖는 이터레이터 리절트 객체를 반환합니다. 

이때 next 메서드가 반환한 이터레이터 리절트 객체의 `value 프로퍼티`에는 **yield 표현식에서 yield된 값(yield 키워드 뒤의 값)이 할당**되고 `done 프로퍼티`에는 **제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값**이 할당됩니다.

<br/>

> 이터레이터의 next 메서드와 달리 제너레이터 객체의 next 메서드에는 인수를 전달할 수 있습니다. 제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당됩니다.
> yield 표현식을 할당받은 변수에 yield 표현식의 평가 결과가 할당되지 않는 것에 주의하기 바랍니다.

```js
function* genFunc() {
  const x = yield 1;
  const y = yield(x + 10);
  return x + y;
}

const generator = genFunc();
let res = generator.next();
console.log(res); // { value: 1, done: flase }

res = generator.next(10);
console.log(res); // { value: 20, done: false }

res = generator.next(20);
console.log(res); // { value: 30, done: true }
```
이처럼 제너레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있습니다. 함수 호출자는 next 메서드를 통해 yield 표현식까지 함수를 실행시켜 제너레이터 객체가 관리하는 상태를 꺼내올 수 있고, next 메서드에 인수를 전달해서 제너레이터 객체에 상태를 밀어넣을 수 있습니다.

<br/>

## 46.5 제너레이터의 활용
### 46.5.1 이터러블의 구현
제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블을 구현할 수 있습니다.

먼저 이터레이션 프로토콜을 준수하여 무한 피보나치 수열을 생성하는 함수를 구현해보겠습니다.
```js
const infiniteFibonacci = (function() {
    let [pre, cur] = [0, 1];

    return {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            [pre, cur] = [cur, pre + cur];
            // 무한 이터러블이므로 done 프로퍼티를 생략한다.
            return {
                value: cur
            };
        }
    };
}());

// console.log(infiniteFibonacci[Symbol.iterator]())
// console.log(infiniteFibonacci.next());
// console.log(infiniteFibonacci.next());
// console.log(infiniteFibonacci.next());

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
    if (num > 10000) break;
    console.log(num);

}
```

이번에는 제너레이터를 사용하여 무한 피보나치 수열을 생성하는 함수를 구현해보겠습니다.
```js
// 무한 이터러블을 생성하는 제너레이터 함수
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while(true) {
    [pre, cur] = [cur, pre+cur];
    yield cur;
  }
}());

// infiniteFibonacci는 무한 이터러블입니다.
for(const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8... 2584. 4181, 6765
}
```

<br/>

### 46.5.2 비동기 처리
재네레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있습니다. 이러한 특성을 활용하면 프로미스를 사용한 비동기 처리를 동기 처럼 구현할 수 있습니다.

다음 예제를 통해 살펴보겠습니다.
```js
const async = generatorFunc => {
    const generator = generatorFunc();

    const onReserved = arg => {
        const result = generator.next(arg);

        return result.done ? result.value : result.value.then(res => onReserved(res));
    }

    return onReserved;
}

(async (function* fetchTodo() {
    const url = 'https://jsonplaceholder.typicode.com/todos/1';

    const response = yield fetch(url);
    const todo = yield response.json();
    console.log(todo);
})())
```
1. async 함수가 호출되면 전달받은 제너레이터 함수 fetchTodo를 호출하여 제너레이터 객체를 생성하고 onReserved 함수를 반환합니다. onReserved 함수는 상위 스코프의 generator 변수를 기억하는 클로저입니다. async 함수가 반환한 onReserved 함수를 즉시 호출하여 next 메서드를 처음 호출합니다.
2. next 메서드가 처음 호출되면 제너레이터 함수 fetchTodo의 첫 번째 yield 문까지 실행됩니다. 이때 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값. 즉 첫 번째 yield된 fetch 함수가 반환한 프로미스가 resolve한 Response 객체를 onReserved 함수에 인수로 전달하면서 재귀 호출합니다.
3. onReserved 함수에 인수로 전달된 Response 객체를 next 메서드에 인수로 전달하면서 next 메서드를 두 번째로 호출합니다. 이때 next 메서드에 인수로 전달한 Response 객체는 제너레이터 함수 fetchTodo의 response 변수에 할당되고 제너레이터 함수 fetchTodo의 두 번째 yield문까지 실행됩니다.
4. next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값. 즉 두 번째 yield된 response.json 메서드가 반환한 프로미스가 resolve한 todo 객체를 onReserved 함수에 인수로 전달하면서 재귀 호출합니다.
5. onReserved 함수에 인수로 전달된 todo 객체를 next 메서드에 인수로 전달하면서 next 메서드를 세 번째로 호출합니다. next 메서드에 인수로 전달한 todo 객체는 제너레이터 함수 fetchTodo의 todo 변수에 할당되고 제너레이터 함수 fetchTodo가 끝까지 실행됩니다.
6. next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true. 즉 제너레이터 함수 fetchTodo가 끝까지 실행되었다면 이터레이터 리절트 객체와 value 프로퍼티 값을 undefined로 반환하고 종료됩니다.

<br/>

## 46.6 async/await
ES8(ECMAScript 2017)에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 `async/await`가 도입되었습니다.

**`async/await`를 사용하면 프로미스의 후속 처리 메서드를 사용하지 않고 동기 처럼 프로미스를 사용할 수 있습니다.**
```js
async function fetchTodo() {
    const url = 'https://jsonplaceholder.typicode.com/todos/1';

    const response = await fetch(url);
    const todo = await response.json();
    console.log(todo); // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}
```

<br/>

### 46.6.1 async 함수
`await` 키워드는 반드시 `async` 함수 내부에서 사용해야 합니다. `async 함수`는 async 키워드를 사용해 정의할 수 있으며 **언제나 프로미스를 반환**합니다. async 함수가 명시적으로 프로미스를 반환하지 않더라도 async 함수는 **암묵적으로 반환값을 resolve하는 프로미스를 반환합니다.**

```js
// async 함수 선언문
async function foo(n) { return n }
foo(1).then(v => console.log(v)); // 1

// async 함수 표현식
const bar = async function(n) { return n };
bar(2).then(v => console.log(v)); // 2

// async 화살표 함수
const baz = async n => n;
baz(n).then(v => console.log(v)); // 3

// async 메서드
const obj = {
  async foo(n) { return n; }
};
obj.foo(4).then(v => console.log(v)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) { return n; }
}
const myClass = new MyClass();
myClass.bar(5).then(v => console.log(v)); // 5
```
<br/>

클래스의 constructor 메서드는 async 메서드가 될 수 없습니다. **클래스의 constructor 메서드는 인스턴스를 반환**해야 하지만 **async 함수는 언제나 프로미스를 반환**해야 합니다.
```js
class MyClass {
  async constructor() { }
  // SyntaxError: class constructor may not be an async method
}
const myClass = new MyClass();
```

<br/>

### 46.6.2 await 키워드
**`await` 키워드는 프로미스가 `settled` 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 `settled` 상태가 되면 프로미스가 resolve한 처리 결과를 반환**한다. `awiat` 키워드는 반드시 프로미스 앞에서 사용해야합니다.
```js
const getGithubUserName = async id => {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  console.log(name); // seunghyune
};

getGithubUserName('SeungHyune')
```
`await` 키워드는 프로미스가 `settled` 상태가 될 때까지 대기하다 프로미스가 `settled` 상태가 되면 프로미스가 resolve한 결과를 res 변수에 할당합니다.

이처럼 await 키워드는 다음 실행을 일시 중지시켰다가 프로미스가 settled 상태가 되면 다시 재개합니다.

아래 예제를 통해 await 키워드의 동작을 순차적으로 알아보겠습니다.
```js
async function foo() {
  const a = await new Promise(resolve => setTimeout(() => resolve(1), 3000));
  const b = await new Promise(resolve => setTimeout(() => resolve(2), 2000));
  const c = await new Promise(resolve => setTimeout(() => resolve(3), 1000));

  console.log([a, b, c]); // [1, 2, 3]
}

foo(); // 약 6초 소요됩니다.
```
위 예제의 foo 함수는 종료될 때까지 6초가 소요됩니다. 그 이유는 await 키워드는 프로미스가 settled 상태가 될 때까지 대기하기 때문입니다. setTimeout 함수가 종료되면 그때 프로미스는 settled 상태가 되어 다음 코드가 실행됩니다.

이렇게 서로 연관이 없는 비동기 처리는 개별적으로 수행되는 비동기 이므로 처리가 완료될 때까지 대기하여 순차적으로 처리할 필요가 없습니다. 이때 `Promise.all()`을 활용할 수 있습니다.
```js
async function foo() {
  const res = Promise.all([
    new Promise(resolve => setTimeout(() => resolve(1), 3000));
    new Promise(resolve => setTimeout(() => resolve(2), 2000));
    new Promise(resolve => setTimeout(() => resolve(3), 1000));
  ])

  console.log(res); // [1, 2, 3]
}

foo(); // 약 3초 소요됩니다.
```
`Promise.all()`을 활용하면 프로미스가 병럴 처리 됩니다.

다음 예제의 경우
- 첫 번째 프로미스는 3초 후에 1을 resolve 합니다.
- 두 번째 프로미스는 2초 후에 2을 resolve 합니다.
- 세 번째 프로미스는 1초 후에 3을 resolve 합니다.

그렇게 약 3초 후에 **모든 프로미스가 fullfilled 상태가 되면 모든 프로미스를 배열에 저장하고 새로운 프로미스를 반환**합니다.

<br/>

### 46.6.3 에러 처리
비동기 처리를 위한 콜백 패턴의 단점 중 가장 심각한 것은 에러 처리가 곤란하다는 것입니다. 에러는 `호출자` 방향으로 전파됩니다. 즉 콜 스택의 아래 방향(실행중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파됩니다. 하지만 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 `try...catch` 문을 사용해 에러를 캐치할 수 없습니다.
```js
try {
  setTimeout(() => {
    throw new Error('Error!');
  }, 1000)'
} catch (error) {
    // 에러를 캐치하지 못한다.
    console.error(error);
}
```
`async/await`에서 에러 처리는 `try...catch`문을 사용할 수 있습니다.

콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확합니다.
```js
const foo = async () => {
  try {
    const url = 'https://wrong.url';

    const response = await fetch(url);
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error); // TypeError : Failed to fetch
  }
}
foo();
```
위 예제의 foo 함수의 catch 문은 HTTP 통신에서 발생한 네트워크 에러뿐 아니라 try 코드 블록 내의 모든문에서 발생한 일반적인 에러까지 모두 캐치할 수 있습니다.

**async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환합니다.**

Promise.prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수도 있습니다.
