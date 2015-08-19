# FP in Scala, Chapter 2 discussion

## If a tail-recursive function is going to be transformed into an iterative one, is there any reason not to just write the iterative one to begin with?

<!-- Is there any benefit to writing tail-recursive pure functions if it's just going to be converted to an iterative algorithm, beyond recovering the performance that an iterative algorithm would have had? -->

Maybe? 

There's nothing inherently wrong with using iteration or overwriting variables, it's just that doing it by hand carries some risk.  I trust today's compilers to perform that transformation better than I can, just like I trust them to write machine code better than I can.

For simple functions like `fib` or `factorial`, it's not too hard to get them right no matter which methodology you use, but I believe that FP methodologies scale better to big systems (especially with big development teams) than non-FP methodologies do.

## No, I mean, would I prefer a tail-recursive version to a non-tail-recursive version for reasons other than speed and constant stack space?

Oh --- no, it's primarily about stack space and secondarily about speed.

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
        * is an error
    * `def k[A]: Int => A`
        * is also an error
    * `def m[A,B](a: A, p: (A,A) => B): B`
        *  it's implementation must be `p(a,a)`

* Scala, like most (but not all) languages, includes a handful of ways of making a liar out of the type signature (our book probably doesn't mention them); we will try to avoid them.  Apart from these "escape hatches", the compiler requires the type signatures to be true --- much more reliable than identifier names or comments.

\*With respect to `def f: Int => Int` above: This doesn't sound like much information, but (2<sup>32</sup>)<sup>(2<sup>32</sup>)</sup> is insignificant compared to the number of implementations an impure function with the same signature can have.  Because of this, we're not used to type signatures telling us much at all.  In typed FP though, we rely on them tremendously; they are the preferred form of documentation, because they are always true.
    
We started on Tony Morris's [parametricity exercises](https://github.com/refried/parametricity-exercises/blob/master/parametricity.scala), but had some trouble because they refer to data structures and concepts we haven't covered yet: `Option` (Sec 4.3), `Either` (Sec 4.4), property-testing (Chapter 8), higher-kinded types (Sec 10.5), Monads (Chapter 11).
