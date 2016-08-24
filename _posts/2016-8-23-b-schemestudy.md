---
layout: post
title: Scheme의 아름다움 1
---

## 선생님

The Little Schemer

## 배운 내용들

- lambda

  - 함수의 return값으로 함수를 건네줄 수 있다.
  
  - 그 함수는 lambda함수를 이용해서 건네줄 수 있다.

- Collecter

  - 함수가 여러 값을 나타낼 수 있도록 도와준다. 값의 Collect
  
  - 아직 완벽하지 이해하지는 못했으나 정말 아름다워 보인다.

## Real code

### 예제1
```scheme
#| Remeber more than two value at a time |#

(define (multiinsertLR new oldL oldR lat)
  (cond ((null? lat) '())
        ((eq? oldL (car lat))
         (cons new 
          (cons oldL 
            (multiinsertLR new oldL oldR (cdr lat)))))
        ((eq? oldR (car lat))
         (cons oldR 
          (cons new 
            (multiinsertLR new oldL oldR (cdr lat)))))
        (else
         (cons (car lat) 
          (multiinsertLR new oldL oldR (cdr lat))))))

(define (multiinsertLR&co new oldL oldR lat col)
  (cond ((null? lat)
         (col '() 0 0))
        ((eq? oldL (car lat))
         (multiinsertLR&co new oldL oldR
                           (cdr lat)
                           (lambda (newlat L R)
                             (col (cons new 
                                (cons oldL newlat)) (+ L 1) R))))
        ((eq? oldR (car lat))
         (multiinsertLR&co new oldL oldR
                           (cdr lat)
                           (lambda (newlat L R)
                             (col (cons oldR 
                                (cons new newlat)) L (+ R 1)))))
        (else
         (multiinsertLR&co new oldL oldR
                           (cdr lat)
                           (lambda (newlat L R)
                             (col (cons (car lat) newlat) L R))))))

(multiinsertLR&co 'salty 'fish 'chips '(chips and fish or fish and chips) (lambda (lat a b) (cons a (cons b lat))))
```

#### 해설
만약 위의 multiinsertLR&co함수가 
(multiinsertLR&co 'hi 'animal 'plant '(hello world) col)
이렇게 실행된다고 하자. (여기서 col은 아직 미정의)

그러면, 조건 분기에서 다음이 실행된다.

(car lat)인 'hello는 oldL, oldR과도 다르므로
else 이후의 내용이 실행된다.
그런데 여기서 바로 (multiinsertLR&co)함수를 인수만 바꿔서 실행한다.
특히 바뀌는 것은 col만 바뀌므로 그쪽에만 집중해서 보기로 하자.

두 번째 multiinsertLR&co의 실행에서
col => (lambda (newlat L R) (col (cons (car lat) newlat) L R))
즉, 이는 (col (newlat L R) (col (cons (car lat) newlat) L R))
과도 동일하다.

풀어서 얘기하자면, 두번째 multiinsertLR&co는 col에 대한부분이
(col (newlat L R) (col (cons (car lat) newlat) L R)) 이렇게 변하는 것이며,
(car lat)이 'hello 였으므로,
(col (newlat L R) (col (cons 'hello newlat) L R))이 col 인자로 넘어가게 된다.

다음 프로세스
(car lat)인 'world는 oldL, oldR과도 다르므로
else 이후의 내용이 실행된다.
그리고 마찬가지로 col만 변화가 되므로 col에만 집중해서 보자.

세번째 multiinsertLR&co의 실행에서
col => (lambda (newlat L R) (col (cons 'world newlat) L R))
여기서 주목해야할 것은 화살표 왼쪽의 col과 오른쪽의 col을 구별하는 것이다.
**왼쪽의 col은 앞으로 col의 자리에 넘겨주게 될 함수의 인자를 의미하는 것이며,**
**오른쪽의 col은 현재 실행되는 함수의 col을 말하는 것이다.**

여기서 우리는 현재 실행되는 함수의 col이 전multiinsertLR&co에서 인자로서 받아온
(col (newlat L R) (col (cons 'hello newlat) L R))이 된다는 것을 알기때문에
우리가 다음 multiinsertLR&co에 전해줄 col은
col(전해줄 col) => (lambda (newlat L R) (col(가장 처음의 col) (cons 'hello (cons 'world newlat)) L R))

이렇게 됨을 알 수있다.

마지막 multiinsertLR&co를 실행하면
(null? lat)이 참이므로
(col '() 0 0)이 실행되는데 col은 위에서 받아온 그대로이므로
(col (cons 'hello (cons 'world '())) L R)이 된다.
즉, 최종 결과는 (col (hello world) L R)이며,
이는 하나의 함수가 리스트를 이용하여 다양한 값을 저장할 수 있다는 것을 의미한다.


### 예제2 (p.s 정말 아름다운 코드다)

```scheme
(define (atom? s)
  (and (not (pair? s)) (not (null? s))))

(define (evens-only* l)
  (cond ((null? l) '())
        ((and (atom? (car l)) (even? (car l)))
         (cons (car l) (evens-only* (cdr l))))
        ((atom? (car l))
         (evens-only* (cdr l)))
        (else
         (cons (evens-only* (car l)) (evens-only* (cdr l))))))

(define (evens-only*&co l col)
  (cond ((null? l)
         (col '() 1 0))
        ((and (atom? (car l)) (even? (car l)))
         (evens-only*&co (cdr l)
                         (lambda (newl mul sum)
                           (col (cons (car l) newl)
                                (* (car l) mul)
                                sum))))
        ((atom? (car l))
         (evens-only*&co (cdr l)
                         (lambda (newl mul sum)
                           (col newl mul
                                (+ (car l) sum)))))
        (else
         (evens-only*&co (car l)
                         (lambda (al ap as)
                           (evens-only*&co (cdr l)
                                           (lambda (dl dp ds)
                                             (col (cons al dl)
                                                  (* ap dp)
                                                  (+ as ds)))))))))

(evens-only*&co '((9 1 2 8) 3 10 ((9 9) 7 6) 2)
             (lambda (l mul sum)
               (cons sum (cons mul l))))
```
