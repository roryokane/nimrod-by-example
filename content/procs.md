---
title: Procs
---
# Procs

Procedures in Nimrod are declared using `proc` and require that their parameter and return types be annotated. After the types and parameters, an `=` is used to denote the start of the function body. Another thing to note is that procedures have uniform function call syntax, which means that they can called as both `foo(a, b)` or `a.foo(b)`.

``` nimrod
proc fibonacci(n: int): int =
  if n < 2:
    result = n
  else:
    result = fibonacci(n - 1) + (n - 2).fibonacci
```

## Exporting symbols

<!-- XXX Move into module topic -->
Encapsulation is also supported, not by conventions such as perpending the name with underscores but by annotating a procedure with `*`, which exports it and makes it available for use by modules.

``` nimrod
# module1:
proc foo*(): int = 2
proc bar(): int = 3

# module2:
echo foo()  # Valid
echo bar()  # will not compile
```

## Side effect analyses

Nimrod provides support for functional programming and so includes the `{.noSideEffect.}` pragma, which statically ensures there are no side effects.

``` nimrod
proc sum(x, y: int): int {. noSideEffect .} =
  x + y

proc minus(x, y: int): int {. noSideEffect .} =
  echo x  # error: 'minus' can have side effects
  x - y
```

## Operators

To create an operator, the symbols that are to be used must be encased inside `` ` ``s to signify they are operators.

``` nimrod
proc `$`(a: array[2, array[2, int]]): string =
  result = ""
  for y in a:
    for vx in v:
      result.add($vx & ", ")
    result.add("\n")

echo [[1, 2],  # See Varargs for how 
      [3, 4]]  # echo works

proc `^&*^@%`(a, b: string): string =
  ## An confusingly named useless proc
  result = a[0] & b[high(str)]

assert("foo" ^&*^@% "bar" == "fr")
``` 

## Generic Functions

<!-- XXX Needs own section -->
Generic functions are like C++'s templates and allow for the same statically checked duck-typing semantics as templates. 

``` nimrod
# Not really good idea for obvious reasons
let zero = ""
proc `+`(a, b: string): string =
  a & b

proc `*`[T, I](a: T, b: int): T =
  result = zero
  for i in 0..b:
    result = result + a

assert("a" * 10 == "aaaaaaaaaaa")
```