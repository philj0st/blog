---
title: logical operators from lisp ifs
date: 2016-04-28 14:04:17
tags: [lisp, AND, NAND, Logical Operators]
categories:
- tech
- Lisp
---
Mentioning my progress in learning Lisp during a check-in today got us into expressing `AND` `OR` and `NOT` with the Lisp `if` expression.
![AND Logic Gate](https://upload.wikimedia.org/wikipedia/commons/thumb/4/46/And.svg/200px-And.svg.png)
```.lisp
; AND -> A and B can be any expression returning T or NIL
(if A
  B
  A)

;equivalent of
(and A B)
```
Think of it! If A is returns T then our if expression returns the return value of the expression B. Otherwise if A returns NIL the if expression returns A .. which we already know, is NIL.

#### example: even AND divisibleBy4

```.lisp
(defparameter n 8) ;would return T
(defparameter n 6) ;would return NIL

(if (= (mod n 2) 0)
  (= (mod n 2) 0); return the result of the even check
  (= (mod n 4) 0)); return the result of the divisibleBy4 check
```

for non-lispers out there here's an implementation in javascript.
```.js
let checkIfEvenAndDivisibleBy4 = n => {
  if (n%2 === 0) {
    return (n%4 === 0)
  }else {
    return (n%2 === 0)
  }
}

checkIfEvenAndDivisibleBy4(8) //true
checkIfEvenAndDivisibleBy4(6) //false

//equivalent of
(n%2 === 0) && (n%4 === 0)
```
#### Beyond `AND`
of course this also works for `OR`:
```.lisp
(if A
  A
  B)
```
since we only need one of the two expressions to be T, we don't even have to evaluate expression B if A is already T.

To describe `NOT` though we would need the lisp primitives:
```.lisp
(if A
  NIL
  T)
```
That said, here's how a `NAND` would look like
```.lisp
(if ((if A
    B
    A))
  NIL
  T)
```
You probably know that [everything can be built from `nand`](http://www.nand2tetris.org/)

Like and `AND` is built by chaining a `NOT` after a `NAND` like this ;)
```
(defun my-overcomplicated-and (a b)
  (if (if (if a b a)
      NIL
      T)
    NIL
    T))
```
yes .. this is stupid :)
Thanks Nick.
