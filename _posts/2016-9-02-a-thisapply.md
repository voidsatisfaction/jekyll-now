---
layout: post
title: JavaScript공부기1 ~ this, bind, apply
---

## 포인트

- This의 사용
  - 객체의 소속인 메소드의 this는 그 객체를 가르킨다. 
  - 아무것도 없을 경우는 window객체

- Bind
  - **bind는 어떤 객체에 종속되어 있는 함수의 this의 문맥을 명시적으로 변경시키기 위해서 사용한다.**

- Apply
  - **apply는 일시적으로 어떠한 함수를 다른 객체에서 사용할 수 있도록 하기 위해서 사용한다.**

## Real code

### This 예제

```javascript
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
```

### Bind예제

[React Native 공부기1 ~ bind 편 참조](https://voidsatisfaction.github.io/b-rn/)

### Apply예제

```javascript
var o = {}
var p = {}
function func(){
    switch(this){
        case o:
            document.write('o<br />');
            break;
        case p:
            document.write('p<br />');
            break;
        case window:
            document.write('window<br />');
            break;          
    }
}
func();
func.apply(o);
func.apply(p);
```

## 참조
- [생활코딩 this](https://opentutorials.org/course/743/6571)

