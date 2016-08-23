---
layout: post
title: Scheme의 아름다움 1
---

## 선생님

The Little Schemer

## 배운 내용들

## Real code

**Collecter : 함수가 여러 값을 나타낼 수 있도록 도와준다. 값의 Collect**

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
