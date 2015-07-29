# FP in Scala, Intro + Chapter 1 Discussion

## Book is more about FP than about Scala?
  Yes, but FP in Scala is great, and this is probably the best book for that.

## How did you come to use Scala?
I was looking for a language to write programs that were:
- high level\* (written in the language of the problem, not of the hardware)
- automatically optimized\*\*
- necessarily correct\*\*\*

I played with Haskell, OCaml; was deterred by an inability to understand the compiler errors.
I gave up and went back to Perl. :wink:

I found Scala 5 years later; the transition from OOP Java to FP Scala could be done more gradually.
Scala is called a "scalable language", because "it scales with you as you learn."  

Using pure or mostly-pure FP on longer-term projects has been super rewarding.  A major feature change is a lot of work, but the compiler tells you every spot that hasn't been made to be consistent, and once the compiler is satisfied, you often have a complex system that, defying all intuition, works correctly on the first run.  I still get mad at the compiler errors sometimes though.

### \* "High-level"
Well-typed FP has its own set of programming patterns that are thought by some to more naturally express computation abstractions.  This is vague, but the book gets into them in Chapters 9–11.

### \*\* "Automatically Optimized"
FP effectively places restrictions on how your code works.  With these restrictions, the compiler can prove 
that values won't change; it may inline constants, avoid locks, reorder code, fuse loops, and more great stuff.

### \*\*\* "Necessarily Correct"
Beyond the improved reasoning that comes from knowing that data can't change out from under you, 
programming with a sophisticated type system becomes a game of setting up rules to cause the
compiler to prevent you from creating an incorrect program.  I think of it as a collaboration between the programmer
and the compiler, where the programmer set out rules about how the program must be pieced together, 
and asks the compiler to alert him if he ever breaks any.

#### Example 1:
```java
int square(int x) { /* ... */ } // java
```
```scala
def square(x: Int): Int = /* ... */ // scala
```
Rules: 

1. `square` must receive an `int`.
2. `square` must be called in a position that can receive an `int`.

#### Example 2:
```scala
def head[A](list: List[A]): A = /* ... */
```
Rules:

1. `head[A]` must receive a `List[A]`.
2. `head[A]` must be called in a position that can receive an `A`.
3. If `head[A]` returns, the result of `A` is one of the elements of `list`. (Where else could the value come from?)

for any type `A`.

#### Example 3:
```scala
def foo[A](a: A): A = /* ... */
```
Rules:

1. `foo[A]` must receive an `A`.
2. `foo[A]` must be called in a position that can receive an `A`.
3. If `foo` returns, the result of `A` is the argument `a`. (Same reasoning as above.)

for any type `A`.

These rules reduce the space of possible programs.  
The more expressive a type system is, the more expressive the rules can be, 
and the more you can do to narrow the space of possible programs around the one you intend.

## What is Scala used for?
- Often as a Java++ (more convenient than Java)
- Most mature strongly-typed FP language on JVM

## What is FP used for?
  Any application where errors are more expensive than learning FP is. (financial, aeronautics, telecom)
  The good news is: the learning curve is a one-time cost, not per-project.

## Scala is imperfect; what are some flaws?
- expectations too high
- experimental features (understandably) aren't well documented
- complaints about Scala's standard library (popular 3rd-party libraries help)
- local type inference sometimes can't infer what I want (compare to global, backtracking type inference in Haskell, OCaml)
- type inferencer sometimes infers surprising/annoying types (e.g. inferring `Any` or `Nothing` due to variance rules)

## Can I have a different definition of pure?
- What if I don't care about the side effects of logging?
- Why is `new Foo` considered pure, when it's changing the structure of the heap?

I think you could have a different definition of pure. FPiS says:
> An expression `e` is referentially transparent if, for all programs `p`, all occurrences of `e` in `p` can be replaced by the result of evaluating `e` without affecting the meaning of `p`. A function `f` is pure if the expression `f(x)` is referentially transparent for all referentially transparent `x`.

I think you can apply your own definition of "affecting the meaning" to the definition above, and get your own definition of purity.

* You are welcome to ignore the side effect of logging if you consider it to be unobservable.
* The book considers heap changes to be unobservable (and on the JVM, until you run out of memory, I think it's fair to say that they are!), so heap allocations and `new` are pure.
