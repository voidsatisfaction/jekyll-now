---
layout: page
title: Nodejs 공부기3 ~ Web Application만들기
---

## 선생님

생활코딩 Nodejs

## 배운 내용들

> 우리는 비게 오게 해달라 오지 않게 해달라고 기도한다.

> 하지만 진정한 기도는 비가 와도 오지 않아도 자기자신이 흔들리지 않게 해달라는 해탈을 위한 기도이다.

고생고생해서 CRUD하나 만들었는데 확실히 Nodejs가 편리한 면이 있다.
그러나 앱을 만드는데에 있어서 중요한 것은 
한번에 다 하려고 하지 않고 하나한 차근차근 해나가는 것이다. 
즉, 기획을 완벽히 하고 앱을 제작하자.

여기서 기획이라 함은 어떠한 객체가 어떻게 작용하는지, 어떤 흐름으로 작동하는지를
명확히 한 것을 의미한다.

**백날 디버깅하는 것 보다 한 번 기획을 잘하는 것이 효율적이다.**


## Real code

```javascript

const express = require('express')
const app = express()
const bodyParser = require('body-parser')
const Oriento = require('oriento');
const server = Oriento({
  host: 'localhost',
  port: 2424,
  username: 'root',
  password: '***' // You should save password on config file for proper security
});
var db = server.use('o2')
console.log('Using database: ' + db.name)

app.use(bodyParser.urlencoded({ extended: false }))
app.locals.pretty = true
app.set('views', './views')
app.set('view engine', 'pug')

app.get('/topic/add', (req, res) => {
  res.render('add')
})
app.post('/topic/add', (req, res) => {
  const title = req.body.post_title
  const name = req.body.post_name
  const description = req.body.post_description

  const sql = 'INSERT INTO topic (title, author, description) VALUES(:title, :name, :description)'
  db.query(sql, {params: {title: title, name: name, description: description}}).then((result) => {
    console.log(result)
    const id = encodeURIComponent(result['@rid'])
    res.redirect('/topic/')
  })
})

app.get('/topic/:id/edit', (req, res) => {
  const id = decodeURIComponent(req.params.id)
  const sql = 'SELECT FROM topic where @rid=:rid'
  db.query(sql, {params: {rid: id}}).then((topic) => {
    console.log(topic)
    res.render('edit', {topic: topic[0]}) // Be careful!! topic[0]
  })
})
app.post('/topic/:id/edit', (req, res) => {
  const rid = decodeURIComponent(req.params.id)
  const title = req.body.post_title
  const name = req.body.post_name
  const description = req.body.post_description

  const sql = `UPDATE topic SET title=:title, author=:name,
  description=:description WHERE @rid=:rid` //no comma plz
  db.query(sql, {
    params: {
      rid: rid,
      title: title,
      name: name,
      description: description}
    }).then((topic) => {
    console.log('edit complete')
    res.redirect('/topic/' + encodeURIComponent(req.params.id))
  })
})

app.get('/topic/:id/delete', (req, res) => {
  const rid = decodeURIComponent(req.params.id)
  const sql = 'DELETE FROM topic WHERE @rid=:rid'

  db.query(sql, {params: {rid: rid}}).then((result) => {
    console.log('delete successfully')
    res.redirect('/topic/')
  })
})


app.get(['/topic', '/topic/:id'], (req, res) => {
  const sql = 'SELECT FROM topic'
  db.query(sql).then((results) => {
    if (req.params.id) {
      const id = decodeURIComponent(req.params.id)
      const sql2 = 'SELECT FROM topic WHERE @rid=:rid' // rid should be placed inside of next's params!
      db.query(sql2, {params: {rid: id}}).then((topic) => {
        console.log(topic)
        res.render('topic', {topic: topic[0], topics: results})
      })
    } else {
      res.render('topic', {topic: {title: 'Welcome!', name: 'Master', description: 'Practicing'}, topics: results})
    }
  })
})


app.listen(3000, () => {
  console.log('Server connected at 3000!')
})

```
