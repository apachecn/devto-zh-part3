# 编译器基础:闭包转换

> 原文：<https://dev.to/rain1/compiler-fundamentals-closure-conversion-4jc6>

```
#lang racket

;; this is a stand alone simple version of the closure
;; conversion part of the hoist pass from the tarot compiler
;; see https://rain-1.github.io/scheme for more.

(require data/queue)

;; closure conversion for lambda calculus
;;
;; the input language is:
;;
;; <l> ::= <var>
;;       | <datum>
;;       | (lambda (<var> ...) <l> ...)
;;       | (begin <l> ...)
;;       | (<l> <l> ...)
;;
;; the output language is
;;
;; <cc> ::= (var loc <index>)
;;        | (var env <index>)
;;        | (var glo <var>)
;;        | <datum>
;;        | (closure (<capture> ...) <cc>)
;;        | (begin <cc> ...)
;;        | (<cc> <cc> ...)
;;
;; <capture> ::= (var loc <index>)
;;             | (var env <index>)
;;
;; Variables have been annotated with their storage type and
;; lambda functions have been replaced with closure objects
;; all of the variables captured by a lambda have been packaged
;; up with the closure object

;;;; HELPERS

(define (every p l)
  (if (null? l)
      #t
      (and (p (car l)) (every p (cdr l)))))

(define (index v l)
  (for/first ([i (in-naturals)]
              [elt (in-list l)]
              #:when (equal? elt v))
    i))

(define (queue-index v q)
  (for/first ([i (in-naturals)]
              [elt (in-queue q)]
              #:when (equal? elt v))
    i))

(define-syntax mapply
  (syntax-rules ()
    ((mapply f xs arg ...)
     (map (lambda (x) (f x arg ...)) xs))))

;;;; SHAPES

(define (var? x) (symbol? x))
(define (datum? x) (or (boolean? x) (number? x)))
(define (lambda? x)
  (if (and (pair? x) (eq? 'lambda (car x)))
      (if (and (every symbol? (cadr x))
               (not (null? (cddr x))))
          #t
          (error "malformed lambda expression" x))
      #f))
(define (lambda-bindings x) (cadr x))
(define (lambda-body x) (implicit-begin (cddr x)))
(define (begin? x) (and (pair? x) (eq? 'begin (car x))))
(define (application? x) (pair? x))

(define (implicit-begin xs)
  (if (null? xs)
      (error "empty expression list")
      (if (null? (cdr xs))
          (car xs)
          `(begin . ,xs))))

;; CLOSURE CONVERSION

(struct scope (locals env captures globals))
;;
;; locals is a list of symbols
;; - it's the variables bound by the current lambda alternatively the top stack frame
;;
;; env is a list of symbols
;; - is a list of every non-global variable that has been brought into scope by lambda binders
;;   for example in (lambda (a b) (lambda (x y) <here>)) at the point <here> the env is (x y a b)
;;
;; captures is a queue of symbols
;; - If you reference a variable that isn't global or local it'll be captured and put into this queue
;;   at the end of processing a subexpression, this queue becomes the closure environment
;;
;; globals is a list of symbols
;; - car, cdr, cons etc.

(define (classify var scope)
  ;; classify a variable with its storage type based on a scope
  (cond ((index var (scope-locals scope))
         => (lambda (i) `(var loc ,i)))
        ((member var (scope-env scope))
         (cond ((queue-index var (scope-captures scope))
                => (lambda (i) `(var env ,i)))
               (else
                (enqueue! (scope-captures scope) var)
                (let ((i (- (queue-length (scope-captures scope)) 1)))
                  `(var env ,i)))))
        ((member var (scope-globals scope))
         `(var glo ,var))
        (else (error "unbound variable error" var))))

(define (cc exp sc)
  (cond ((var? exp) (classify exp sc))

        ((datum? exp) exp)

        ((lambda? exp)
         (let* ((vars (lambda-bindings exp))
                (body (lambda-body exp))
                (captures^ (make-queue))
                (sc^ (scope vars
                            (append (scope-locals sc) (scope-env sc))
                            captures^
                            (scope-globals sc)))
                (body^ (cc body sc^)))
           `(closure ,(mapply cc (queue->list captures^) sc) ,body^)))

        ((begin? exp)
         `(begin . ,(mapply cc (cdr exp) sc)))

        ((application? exp)
         (mapply cc exp sc))

        (else (error "malformed expression in cc" exp))))

;;;; TESTING, EXAMPLES

(define (print x) (display x) (newline))

(define (go exp)
  (let ((res
         (cc exp (scope '()
                        '()
                        (make-queue)
                        '(car cdr cons)))))
    (print exp)
    (display "==> ")
    (print res)
    (newline)))

(define (test)
  (go '3)
  (go '(lambda (x) x))
  (go '(lambda (x) (car x)))
  (go '(lambda (x) (x 5 x 7)))
  (go '(lambda (x y) (x y)))
  (go '(lambda (x y) (y x)))
  (go '(lambda (z) (lambda (x y) (y x))))
  (go '(lambda (x y) (lambda (z) (y x))))
  (go '(lambda (x) (lambda (y) (lambda (z) (x y z))))))

(test)

;; 3
;; ==> 3
;; 
;; (lambda (x) x)
;; ==> (closure () (var loc 0))
;; 
;; (lambda (x) (car x))
;; ==> (closure () ((var glo car) (var loc 0)))
;; 
;; (lambda (x) (x 5 x 7))
;; ==> (closure () ((var loc 0) 5 (var loc 0) 7))
;; 
;; (lambda (x y) (x y))
;; ==> (closure () ((var loc 0) (var loc 1)))
;; 
;; (lambda (x y) (y x))
;; ==> (closure () ((var loc 1) (var loc 0)))
;; 
;; (lambda (z) (lambda (x y) (y x)))
;; ==> (closure () (closure () ((var loc 1) (var loc 0))))
;; 
;; (lambda (x y) (lambda (z) (y x)))
;; ==> (closure () (closure ((var loc 1) (var loc 0)) ((var env 0) (var env 1))))
;; 
;; (lambda (x) (lambda (y) (lambda (z) (x y z))))
;; ==> (closure () (closure ((var loc 0)) (closure ((var env 0) (var loc 0)) ((var env 0) (var env 1) (var loc 0))))) 
```