# How we code

The first and foremost concern when building systems is how it is
expressed in its given substrate. For us as software engineers that
expression takes the form of code.

It is important to find a common ground of practices and rules from
which we individually have the freedom to explore ideas and different
means of expressing our domain.

While the aim is to allow as much freedom as possible, some very broad
restrictions will be made on what paradigms we use to express code
in. This is neccessary in order for all of our code to form a
coherrent whole. To reach goal absolute and bona-fide compliance with
the spirit and princple of these rules are required by each one of us.

Before we stake out the territory where-in we will construct our code
we must first consider the values and goals of our code. These are:

**The Supremacy of Expression**

When we build software the key value that we create is the actual
algorithm that we construct. Not the comments, not build-scripts, not
the boilerplate. The expression of the algorithm is at the heart of
the program and it is our first and foremost job to find the proper
expression of the program. The expression of a program is wholey
separate from concerns such as naming, where to put line-breaks or how
many characters may be placed on a single line. By expression we mean
the structure of the program as seen by the complier. A programmer is
the bonsai artist to the AST.

**The Fallacy of Prediction**

All learning is prediction, and as we grow more competent as engineers
we become better at predicting what will happen before it does. But as
our certainty grows, as does our hubris. We start looking to far
ahead, we start modelling before we have results and we start adding
features before they are neccessary. Most of the time our predictions
are fairly close to reality, but they never truly hit the mark. In
most endeavours this is acceptable, however when building software,
being not-quite-right is worse than being entirely wrong. The impulse
to predict is inately human, so hard-wired in our cognition that it
can be frustrating to keep in check; we must consciously strive to
defer judgement until we know exactly what to build before building
it. If we don't, the best of intentions lead to worst of designs.

**The Complexity of State**

The most difficult part about reasoning about programs is mentally
keeping track of mutable state. When state exists, we must keep track
of the order of operation, as well as reasoning about initial and
terminal states when processing some instruction. Even when
non-observable away, state presents a significant challenge when
determining the correctness of a program. Of course, some state is
neccessary for most applications, deciding on where the neccessary
state is placed is absolutely critical. In some cases it is
appropriate to move the state into another system that provides the
proper semantics for mutating the state (i.e. a database), in others
it is about having one central point of mutability.

**The Erraticness of Side-effects**

Side-effects are in many senses similar to state. Both are neccessary
in most useful programs but cause problems when reasoning about
them. Side-effects are particularly difficult as they introduce error
conditions that are non-deterministic with regards to the input. Just
like with state, we mitigate the impurities by a thin central layer of
translation between the pure and impure world.

## Practices

- **Use functional programming** - Functional programming shares the aims of reducing side-effects and state. Don't fallback on imperative or object-oriented programming

- **Aim for one-line functions** - Most functions can be expressed a single expression, if it doesn't the function is probably too big.

```kotlin
// Avoid
fun foo(a: A, b: B) : C {
  val derp = a.foo()
  return b.bar(derp)
}

// Prefer
fun foo(a: A, b: B): C = b.bar(a.foo())
```

- **Prefer functional-data processing to imperative** - Using well-known higher-order functions constrains the number of ways to write something

```kotlin
// Avoid
for (x: Int in xs) {
  println(x)
}

// OK
xs.forEach { println(it) }

// Prefer
xs.forEach(::println)
```

- **Prefer immutable classes** - Classes should should generally represent values, not identities

```kotlin
// Avoid
class Foo {
    private val bar = null
    fun setBar(String bar) {
        this.bar = bar
    }
    fun getBar() : String = bar
}

// Prefer
data class Foo(val bar: String)
```

- **Don't use exceptions for flow-control** - Exceptions are only for unrecoverable errors, `panic`s in erlang parlance. Instead model the results as values

```kotlin
// avoid
fun getMOTD() : MOTDResult {
  val res = recv(1024)
  if (res.startswith("error")) {
    throw Exception()
  }
}

// prefer
sealed class MOTDResult
data class MOTDError(error: String): MOTDResult
data class MOTDSuccess(content: String): MOTDResult

fun getMOTD() : MOTDResult {
  val res = recv(1024)
  if (res.startswith("error") {
    return MOTDError(res)
  } else {
    return MOTDSuccess(res)
  }
}
```

- **Use Kotlin's object extensions to simplify code** - They often provide logical groupings of side effects when dealing with legacy code
```kotlin

// Avoid
fun getClient(): SomeClient {
   val sc = SomeClient()
   sc.hostname = "party-server"
}

// Prefer
fun getClient(): SomeClient = SomeClient().apply {
  hostname = "party-server"
}
```

- **Never use semi-colons** - Idiomatic kotlin doesn't use semicolons and neither do we

```kotlin
// avoid
val foo = "bar";
val baz = "derp"

// Prefer
val foo = "bar"
val baz = "derp"

```

- **Don't use wildcard imports**

```kotlin
// Avoid
import foo.bar.baz.*

// Prefer
import foo.bar.baz.Fizz
import foo.bar.baz.Buzz
```

- **No consecutive blank-lines** - Separate "paragraphs" with a single blank-line.

```kotlin

//Avoid
val doSomething = doIt()


val doSomethingElse = doThat()

// Prefer
val doSomething = doIt()

val doSomethingElse = doThat()
```

- **Be conservative with blank-lines** - Don't break code into too many paragraphs

```kotlin

// Avoid
fun foo() {
   val thing1 = herpDaDerp()

   val thing2 = derpDaHerp()

   return combine(thing1, thing2)
}

// Prefer
fun foo() {
   val thing1 = herpDaDerp()
   val thing2 = derpDaHerp()

   return combine(thing1, thing2)
}
```
- **Use four spaces for indentation** - Recomended by Kotlin.

- **Always use braces for IFs unless one-line expressions**
```kotlin

// Avoid
val foo =
  if(bar.baz)
    "yay"
  else
    "nope"

// Prefer
val foo = if(bar.baz) "yay" else "nope!"

// or prefer
val foo = if (bar.baz) {
  "yay"
}  else {
  "nope"
}
```

- **Prefer string interpolation to explicit concatenation**

``` kotlin

// Avoid
val foo = "Hello there " + name + "!"

// Prefer
val foo = "Hello there $name!"
```

- **Avoid do in function names unless specifically causing a side-effect**

- **X, Y, A, B, C are acceptable  parameter names IFF for primary subjects of unary and binary functions** - Likewise, abbreviating the Type itself is OK.

- **When creating functors or monads implement `map` and `flatMap` respectively. Example to come.

- **when a function consists soley of a when exprssion use its brackets**

```kotlin
// Avoid
fun myWhen(willUGoOutWithMe : Outcomes): String {
  when(willUGoOutwithme) {
    is YourNotMyType -> "ok"
    is ImWayOutOfUrLeauge -> "ok"
    is IHaveABoyFriend -> "ok"
    is IHaveARestrainingOrder -> "atleast i get surprise cuddles in jail"
  }
}
// Prefer
fun myWhen(willUGoOutWithMe : Outcomes) = when(willUGoOutwithme) {
 is YourNotMyType -> "ok"
 is ImWayOutOfUrLeauge -> "ok"
 is IHaveABoyFriend -> "ok"
 is IHaveARestrainingOrder -> "atleast i get surprise cuddles in jail"
}
```

- **Create empty functions using Unit instead of empty brackets
``` kotlin
// Avoid
fun myFun() {}
// Prefer
fun myFun() = Unit
```

- ** When doing a multiline data-class break the line at the parenthesis**

``` kotlin
// Prefer
data class Person(
    val firstName: String,
    val lastName: String,
    var age: Int
)
// Avoid
data class Person(val firstName: String,
                  val lastName: String,
                  var age: Int)
```
