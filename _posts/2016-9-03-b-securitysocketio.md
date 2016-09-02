---
layout: post
title: Nodejs 공부기4 ~ Session & Password & Socket.io
---

## 포인트

- Session
  - Client에 저장하는 것은 sessionid뿐. 실질적인 데이터는 서버에.

- Password
  - md5 + salt(global, respective)
  - sha512 + salt
  - PBKDF2

- Socket.io
  - ~~정의는아직~~
  - 실시간 어플리케이션(채팅, 캐치마인드)

## Real code

### Session

```javascript

const express = require('express')
const session = require('express-session')
const FileStore = require('session-file-store')(session)
const bodyParser = require('body-parser')
const pug = require('pug')
const app = express()

app.set('views', './views')
app.set('view engine', 'pug')

app.use(session({
  store: new FileStore(),
  secret: 'wefjwoefkwlefmwlkm',
  resave: false,
  saveUninitialized: true,
}))
app.use(bodyParser.urlencoded({ extended: false }))

// this scope!
const userDB = {
  findUser: function(id,password){
    if (!this.allUsers[id] || this.allUsers[id]['password'] != password) {
      return false
    } else {
      return true
    }
  },
  allUsers: {
    admin: {
      password: '123'
    },
    nersery: {
      password: '123123'
    },
    lionking: {
      password: '123'
    }
  }
}

// req.session.count
app.get('/login', (req, res) => {
  if (req.session.userInfo) {
    res.redirect('/welcome')
  } else {
    errorMessage = req.session.err
    res.render('login',{errorMessage: errorMessage})
  }
})
app.post('/login', (req, res) => {
  const userId = req.body.userId
  const userPassword = req.body.userPassword

  if (userDB.findUser(userId,userPassword)) {
    delete req.session.err
    req.session.userInfo = {
      id: userId
    }
    res.redirect('/welcome')
  } else {
    req.session.err = 'Check your id or password'
    res.redirect('/login')
  }
})

app.get('/logout', (req, res) => {
  delete req.session.errorMessage
  delete req.session.userInfo
  res.redirect('/login')
})

app.get('/register', (req, res) => {
  if (req.session.userInfo) {
    res.redirect('/welcome')
  } else if(req.session.errorMessage){
    res.render('register',{errorMessage: req.session.errorMessage})
  } else {
    res.render('register')
  }
})
app.post('/register', (req, res) => {
  const userId = req.body.userId
  const userPassword = req.body.userPassword
  const userPasswordConfirm = req.body.userPasswordConfirm

  if (userDB.allUsers[userId]) {
    req.session.errorMessage = 'the id is already exist'
  } else if(userPassword.length < 3){
    req.session.errorMessage = 'your password should be more than 3 characters'
  } else if(userPassword !== userPasswordConfirm){
    req.session.errorMessage = 'Password is not correct with Password confirm'
  } else {
    delete req.session.errorMessage
    userDB.allUsers[userId] = { password: userPassword}
    req.session.userInfo = {id: userId}
    req.session.save(() => {
      res.redirect('/welcome')
    })
  }

  res.redirect('/register')
})

app.get('/welcome', (req, res) => {
  const userId = req.session.userInfo.id

  res.render('welcome', {userId: userId})
})

app.listen(3000, () => {
  console.log('Connected at 3000 port!')
})

```

### Socket.io

#### js part
```javascript

const app = require('express')()
const http = require('http').Server(app)
const io = require('socket.io')(http)

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/views/index.html')
})

io.on('connection', (socket) => {
  socket.on('chat message', (msg) => {
    io.emit('chat message', msg)
  })
})

http.listen(3000, () => {
  console.log('Connected at 3000 port!')
})


```

#### html part

```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Socket.IO chat</title>
    <style>
      * { margin: 0; padding: 0; box-sizing: border-box; }
      body { font: 13px Helvetica, Arial; }
      form { background: #000; padding: 3px; position: fixed; bottom: 0; width: 100%; }
      form input { border: 0; padding: 10px; width: 90%; margin-right: .5%; }
      form button { width: 9%; background: rgb(130, 224, 255); border: none; padding: 10px; }
      #messages { list-style-type: none; margin: 0; padding: 0; }
      #messages li { padding: 5px 10px; }
      #messages li:nth-child(odd) { background: #eee; }
    </style>
  </head>
  <body>
    <ul id="messages"></ul>
    <form>
      <input id="m" type="text" autocomplete="off" /><button>Send</button>
    </form>
    <script src="/socket.io/socket.io.js"></script>
    <script src="http://code.jquery.com/jquery-1.11.1.js"></script>
    <script>
      const socket = io()
      $('form').submit( () => {
        socket.emit('chat message', $('#m').val())
        $('#m').val('')
        return false
      })
      socket.on('chat message', (msg) => {
        $('#messages').append($('<li>').text(msg))
      })
    </script>
  </body>
</html>


```

## 참조

- [생활코딩 Password 보안](https://opentutorials.org/course/2136/12132)
- [Socket.io Getting Started](http://socket.io/get-started/chat/)
