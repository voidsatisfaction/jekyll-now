---
layout: post
title: Nodejs 공부기 1
---

## 선생님
[생활코딩 Nodejs](https://opentutorials.org/course/2136/)

## 배운 내용들

### Basics
  - Nodejs?
  
  - Client and Server
  
  - NPM
    - `npm init` => package.json 파일 작성
    - `npm install underscore (--save)` => node_modules폴더에 underscore추가, --save는 package.json에 dependencies추가
    - Example : Uglify, Express, Underscore
  
  - Callback Function
    - 코드상에서 직접 호출되는 것이 아니고, 누군가가 호출하는 함수. **(정의가 완벽하지 못한 것 같다.)**
    - 주로 lambda를 이용해서 구현.
    - 함수도 데이터이기 때문에 함수가 함수를 인자로 받을 수 있는 것을 이용.
    
  - Sync, Async
    - 빨래/설거지/청소, 100만명에게 이메일 보내기
    
  - Relationship
    - JS and Nodejs
    - Module and NPM
    - Structure of App.js
  
### Express API

  - Static, Dynamic
    - Static : html, image, ... => 실시간 동기화, 편집이 쉬움 => `./public/htmlfile or img`
    - Dynamic : js code in controller(call back) => 편집은 어려우나 동적으로 제어가능 => app.js
    
  - Template Engine(Jade)
    - `app.set('view engine', 'jade')`
    - `app.set('views', './views')`
    - Jade만의 형식이 따로 있음
    
  - Query String
    - URL을 이용한 정보의 전달
    - `http://a.com/topic?id=1`
    - `http://a.com/topic/id/1` (semantic)
    
## Real code

### App.js
```javascript
const express = require('express')
const app = express()

app.locals.pretty = true
app.set('view engine', 'jade')
app.set('views', './views')
app.use(express.static('public')) // static file service

app.get('/topic/:id', (req, res) => {
  const topics = [
    'javascript is ...',
    'Nodejs is ...',
    'Express is ...'
  ]
  const output = `
    <a href="/topic/0">javascript</a><br>
    <a href="/topic/1">nodejs</a><br>
    <a href="/topic/2">express</a><br>
    ${topics[req.params.id]}
  `
  res.send(output)
})
app.get('/topic/:id/:mode', (req, res) => {
  res.send(`${req.params.id},${req.params.mode}`)
})

app.get('/template', (req, res) => {
  res.render('temp', {time: Date(), _title: 'jade'})
})
app.get('/', (req, res) => {
  res.send('Hello this is my main page')
})
app.get('/dynamic', (req, res) => {
  var lis = ''
  for(var i=0; i<5; i++){
    lis += '<li>coding</li>'
  }
  var time = Date()
  res.send(
    `
    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="utf-8">
        <title></title>
      </head>
      <body>
        <p>
          Hello Dynamic!!!!
          <ul>
            ${lis}
          </ul>
          ${time}
        </p>
      </body>
    </html>
    `
  )
})
app.get('/route', (req, res) => {
  res.send('<img src="/five_minutes.png" />')
})
app.get('/login', (req, res) => {
  res.send(
    `<h1>Practice</h1>
    <p>What are you doing now?</p>`
  )
})
app.listen(3000, () => {
  console.log('Connected at 3000 port!')
})

```

### Template.jade
```
html
  head
    title= _title
  body
    h1 Hello world!
    ul
      -for(var i=0; i<5; i++)
        li coding
    div= time

```

## 궁금한 것들

- 한 컴퓨터에 여러 서버가 존재할 경우, 만일 서버의 포트가 겹치면 어떻게 되는가?
- 왜 콜백이 존재해야 하는가? (애초에 콜백이란 무엇인가?) maybe 중복의 제거?(기능적 중복)
- Ruby는 멀티스레드를 지원하는가?
- Nodejs는 싱글스레드인데 어떻게 비동기가 가능한 것인가?(비동기의 원리)
- `const http = require('http')` 의 정확한 의미? (http모듈 객체를 http라는 상수에 넣는다? 그럼 모듈 객체는 뭐지? 인스턴스인가?)
- Nodejs의 기능을 구현하기 위한 '그릇' 혹은 '배경' = JavaScript 라고 이해해도 되는가?
- RESTful?
- Semantic URL?
- Node의 View는 어떻게 꾸며주는가? Angular? Jade?
