# FP in Scala, Chapter 2 discussion

## If a tail-recursive function is going to be transformed into an iterative one, is there any reason not to just write the iterative one to begin with?

<!-- Is there any benefit to writing tail-recursive pure functions if it's just going to be converted to an iterative algorithm, beyond recovering the performance that an iterative algorithm would have had? -->

Maybe? 

There's nothing inherently wrong with using iteration or overwriting variables, it's just that doing it by hand carries some risk.  I trust today's compilers to perform that transformation better than I can, just like I trust them to write machine code better than I can.

For simple functions like `fib` or `factorial`, it's not too hard to get them right no matter which methodology you use, but I believe that FP methodologies scale better to big systems (especially with big development teams) than non-FP methodologies do.

## No, I mean, would I prefer a tail-recursive version to a non-tail-recursive version for reasons other than speed and constant stack space?

Oh, no.  It's for stack space and speed.

# Tail recursion
There were some questions after class about writing tail-recursion in general.

A function is tail-recursive if, when it calls itself, it doesn't do any additional processing on the result of that call.  So:

```scala
def hello(x: Int): String = 
  if (x < 5) "hello!" else hello(x-1)
```

is tail recursive, but 

```scala
def HELLO(x: Int): String = 
  if (x < 5) "HELLO" else HELLO(x-1) + "!"
```

is not, because it calls itself and then does something more with the answer first (append the "!").  Tail-call optimization works by throwing away the current stack frame before recursing, but it can't if it needs to come back and do more work after.

`HELLO2` is tail recursive, and carries the remaining work to do forward with it as a parameter, instead of returning to the previous level to finish.

```scala
def HELLO2(x: Int, suffix: String): String = 
  if (x < 5) "HELLO" + suffix else HELLO2(x-1, suffix + "!")
```

# Parametricity

We flipped through Tony Morris's slides on [Parametricity](http://tonymorris.github.io/talks/#4985cb8e6d8d9a24e32d98204526c8e3b9319e33), and talked about how:

* Function names don't tell us much about what a function does. They might say precisely what it does, they may give a hint about what it does, or they sometimes they lead you to believe it does something completely different from what it actually does.  Comments can similarly be misleading.

* When we can assume pure functions, then type signatures tell us a lot about what a function can or can't do:
    * `def f: Int => Int` 
        * `f` is one of (2<sup>32</sup>)<sup>(2<sup>32</sup>)</sup> possible mappings from `Int` to `Int`.  (*see below)
    * `def g[A]: A => A` 
        * its implementation must be `(a: A) => a`
        * i.e. it returns its argument
    * `def h[A]: A => Int`
        * ignores its argument and returns a constant `Int`
    * `def i[A]: A => List[A]`
        * returns its argument, repeated a constant number of times
    * `def j[A,B]: A => B`
        * is uninhabited (no legal implementation)
    * `def k[A]: Int => A`
        * is also uninhabited
    * `def m[A,B](a: A, p: (A,A) => B): B`
        *  it's implementation must be `p(a,a)`

* Scala, like most (but not all) languages, includes a handful of ways of making a liar out of the type signature (our book probably doesn't mention them); we will try to avoid them.  Apart from these "escape hatches", the compiler requires the type signatures to be true; we can't say the same of identifier names or comments.

\*With respect to `def f: Int => Int` above: This doesn't sound like much information, but (2<sup>32</sup>)<sup>(2<sup>32</sup>)</sup> is insignificant compared to the number of implementations an impure function with the same signature can have.  Because impure functions can do literally anything, we're not used to type signatures telling us much at all.  In typed FP though, we rely on them tremendously; they are the preferred form of documentation because they are always true.  The more semantic information you can work into the type signature, the better.  Names and comments are next-best, for information that can't be encoded in the machine-verifiable language of the type system.
    
We started on Tony Morris's [parametricity exercises](https://github.com/refried/parametricity-exercises/blob/master/parametricity.scala), but had trouble because they refer to data structures and concepts we haven't covered yet: `Option` (Sec 4.3), `Either` (Sec 4.4), property-based testing (Chapter 8), higher-kinded types (Sec 10.5), monads (Chapter 11), infix type constructor syntax (Pg 244).
