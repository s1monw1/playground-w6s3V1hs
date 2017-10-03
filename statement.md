# Function Literals with Receiver

Today I want to give a quick introduction to <b>Function Literals with Receiver</b>, also referred to as <b>Lambda with Receiver</b>. This concept is what makes Kotlin great for designing Domain Specific Languages as we know it from Groovy for example. Also, Kotlins standard library includes many functions that many of you may have used or at least read about already: `apply` and `with` are the most prominent ones.


## Preconditions

Kotlin, other than Java, has _proper function types_, which means that variables can for example be of type "function accepting an `Int` and returning a `String`", coded as `(Int) -> String`. These function types can for instance be used as _parameters_ to other functions, which are in turn called "higher order functions":

```kotlin
fun myHigherOrderFun(functionArg: (Int)->String) = functionArg(5)
```

As you can see `myHigherOrderFun` has a parameter of a function type, which it can call in its body. If we now want to use this higher order function, we can make use of a lambda, also known as "function literal":

```kotlin runnable
fun myHigherOrderFun(functionArg: (Int)->String) = functionArg(5)

fun main(args: Array<String>){
    println ( myHigherOrderFun { "The Number is $it" })
}

```
## Function Literals with Receiver

As we've seen, function literals can be used as arguments to other functions, which is a very powerful feature on its own. Nevertheless, Kotlin goes even further and supports the concept of "function literals with receivers". This enables us to _call methods on the <b>receiver</b> of the function literal_ in its body without any additional qualifiers. This is very similar to <a href="https://kotlinlang.org/docs/reference/extensions.html" target="_blank">extension functions</a>, where it's also possible to access members of the receiver object inside the extension. Let's see, what function literals look like:

```kotlin
val greet: String.() -> Unit = { println("Hello $this") }
val countChars: String.() -> Unit = { println("Counted chars: ${length}") }

````

This one defines a variable of type `String.() -> Unit`, which is basically a function type `() -> Unit` with `String` as the <b>receiver</b>. All methods of this receiver can therefore be called in the method body. In the `greet` example `this` is simply used to print the `String`, `countChars` on the other hand makes use of being able to call the member function `length()` without having to use a qualifier (`this.length()` would work, too). The function can be called like so:

```kotlin runnable
val greet: String.() -> Unit = { println("Hello $this") }
val countChars: String.() -> Unit = { println("Counted chars: ${length}") }

fun main(args: Array<String>){
    val txt = "tech.io"

    greet(txt)
    countChars(txt)
}
```

This is essentially all you need to know. As I already mentioned in the beginning, Kotlin's standard library contains methods using this concept, one of which is `apply`. It's defined as follows:

```kotlin
public inline fun <T> T.apply(block: T.() -> Unit): T {       
    block()
    return this 
}
```
As we can easily see, it's an extension function to "everything" literally (generic type `T`), and it expects a function literal with a generic receiver of type `T`, which is executed before the receiver is returned to the caller. This little function is actually fantastic as it provides a way to build certain objects very concisely and easily.

```kotlin runnable
data class GuiContainer(var width: Int = 0, var height: Int = 0, var background: String = "red") {
    fun printMe() = println(this)
}

fun main(args: Array<String>) {
    val container = GuiContainer().apply {
        width = 10
        height = 20
        background = "blueish"
        printMe()
    }
    println("after apply: $container")
}
```

In this example a data class `GuiContainer` is created with default parameters and then the `apply` method is called on it. As we've seen before, it's possible to call any method of our receiver `GuiContainer` and it's possible to set its properties and to call `printMe()` in the end. Since `apply` returns the receiver after it completes, it can directly be assign to a variable. 

I hope this example shows what you can do with function literals with receiver. I really like the feature because it gives us so many possibilities in our day-to-day work. Probably it's one of Kotlin's most important ones :-) I myself found quite some spots in my code, which I could simplify a lot using such receiver-aware functions, maybe you will as well.

I'd like to promote one of the best technical books I've read so far: <a target="_blank" href="https://www.amazon.de/gp/product/1617293296/ref=as_li_tl?ie=UTF8&camp=1638&creative=6742&creativeASIN=1617293296&linkCode=as2&tag=simonwirtzde-21&linkId=a98710bb701f6ee5fe515f1cceea3cca">Kotlin in Action.</a><img src="//ir-de.amazon-adsystem.com/e/ir?t=simonwirtzde-21&l=am2&o=3&a=1617293296" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

Simon