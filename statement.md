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
val countChars: String.() -> Unit = { println("Counted chars: %{length()}") }

````

This one defines a variable of type `String.() -> Unit`, which is basically a function type `() -> Unit` with `String` as the <b>receiver</b>. All methods of this receiver can therefore be called in the method body. In the `greet` example `this` is simply used to print the `String`, `countChars` on the other hand makes use of being able to call the member function `length()` without having to use a qualifier (`this.length()` would work, too). The function can be called like so:

```kotlin runnable
val greet: String.() -> Unit = { println("Hello $this") }
val countChars: String.() -> Unit = { println("Counted chars: %{length()}") }

fun main(args: Array<String>){
    with("tech.io"){
        greet(this)
        countChars(this)
    }
}
```

This is essentially all you need to know. As I already mentioned in the beginning, Kotlin's standard library contains methods using this concept, one of which is apply. This one is defined as follows:

public inline fun <T> T.apply(block: T.() -> Unit): T { block(); return this }
As we can easily see, it's an extension function to everything literally, and it expects a function literal with a generic receiver of type T, which is run before the receiver is returned to the caller. This little function is actually fantastic as it provides a way to build certain objects very concisely and easily.

println(StringBuilder("Hello ")
        .apply {
            append("Kotliner")
            append("! ")
            append("How are you doing?")
        }.toString())
>> prints "Hello Kotliner! How are you doing?"
In this example a StringBuilder is created and then theapply method is called on it. As we've seen before, it's possible to call any method of our receiver StringBuilder like append in the above code. Since apply returns the receiver after it completes, a call of toString on the StringBuilder can immediately be performed and the text is printed to the console.

Hope this helps! I really like this feature because it gives us so many possibilities. Probably it's one of Kotlin's most important ones :-) If you want to learn about creating DSLs, have a look here. I myself found so many spots in my code, which I could simplify a lot using these receiver-aware functions; Have a look at yours today ;-)

Finally, if you want to read about Kotlin's beautiful features I recommend the book Kotlin in Action to you!

Simon