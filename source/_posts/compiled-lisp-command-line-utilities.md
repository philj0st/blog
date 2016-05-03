---
title: compiled lisp command line utilities
date: 2016-05-02 23:23:46
tags: [lisp, clisp, compiled, command line]
categories:
- tech
- Lisp
---
`clisp --help`'s line ` -c [-l] lispfile [-o outputfile] - compile lispfile
` got my curiosity.
Stack Overflow told me i can access command line arguments with `*args*`.

So let's compile the 8th of the 99-lisp-problems.

```.lisp
(defun unique (lst)
  (when lst
    (cond
      ; if the 1st and 2nd list items are the same, skip car and call unique on the cdr
      ((equal (car lst) (cadr lst)) (unique (cdr lst)) )
      ; else, just append the car to the rest of the unique list
      (T (append (list (car lst)) (unique (cdr lst))) )
    )
  )
)
; *args* it's been given from the commandline
(print (unique *args*))

```
running `clisp -c unique.lisp` and changing execution permissions.
```.bash
phil@HAL:~/development/lisp/scratchpad$ 🐵  clisp -c unique.lisp
phil@HAL:~/development/lisp/scratchpad$ 🐵  chmod 755 unique.fas
phil@HAL:~/development/lisp/scratchpad$ 🐵  mv unique.fas unique

```
to let bash know how to execute it, prepend the shebang line `#!/usr/bin/clisp` (or find out ur own path to clisp with `whereis clisp`) to `unique.fas`.

et voila:

```.bash
phil@HAL:~/development/lisp/scratchpad$ 🐵  ./unique a b c c c d d e f f
("a" "b" "c" "d" "e" "f")  

```
symbolic linking `unique` to /usr/bin with `ln -s` would let us call it without the `./` and would let it look like some of our gnu utils or shell builtin ;)
