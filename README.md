# 함수형 프로그래밍과 ES6+ 예제 코드

## 강의 목록

1.  함수형 자바스크립트 기본기

    1. 평가와 일급

       ```javascript
       # 평가
         - 코드가 계산(Evaluation) 되어 값을 만드는 것

       # 일급
         - `값`으로 다룰 수 있다.
         - `변수`에 담을 수 있다.
         - 함수의 `인자`로 사용될 수 있다.
         - 함수의 `결과`로 사용될 수 있다.
       ```

    2. 일급 함수

       ```javascript
       # 일급 함수
         - 함수를 `값`으로 다룰 수 있다.
         - 조합성과 추상화의 도구
       ```

    3. 고차 함수

       ```javascript
       # 고차 함수
        - 함수를 `값`으로 다루는 함수
        - 함수를 `인자`로 받아서 실행하는 함수
        - 함수를 만들어 리턴하는 함수 (`클로저`를 만들어 리턴하는 함수)
       ```

    4. 부수효과와 순수 함수

2.  ES6에서의 순회와 이터러블/이터레이터 프로토콜

    1. 기존과 달라진 ES6에서의 리스트 순회
       ```javascript
       - for i++
       - for of
       ```
    2. Array, Set, Map을 통해 알아보는 이터러블/이터레이터 프로토콜

       - 이터러블: `이터레이터를 리턴`하는 `[Symbol.iterator]() 를 가진 값`

       - 이터레이터: `{ value, done } 객체를 리턴`하는 `next() 를 가진 값`

       - 이터러블/이터레이터 프로토콜: `이터러블`을 `for...of`, `전개 연산자` 등과 함께 동작하도록한 규약

       ```javascript
       log("Set -----------");
       const set = new Set([1, 2, 3]);
       for (const a of set) log(a);

       log("Map -----------");
       const map = new Map([
         ["a", 1],
         ["b", 2],
         ["c", 3],
       ]);

       for (const a of map.keys()) log(a);
       for (const a of map.values()) log(a);
       for (const a of map.entries()) log(a);
       ```

    3. 사용자 정의 이터러블
       ```javascript
       const iterable = {
         [Symbol.iterator]() {
           let i = 3;
           return {
             next() {
               return i == 0 ? { done: true } : { value: i--, done: false };
             },
             [Symbol.iterator]() {
               return this;
             },
           };
         },
       };
       let iterator = iterable[Symbol.iterator]();
       iterator.next();
       iterator.next();
       ```
    4. 전개 연산자

       ```javascript
       const a = [1, 2];
       // a[Symbol.iterator] = null;
       log([...a, ...arr, ...set, ...map.keys()]);
       ```

3.  제너레이터와 이터레이터

    1. 제너레이터와 이터레이터

       - 제너레이터: `이터레이터`이자 `이터러블을 생성`하는 함수

       ```javascript
       function* gen() {
         yield 1;
         if (false) yield 2;
         yield 3;
       }

       let iter = gen();
       log(iter[Symbol.iterator]() == iter);
       log(iter.next());
       log(iter.next());
       log(iter.next());
       log(iter.next());

       for (const a of gen()) log(a);
       ```

    2. odds

       ```javascript
       function* infinity(i = 0) {
         while (true) yield i++;
       }

       function* limit(l, iter) {
         for (const a of iter) {
           yield a;
           if (a == l) return;
         }
       }

       function* odds(l) {
         for (const a of limit(l, infinity(1))) {
           if (a % 2) yield a;
         }
       }

       for (const a of odds(40)) log(a);
       ```

    3. for...of, 전개 연산자, 구조 분해, 나머지 연산자

       ```javascript
       // 전개 연사자
       log(...odds(10));
       log([...odds(10), ...odds(20)]);

       // 구조 분해 & 나머지 연산자
       const [head, ...tail] = odds(5);
       log(head);
       log(tail);

       const [a, b, ...rest] = odds(10);
       log(a);
       log(b);
       log(rest);
       ```

4.  map, filter, reduce

    1. map

       ```javascript
       const map = (f, iter) => {
         let res = [];
         for (const a of iter) {
           res.push(f(a));
         }
         return res;
       };

       log(map((p) => p.name, products));

       log(map((p) => p.price, products));
       ```

    2. 이터러블 프로토콜을 따른 map의 다형성 1

       ```javascript
       log(map((el) => el.nodeName, document.querySelectorAll("*")));

       function* gen() {
         yield 2;
         if (false) yield 3;
         yield 4;
       }

       log(map((a) => a * a, gen()));
       ```

    3. 이터러블 프로토콜을 따른 map의 다형성 2

       ```javascript
       let m = new Map();
       m.set("a", 10);
       m.set("b", 20);
       log(new Map(map(([k, a]) => [k, a * 2], m)));
       ```

    4. filter

       ```javascript
       const filter = (f, iter) => {
         let res = [];
         for (const a of iter) {
           if (f(a)) res.push(a);
         }
         return res;
       };

       log(...filter((p) => p.price < 20000, products));

       log(...filter((p) => p.price >= 20000, products));

       log(filter((n) => n % 2, [1, 2, 3, 4]));
       ```

    5. reduce

       ```javascript
       const reduce = (f, acc, iter) => {
         if (!iter) {
           iter = acc[Symbol.iterator](); // 이터레이터를 반환
           acc = iter.next().value; // 이터레이터 첫번째 값 반환
         }
         for (const a of iter) {
           acc = f(acc, a);
         }
         return acc;
       };

       const add = (a, b) => a + b;

       log(reduce(add, [1, 2, 3, 4, 5]));
       ```

    6. reduce 2

       ```javascript
       log(
         reduce(
           (total_price, product) => total_price + product.price,
           0,
           products
         )
       );
       ```

    7. map+filter+reduce 중첩 사용과 함수형 사고

       ```javascript
       const products = [
         { name: "반팔티", price: 15000 },
         { name: "긴팔티", price: 20000 },
         { name: "핸드폰케이스", price: 15000 },
         { name: "후드티", price: 30000 },
         { name: "바지", price: 25000 },
       ];

       const add = (a, b) => a + b;

       log(
         reduce(
           add,
           map(
             (p) => p.price,
             filter((p) => p.price < 20000, products)
           )
         )
       );

       log(
         reduce(
           add,
           filter(
             (n) => n >= 20000,
             map((p) => p.price, products)
           )
         )
       );
       ```

5.  코드를 값으로 다루어 표현력 높이기

    1. go

       ```javascript
       // 값을 리턴하는 함수
       // 위에서부터 아래로 읽을 수 있음
       const go = (...args) => reduce((a, f) => f(a), args);

       go(
         add(0, 1),
         (a) => a + 10,
         (a) => a + 100,
         log
       );
       ```

    2. pipe

       ```javascript
       // 함수를 리턴하는 함수
       const pipe =
         (f, ...fs) =>
         (...as) =>
           go(f(...as), ...fs);

       const f = pipe(
         (a, b) => a + b,
         (a) => a + 10,
         (a) => a + 100
       );

       log(f(0, 1));
       ```

    3. go를 사용하여 읽기 좋은 코드로 만들기

       ```javascript
       log(
         reduce(
           add,
           map(
             (p) => p.price,
             filter((p) => p.price < 20000, products)
           )
         )
       );

       go(
         products,
         (products) => filter((p) => p.price < 20000, products),
         (products) => map((p) => p.price, products),
         (prices) => reduce(add, prices),
         log
       );
       ```

    4. go+curry를 사용하여 더 읽기 좋은 코드로 만들기

       ```javascript
       // 함수를 받아서 함수를 리턴
       // 인자를 받아서 원하는 개수만큼 들어왔을 때, 받아두었던 함수를 원하는 시점에 평가
       const curry =
         (f) =>
         (a, ..._) =>
           _.length ? f(a, ..._) : (..._) => f(a, ..._);

       const mult = curry((a, b) => a * b);
       log(mult(3)(2));

       const mult3 = mult(3);
       log(mult3(10));
       log(mult3(5));
       log(mult3(3));

       go(
         products,
         filter((p) => p.price < 20000),
         map((p) => p.price),
         reduce(add),
         log
       );
       ```

    5. 함수 조합으로 함수 만들기

       ```javascript
       const pipe =
         (f, ...fs) =>
         (...as) =>
           go(f(...as), ...fs);

       const total_price = pipe(
         map((p) => p.price),
         reduce(add)
       );

       const base_total_price = (predi) => pipe(filter(predi), total_price);

       go(
         products,
         base_total_price((p) => p.price < 20000),
         log
       );

       go(
         products,
         base_total_price((p) => p.price >= 20000),
         log
       );
       ```

6.  장바구니 예제

    1.  총 수량, 총 가격

        ```javascript
        const products = [
          { name: "반팔티", price: 15000, quantity: 1, is_selected: true },
          { name: "긴팔티", price: 20000, quantity: 2, is_selected: false },
          {
            name: "핸드폰케이스",
            price: 15000,
            quantity: 3,
            is_selected: true,
          },
          { name: "후드티", price: 30000, quantity: 4, is_selected: false },
          { name: "바지", price: 25000, quantity: 5, is_selected: false },
        ];

        const add = (a, b) => a + b;

        const sum = curry((f, iter) => go(iter, map(f), reduce(add)));

        const total_quantity = sum((p) => p.quantity);

        const total_price = sum((p) => p.price * p.quantity);

        log(total_quantity(products));

        // go, pipe 변환 예시
        const total_quantity = (products) =>
          go(
            products,
            map((p) => p.quantity),
            reduce((a, b) => a + b)
          );

        //  (products) => go(products, ... )   =>   pipe( ... )
        const total_quantity = pipe(
          map((p) => p.quantity),
          reduce((a, b) => a + b)
        );
        //

        log(total_quantity(products));
        ```

    2.  HTML로 출력하기

              ```javascript
              document.querySelector("#cart").innerHTML = `
                 <table>
                    <tr>
                    <th></th>
                    <th>상품 이름</th>
                    <th>가격</th>
                    <th>수량</th>
                    <th>총 가격</th>
                    </tr>
                    ${go(
                      products,
                      sum(
                        (p) => `

           <tr>
              <td><input type="checkbox" ${p.is_selected ? "checked" : ""}></td>
              <td>${p.name}</td>
              <td>${p.price}</td>
              <td><input type="number" value="${p.quantity}"></td>
              <td>${p.price * p.quantity}</td>
           </tr>
        `
                      )
                    )}
                    <tr>
                    <td colspan="3">합계</td>
                    <td>${total_quantity(filter((p) => p.is_selected, products))}</td>
                    <td>${total_price(filter((p) => p.is_selected, products))}</td>
                    </tr>
                 </table>
              `;
              ```

7.  지연성 1

    1. range와 느긋한 L.range

       ```javascript
       // range :  숫자를 인자로 받고, 숫자의 크기만 한 배열을 리턴
       var add = (a, b) => a + b;

       const range = (l) => {
         let i = -1;
         let res = [];
         while (++i < l) {
           res.push(i);
         }
         return res;
       };

       var list = range(4);
       log(list); // [0, 1, 2, 3] 배열 출력 => range(4) 실행 즉시 배열로 평가
       log(reduce(add, list));

       // 느긋한 L.range : 제너레이션
       const L = {};
       L.range = function* (l) {
         let i = -1;
         while (++i < l) {
           yield i;
         }
       };

       var list = L.range(4);
       log(list); // l.range {<suspended>} 이터레이터 출력 =>  지연 평가
       log(reduce(add, list));
       ```

    2. range와 느긋한 L.range 테스트

       ```javascript
       function test(name, time, f) {
         console.time(name);
         while (time--) f();
         console.timeEnd(name);
       }

       test("range", 10, () => reduce(add, range(1000000)));
       test("L.range", 10, () => reduce(add, L.range(1000000)));
       ```

    3. take

       ```javascript
       const take = curry((l, iter) => {
         let res = [];
         for (const a of iter) {
           res.push(a);
           if (res.length == l) return res;
         }
         return res;
       });

       console.time("");
       go(range(10000), take(5), reduce(add), log);
       console.timeEnd("");

       console.time("");
       go(L.range(10000), take(5), reduce(add), log);
       console.timeEnd("");
       ```

    4. 제너레이터/이터레이터 프로토콜로 구현하는 지연 평가

       - `이터러블` 중심 프로그래밍에서의 `지연 평가 (Lazy Evaluation)`

         - 제때 계산법

         - 느긋한 계산법

         - 제너레이터/이터레이터 프로토콜을 기반으로 구현

    5. L.map

       ```javascript
       // 지연성 map => 제너레이터 이터레이터 프로토콜 기반으로 구현 / 이터레이터를 반환하는 제너레이터
       L.map = function* (f, iter) {
         for (const a of iter) yield f(a);
       };

       var it = L.map((a) => a + 10, [1, 2, 3]);

       log(it.next());
       log(it.next());
       log(it.next());
       //
       log([...it]);
       ```

    6. L. filter

       ```javascript
       // 지연성 filter => 제너레이터 이터레이터 프로토콜 기반으로 구현 / 이터레이터를 반환하는 제너레이터
       L.filter = function* (f, iter) {
         for (const a of iter) if (f(a)) yield a;
       };

       var it = L.filter((a) => a % 2, [1, 2, 3, 4]);

       log(it.next());
       log(it.next());
       log(it.next());
       ```

    7. range, map, filter, take, reduce 중첩 사용

       - 엄격한 평가

       - 평가 흐름이 왼쪽에서 오른쪽으로 흐름(즉시 평가)

       - 각각의 계산이 모두 종료되어야 다음 단계를 수행

       ```javascript
       const range = (l) => {
         let i = -1;
         let res = [];
         while (++i < l) {
           res.push(i);
         }
         return res;
       };
       //
       const map = curry((f, iter) => {
         let res = [];
         iter = iter[Symbol.iterator]();
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           res.push(f(a));
         }
         return res;
       });
       //
       const filter = curry((f, iter) => {
         let res = [];
         iter = iter[Symbol.iterator]();
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           if (f(a)) res.push(a);
         }
         return res;
       });
       //
       const take = curry((l, iter) => {
         let res = [];
         iter = iter[Symbol.iterator]();
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           res.push(a);
           if (res.length == l) return res;
         }
         return res;
       });
       //
       const reduce = curry((f, acc, iter) => {
         if (!iter) {
           iter = acc[Symbol.iterator]();
           acc = iter.next().value;
         } else {
           iter = iter[Symbol.iterator]();
         }
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           acc = f(acc, a);
         }
         return acc;
       });
       //
       console.time("");
       go(
         range(100000),
         map((n) => n + 10),
         filter((n) => n % 2),
         take(10),
         log
       );
       console.timeEnd("");
       ```

    8. L.range, L.map, L.filter, take 의 평가 순서

       - 지연 평가(Lazy Evaluation) : 제너레이터/이터레이터 프로토콜을 기반으로 구현

       - 평가 흐름이 위에서 아래로 흐름

       ```javascript
       L.range = function* (l) {
         let i = -1;
         while (++i < l) {
           yield i;
         }
       };
       //
       L.map = curry(function* (f, iter) {
         iter = iter[Symbol.iterator]();
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           yield f(a);
         }
       });
       //
       L.filter = curry(function* (f, iter) {
         iter = iter[Symbol.iterator]();
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           if (f(a)) {
             yield a;
           }
         }
       });
       //
       const reduce = curry((f, acc, iter) => {
         if (!iter) {
           iter = acc[Symbol.iterator]();
           acc = iter.next().value;
         } else {
           iter = iter[Symbol.iterator]();
         }
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           acc = f(acc, a);
         }
         return acc;
       });
       //
       const take = curry((l, iter) => {
         let res = [];
         iter = iter[Symbol.iterator]();
         let cur;
         while (!(cur = iter.next()).done) {
           const a = cur.value;
           res.push(a);
           if (res.length == l) return res;
         }
         return res;
       });
       //
       console.time("L");
       go(
         L.range(100000),
         L.map((n) => n + 10),
         L.filter((n) => n % 2),
         take(10),
         log
       );
       console.timeEnd("L");
       ```

    9. 엄격한 계산과 느긋한 계산의 효율성 비교

       ```javascript
       console.time("L");
       go(
         L.range(Infinity),
         L.map((n) => n + 10),
         L.filter((n) => n % 2),
         take(10),
         log
       );
       console.timeEnd("L");
       ```

    10. map, filter 계열 함수들이 가지는 결합 법칙

        - 사용하는 데이터가 무엇이든지

        - 사용하는 보조 함수가 순수 함수라면 무엇이든지

        - 아래와 같이 결합한다면 둘 다 결과가 같다.

        ```javascript
        [[mapping, mapping], [filtering, filtering], [mapping, mapping]] = [
          [mapping, filtering, mapping],
          [mapping, filtering, mapping],
        ];
        ```

    11. ES6의 기본 규악을 통해 구현하는 지연 평가의 장점

        - ES6를 통해 가능

        - 조합성이 높음

8.  지연성 2

    1. 결과를 만드는 함수 reduce, take

       - 최종적으로 함수의 결과를 만드는 함수

       - 연산의 시작점을 알리는 함수

    2. queryStr 함수 만들기

       ```javascript
       const queryStr = pipe(
         Object.entries,
         map(([k, v]) => `${k}=${v}`),
         reduce((a, b) => `${a}&${b}`)
       );

       log(queryStr({ limit: 10, offset: 10, type: "notice" }));
       ```

    3. Array.prototype.join 보다 다형성이 높은 join 함수

       ```javascript
       L.entries = function* (obj) {
         for (const k in obj) yield [k, obj[k]];
       };

       const join = curry((sep = ",", iter) =>
         reduce((a, b) => `${a}${sep}${b}`, iter)
       );

       const queryStr = pipe(
         L.entries,
         L.map(([k, v]) => `${k}=${v}`),
         join("&")
       );

       log(queryStr({ limit: 10, offset: 10, type: "notice" }));
       ```

    4. take, find

       ```javascript

       ```

    5. L.map, L.filter로 map과 filter 만들기

       ```javascript

       ```

    6. L.flatten, flatten

       ```javascript

       ```

    7. L.flatMap, flatMap

       ```javascript

       ```

    8. 2차원 배열 다루기

       ```javascript

       ```

    9. 지연성 / 이터러블 중심 프로그래밍 실무적인 코드

       ```javascript

       ```

9.  비동기/동시성 프로그래밍 1

    1. callback과 Promise

       ```javascript

       ```

    2. 비동기를 값으로 만드는 Promise

       ```javascript

       ```

    3. 값으로서의 Promise 활용

       ```javascript

       ```

    4. 합성 관점에서의 Promise와 모나드

       ```javascript

       ```

    5. Kleisli Composition 관점에서의 Promise

       ```javascript

       ```

    6. go, pipe, reduce에서 비동기 제어

       ```javascript

       ```

    7. promise.then의 중요한 규칙

       ```javascript

       ```

10. 비동기/동시성 프로그래밍 2

    1. 지연 평가 + Promise - L.map, map, take

       ```javascript

       ```

    2. Kleisli Composition - L.filter, filter, nop, take

       ```javascript

       ```

    3. reduce에서 nop 지원

       ```javascript

       ```

    4. 지연 평가 + Promise의 효율성

       ```javascript

       ```

    5. 지연된 함수열을 병렬적으로 평가하기 - C.reduce, C.take 1

       ```javascript

       ```

    6. 지연된 함수열을 병렬적으로 평가하기 - C.reduce, C.take 2

       ```javascript

       ```

    7. 즉시 병렬적으로 평가하기 - C.map, C.filter

       ```javascript

       ```

    8. 즉시, 지연, Promise, 병렬적 조합하기

       ```javascript

       ```

    9. 코드 간단히 정리

       ```javascript

       ```

    10. Node.js에서 SQL 병렬 평가로 얻은 효율

        ```javascript

        ```

11. 비동기/동시성 프로그래밍 3

    1. async/await

       ```javascript

       ```

    2. Q&A) Array.prototype.map이 있는데 왜 FxJS의 map 함수가 필요한지?

       ```javascript

       ```

    3. Q&A) 이제 비동기는 async/await로 제어할 수 있는데 왜 파이프라인이 필요한지?

       ```javascript

       ```

    4. Q&A) async/await와 파이프라인을 같이 사용하기도 하는지?

       ```javascript

       ```

    5. Q&A) 동기 상황에서 에러 핸들링은 어떻게 해야하는지?

       ```javascript

       ```

    6. Q&A) 비동기 상황에서 에러 핸들링은 어떻게 해야하는지?

       ```javascript

       ```

    7. Q&A) 동기/비동기 에러 핸들링에서의 파이프라인의 이점은?

       ```javascript

       ```
