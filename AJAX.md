# AJAX

AJAX로 리소스를 받아오는 방법에 관한 문서입니다. 테스트 API는 JSON 형식의 데이터를 받아온다는 가정 하에 JSONPlaceholder에서 제공한 API를 사용했습니다.

## XHR(XML HTTP Request)

GET

```javascript
const xhr = new XMLHttpRequest();
xhr.onload = () => {
    if (xhr.status === 200 || xhr.status === 201) {
        console.log(JSON.parse(xhr.responseText));
    } else {
        console.error(xhr.responseText);
    }
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
    if (xhr.status === 200 || xhr.status === 201) {
        console.log(JSON.parse(xhr.responseText));
    } else {
        console.error(xhr.responseText);
    }
};
xhr.open("POST", "https://jsonplaceholder.typicode.com/todos");
xhr.setRequestHeader("Content-Type", "application/json");
xhr.send(JSON.stringify(data));
```

## Fetch

GET

```javascript
fetch("https://jsonplaceholder.typicode.com/todos/1")
    .then(res => {
        if (res.status === 200 || res.status === 201) {
            res.json().then(json => {
                console.log(json);
            });
        } else {
            console.error(res.status);
        }
    })
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
    data
})
    .then(res => {
        if (res.status === 200 || res.status === 201) {
            res.json().then(json => {
                console.log(json);
            });
        } else {
            console.error(res.status);
        }
    })
    .catch(err => console.log(err));
```

## Axios
