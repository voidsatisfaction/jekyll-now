---
layout: post
title: Scheme의 아름다움 1
---

## 선생님

The Little Schemer

## 배운 내용들

## Real code

```Scheme
#| Remeber more than two value at a time |#

(define (multiinsertLR new oldL oldR lat)
  (cond ((null? lat) '())
        ((eq? oldL (car lat))
         (cons new (cons oldL (multiinsertLR new oldL oldR (cdr lat)))))
        ((eq? oldR (car lat))
         (cons oldR (cons new (multiinsertLR new oldL oldR (cdr lat)))))
        (else
         (cons (car lat) (multiinsertLR new oldL oldR (cdr lat))))))

(define (multiinsertLR&co new oldL oldR lat col)
  (cond ((null? lat)
         (col '() 0 0))
        ((eq? oldL (car lat))
         (multiinsertLR&co new oldL oldR
                           (cdr lat)
                           (lambda (newlat L R)
                             (col (cons new (cons oldL newlat)) (+ L 1) R))))
        ((eq? oldR (car lat))
         (multiinsertLR&co new oldL oldR
                           (cdr lat)
                           (lambda (newlat L R)
                             (col (cons oldR (cons new newlat)) L (+ R 1)))))
        (else
         (multiinsertLR&co new oldL oldR
                           (cdr lat)
                           (lambda (newlat L R)
                             (col (cons (car lat) newlat) L R))))))

(multiinsertLR&co 'salty 'fish 'chips '(chips and fish or fish and chips) (lambda (lat a b) (cons a (cons b lat))))
```

