---
layout: post
title: Nodejs 공부기 2
---

## 선생님

생활코딩 Nodejs

## 배운 내용들

### File Based Web App
- Get / Post
  - Get : Using URL
  
  - Post : No URL
    - bodyParser

- Web App Making
  - CR(UD)
  
  - Removing Overlapped Expression
  
- File Uploading
  - Multer
  
### ETC
- Nodejs Auto-restart(supervisor module)

## Real code

### App.js(CR,File upload)

```javascript

const express = require('express')
const bodyParser = require('body-parser')
const multer = require('multer')
const _storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/')
  },
  filename: (req, file, cb) => {
    cb(null, file.originalname + '-' + Date.now())
  }
})
const upload = multer({ storage: _storage })
const fs = require('fs')
const app = express()

app.use('/user', express.static('uploads')) // uploads에 있는 파일들을 /user라는 url을통해서 서비스 하겠다. (유저가 갖고 갈 수 있게 함.)
app.use(bodyParser.urlencoded({ extended: false }))
app.set('views', './views')
app.set('view engine', 'pug')

app.get('/', (req, res) => {
  res.send('hi fool!' )
})

app.get('/upload', (req, res) => {
  res.render('upload')
})

// Middle ware first(processing)
app.post('/upload', upload.single('userfile'), (req, res) => {
  console.log(req.file)
  res.send('Uploaded : ' + req.file.filename)
})

app.get('/topic/new', (req, res) => {
  fs.readdir('data', (err, files) => {
    if (err) {
      console.log(err)
      res.status(500).send('Internal Server Error')
    }
    res.render('new', { topics: files})
  })
})

app.get(['/topic', '/topic/:id'], (req, res) => {
  fs.readdir('data', (err, files) => {
    if (err) {
      console.log(err)
      res.status(500).send('Internal Server Error')
    }
    const id = req.params.id
    if (id) {
      fs.readFile(`data/${id}`, 'utf8', (err, data) => {
        if (err) {
          console.log(err)
          res.status(500).send('Internal Server Error')
        }
        res.render('view', { title: id, topics: files, description: data})
      })
    } else {
      res.render('view', { topics: files, title: 'Welcome', description: 'Hello, JavaScript for server.' })
    }
  })
})

app.post('/topic', (req, res) => {
  const title = req.body.title
  const description = req.body.description

  fs.writeFile(`data/${title}`,description, (err) => {
    if (err) {
      res.status(500).send('Internal Server Error!')
    }
    res.redirect('/topic')
  })
})

app.listen(3000, () => {
  console.log('Connected at 3000port!')
})

```
