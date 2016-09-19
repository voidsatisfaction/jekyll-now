---
layout: post
title: Xcode버전업과 RN개발환경 오류들
---

이 글을 쓰게 되는 계기는 다름이 아니라 몇가지 에러로 인하여 나와 사부가 정말 말 그대로 **하루종일** 고생했다는 점이다.

그래서 앞으로 이러한 에러가 나올 경우에 잘 대처할 수 있도록 머리에 담아두고자 이곳에 글을 쓴다.

## 발단 : Git사용의 미숙함 + Xcode업데이트

Remote에서 git pull을 한 이후로 이상하게 simulator가 실행되지 않는 버그가 발생했다. 그리고, 나는 그것이 Xcode가 업데이트되지 않았기 때문에 발생한 문제라고 생각했다.

그래서 나는, 끔찍한 미래가 기다리는 것도 모른채

RN노드 모듈을 전부 삭제하고 다시 깔고, cache도 전부 지웠다 다시 깔았으며, xcode마저 업데이트 했다.

## 해결

해결은 다음과 같은 순서로 행해졌다.

### 1. xcode업데이트에 의한 websocket오류 => RCTWebSocket의 Custom Compiler Flag의 value 제거

![error1](/images/20160919_websocket_error.png)

### 2. terminal상에서 무엇이 에러인지 모를때 => xcode에서 build후 error를 추출한다.

### 3. xcode를 build할때 => 흰색으로 열어라

### 4. linker library지원 (7.0버전 설정을 로컬에 맞는 9.1로 변환)

![error2](/images/20160919_linker_error.png)

### 5. suitable library 오류 => 시뮬레이터를 ios10 에서 ios9로 실행을 바꿈(없으면 새로 다운받아야함)

```

dyld: Library not loaded: @rpath/libFirestack.a
  Referenced from: /Users/tech-camp-064/Library/Developer/CoreSimulator/Devices/3692613C-5EBB-4BAD-9496-C336B2629BC8/data/Containers/Bundle/Application/D73B751F-39B9-4230-818A-C7C5BF5B7C66/SchoolShare.app/SchoolShare
  Reason: no suitable image found.  

  Did find:
    /Users/tech-camp-064/Library/Developer/Xcode/DerivedData/SchoolShare-dlacbtwbdazihdgkeyjricajfoer/Build/Products/Debug-iphonesimulator/libFirestack.a: required code signature missing for '/Users/tech-camp-064/Library/Developer/Xcode/DerivedData/SchoolShare-dlacbtwbdazihdgkeyjricajfoer/Build/Products/Debug-iphonesimulator/libFirestack.a'

(lldb)

```

![error3](/images/20160919_simulator_error.png)

### 6. 무슨 이유인지 모르겠지만 npm install을 해도 존재하지 않다고 하면 그것은 수동으로 npm i 해줘야 한다

![error4](/images/20160919_module_error2.png)

이와같은 방식으로 해결했다.

## 교훈

- xcode업데이트는 팀원과 협의하에 행해야 한다.

- terminal상에서 error의 내용을 잘 확인할 수 없을때에는 xcode에서 build하면 단서를 얻을 수 있다.

- xcode를 틈틈이 공부할 수 있으면 참 좋을것 같다.

그래도 오늘도 굴복하지 말고 Happy Coding!
