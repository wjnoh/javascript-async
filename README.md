# Javascript-Async

이 문서는 자바스크립트의 비동기 처리 방법에 관해 정리해둔 문서입니다. 편의상 평어체로 작성했습니다. 비동기 AJAX에 관한 내용은 AJAX MD를 참고하시기 바랍니다.

<br/>

## Callback

가장 기본적인 방법은 콜백 함수를 이용하는 것이다. 1초에 걸쳐 1, 2, 3, 4를 프린트하는 코드를 작성해보자.

```javascript
function printLater(number, fn){
    setTimeout(
        () => {
            console.log(number);
            if(fn) fn();
    	},
        1000
    )
}

printLater(1, () => {
    printLater(2, () => {
        printLater(3, () => {
            printLater(4);
        })
    })
})
```

가장 단순한 방법이다. 하지만 이런 방식이라면 작업이 많아지면 많아질수록 코드 구조는 더 깊어질 것이고, 점점 더 코드 읽기가 어려워질 것이다. 이를 콜백 지옥이라 한다.

<br/>

## Promise

콜백 지옥 문제를 해결해주는 것이 바로 Promise이다. 똑같은 코드를 이번에는 Promise로 작성해보자.

```javascript
function printLater(number){
    return new Promise(
        resolve => {
            setTimeout(
                () => {
                    console.log(number);
                    resolve();
                },
                1000
            )
        }
    )
}

printLater(1)
.then(() => printLater(2))
.then(() => printLater(3))
.then(() => printLater(4))
```

new Promise로 promise 함수를 생성하면서, 그 파라미터로는 resolve를 받는다. resolve는 promise가 끝났음을 알릴 때 사용한다. 그리고 promise로 된 함수를 불러올 때는 .then을 사용하고 콜백 함수로 다음 promise 함수를 불러오면 된다. 여러번 적더라도 깊이는 같으므로 콜백 지옥에 빠질 걱정이 없다.

promise 함수의 파라미터로는 resolve도 있지만 reject도 있다. promise에서 결과 값을 반환할 때는 resolve(결과 값)을 작성하고, 오류를 발생시킬 때는 reject(오류)를 작성한다. resolve로 반환된 결과 값은 .then 내부 함수의 파라미터로 불러와 다음 함수를 불러오는데 사용할 수 있고,  .catch로는 미리 설정해둔 오류를 반환할 수 있다. 아래 예제를 보자. 한 번 함수를 실행시킬 때마다 1을 더해 반환하고, 이 값이 4  이상이면 오류를 반환하는 예제이다.

```javascript
function printLater(number) {
    return new Promise(
        (resolve, reject) => {
            if(number > 4){
                return reject("4보다 큽니다.");
            }
            setTimeout(
                () => {
                    console.log(number);
                    resolve(number + 1);
                },
                1000
            )
        }
    )
}

printLater(1)	// 1 출력하면서 2 반환
.then(num => printLater(num))	// 2 출력하면서 3 반환
.then(num => printLater(num))	// 3 출력하면서 4 반환
.then(num => printLater(num))	// 4 출력하면서 5 반환
.then(num => printLater(num))	// 받은 5가 4보다 크므로 reject 반환, catch로 이동
.catch(e => console.log(e))		// 4보다 크다는 메시지를 출력
```
<br/>

## Async / Await

Promise로 충분히 편해지긴 했지만, 그래도 여전히 콜백 방식으로 함수를 불러오기 때문에 코드가 비동기스럽고 알아보기 어렵다. 그래서 생겨난 것이 비동기지만 동기스러운 코드를 만들어주는 Async와 Await이다.  아래 코드를 한 번 보자.

```javascript
async function asyncTest() {
    await firstFunction(){...};
    await secondFunction(){...};
	await thirdFunction(){...};
}

asyncTest();
```

대략 이런 방식으로 사용된다. 함수 앞에 async를 붙여주고, 호출할 함수 앞에 await을 붙여주면 된다. 세 함수가 모두 비동기적으로 실행되는 함수라 할지라도, 앞에 await만 붙여주면 순서대로 함수가 실행된다. 단 await으로 실행되는 함수들은 모두 Promise를 리턴해야 한다. async와 await도 결국 Promise 기반이기 때문이다. 아래 코드는 아까와 비슷하게 1, 2, 3을 순서대로 프린트하면서 2보다 크면 오류를 반환하는 예제이다.

```javascript
async function asyncTest() {
    try {
        await printLater(1);	// 1
		await printLater(2);	// 2
		await printLater(3);	// 2보다 큽니다!
    }
    catch(error) {
        console.log(error);
	}
}

function printLater(number) {
	return new Promise((resolve, reject) => {
        if(number > 2){
            return reject("2보다 큽니다!");
        }
		setTimeout(
			() => {
            	console.log(number);
				resolve();
			},
			1000
		)
    })
}

asyncTest();
```

이렇게 된다. 동기적인 방식으로 코드를 작성할 수 있기 때문에 에러를 반환할 때도 try와 catch를 쓸 수 있다. reject로 반환된 값을 catch에서 error로 받아오기 때문이다.