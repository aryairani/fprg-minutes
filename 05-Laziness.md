# FP in Scala, Chapter 5 discussion

## Streams
`def cons[A](hd: => A, tl: => Stream[A]): Stream[A]`

- Streams allow you to define expensive or infinite sequences in a compact way without worrying at the start 
about how much of the stream will be needed.
- Streams can be implemented to remember, or forget, their dynamically computed elements, depending on 
time/memory restrictions.
- `cons` doesn't evaluate its arguments directly.  We sometimes get confused by this because we're used to strict
arguments, which are evaluated before the function is called.  Lazy arguments aren't evaluated before the function
is called; they may or may not be evaluated during the execution of the function.  Scala's lazy arguments are
represented internally as `Function0[A]` aka `() => A`.  That's how they are only maybe evaluated later.

## foldRight
`def foldLeft[B](z: B)(f: (B, A) => B): B`

Like List.foldLeft, Stream.foldLeft applies an accumulating function `f` to a stream from left to right.

`def foldRight[B](z: => B)(f: (A, => B) => B): B`

In contrast to List.foldRight, which applies `f` from right to left, Stream.foldRight applies `f`
from left to right, where the accumulator `=>B` argument is the hypothetical result of having evaluated `f`
starting from the right side of the list.

Because the function's accumulator `=>B` argument is lazy, you can short-circuit the loop by not evaluating it.
This means that the accumulator computed from the right side of the list isn't necessary to the result; 
thus, the evaluation is short-circuited.
Try this at home.

## unfold
`def unfold[A, B](z: B)(f: B => Option[(A, B)]): Stream[A]`

Whereas `fold` reduces a sequence of `A`s to a single `B` value, `unfold` expands a `B` into a sequence of `A` values.
In the case of streams, the defined sequence can be infinite in length.  Many sequences can be compactly represented 
with `unfold`.
