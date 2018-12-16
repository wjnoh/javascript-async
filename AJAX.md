# AJAX

AJAX로 데이터를 받아오는 방법에 관한 문서입니다. 테스트 API는 JSON 형식의 데이터를 받아온다는 가정 하에 JSONPlaceholder에서 제공하는 API를 사용했습니다.

## XHR(XML HTTP Request)

-   서버로부터 받은 JSON 데이터는 문자열인데, 이를 객체로 사용하려면 JSON.parse() 메소드로 객체화해야 한다.

-   JSON 형식으로 서버로 보내기 위해서는 해당 객체를 JSON.stringify() 메소드로 JSON 문자열화해야 한다.

GET

```javascript
const xhr = new XMLHttpRequest();
xhr.onload = () => {
    console.log(JSON.parse(xhr.responseText));
};
xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1");
xhr.send();
```

POST

```javascript
const xhr = new XMLHttpRequest();
const data = {
    userId: 1,
    title: "title",
    completed: false
};
xhr.onload = () => {
    console.log(JSON.parse(xhr.responseText));
};
xhr.open("POST", "https://jsonplaceholder.typicode.com/todos");
xhr.setRequestHeader("Content-Type", "application/json");
xhr.send(JSON.stringify(data));
```

## Fetch

-   JSON.parse() 메소드는 단순히 동기적으로 객체를 반환할 뿐이다. 때문에 then() 메소드로 Promise를 반환하는 fetch는 .json() 메소드를 통해 비동기적으로 Promise 객체를 반환해야 한다.

GET

```javascript
fetch("https://jsonplaceholder.typicode.com/todos/1")
    .then(res => res.json())
    .then(json => console.log(json))
    .catch(err => console.log(err));
```

POST

```javascript
const data = {
    userId: 1,
    title: "title",
    completed: false
};
fetch("https://jsonplaceholder.typicode.com/todos", {
    method: "POST",
    header: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
})
    .then(res => res.json())
    .then(json => console.log(json))
    .catch(err => console.log(err));
```

## Axios
