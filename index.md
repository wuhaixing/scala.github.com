---
layout: cheatsheet
title: Scalacheat
by: (initial version: Brendan O'Connor) HamsterofDea
about: Thanks to <a href="http://brenocon.com/">Brendan O'Connor</a>, this cheatsheet aims to be a quick reference of Scala syntactic constructions. Licensed by Brendan O'Connor under a CC-BY-SA 3.0 license. Pimped by HamsterofDeath who doesn't care about the license and just wants to help scala to spread :)
---

###### Contributed by {{ page.by }}

|note: "this is a lie" means i gave a simplified explanation and left out details which will be covered later                                                                                                          |                 |
| ------                                                                                                   | ------          |
|  <h2 id="declarations">基本声明</h2>                                                                       |                 |
|  `var x = 5`<br>`x = 6`                                                                                             |  可变变量, 其值可修改       |
|  <span class="label success">好</span> `val x = 5`<br> <span class="label important">坏</span> `x=6`  |  不可变, 不能修改其值       |
|  `var x: Double = 5`                                                                                     |  明确类型. 如果未说明,编译器会自己决定 (more later) |
|  `var x: Double = 5;val y = 42`                                                                                     |  本行最后一条语句的分号是可选的 |
|  `var x = {5}`<br>`var x = {1+2}`             |  也可以用表达式的结果赋值  |
|  `def x:String = return "hello world"`                 | 声明方法                      |
|  `def x(foo:String):String = return foo+"hello world"`                 | 声明带有简单参数的方法                      |
|  `def x(foo:String, bar:String):String = return foo+"hello world"+bar`                 | 声明带有两个参数的方法                      |
|  `def x:String = {`<br>`val x = 1`<br>`return "hello world"+1`<br>`}`                 | 多行语句需要用 {} 括起来                      |
| `def x = "hello world"`|关键字 return 和返回类型的声明可选, 但如果方法中有 return, *必须* 明确指定返回类型. 默认的返回值是代码中最后一条语句的值(所以别随便移动最后一条语句哦). 
|`def iAcceptVarArgs(i:Int,s:String,d:Double*) = {...}`|参数数量可变的方法|
|`def x = {def y = 7;y}` | 可以在方法内声明方法|
|<h2 id="primitive types">基础类型</h2>|
|`Byte, Char, Boolean, Double, Float, Long, Int, Short`|基础类型. 他们会自动的 boxed 或 unboxed, 不需要再用对应的java.lang.Number. 我在这里用java.lang.Integer们及其类型结构完全是为了java开发人员着想.|
|<h2 id="syntax details">语法细节</h2>|
|不知道该放哪的一些内容||
|`instance.bar`|bar 可能是一个field访问,或者一个无参方法调用. scala 里面两者是没有区别的. 因此在 def 和 val 之间你可以根据个人喜欢选择|
|`instance.bar()`|对于方法调用, 可以加上 (). 依照规范, 不带 () 的方法只进行计算，返回结果，不会改变任何状态.调用带 () 的方法是为了其效果，比如改变一个值，在终端上打印内容等|
|`instance bar`|如果对于编译器来说你的意图很明显，就可以省略 "." 通常人类和编译器都会同意把类实例放左边，它的方法放右边，这对于DSL很有用|
|`println {"hello world"}`|对于单参方法，你可以用一个block代替(). 这个对 DSLs 也很有用|
|  <h2 id="declarations2">不是那么基本的声明</h2>                                                                       |                 |
| `val x = {`<br>`class y(val z: String)`<br>`new y("hello").z`<br>`}`<br><span class="label important">坏</span>`val foo = new y("does not work outside the block above")`| 任何东西都可以嵌入其他东西，但只能在其作用域内访问到|
| `lazy val x = expensiveOperation()`|这个昂贵的操作仅在需要x的时候执行, 之前不会|
| `def method(a:String = "hello", b:String = "world") = a+" "+b`|带默认值的方法|
| `method("goodbye")`|调用上面的方法, 未指定的参数会使用其默认值. 返回 "goodbye world"|
| `method(b = "friend")`|调用上面的方法, 明确传入参数b. a 用默认的 "hello". 返回 "hello friend"|
|`def method(param: => String)`|"=>" 的含义是，当调用这个方法时，参数是一个函数的返回值,每次访问这个参数，都要执行此函数进行计算(see Iterator.continually). 计算结果不会被cached, 但可以传递一个lazy val使它cached.|
|`def method(s:String)(i:Int)`|有多个参数列表的方法|
|`val intermediate = method("hello")`<br>`intermediate(5)`|why? 因为你可以先传递一个参数列表， 稍后再提供另一个。可以绕过 "不完整的调用" . 在java中，你得为这个创建个builder.|
|  <h2 id="object_orientation">与OO相关的声明</h2>                                                     |                 |
| `class Foo`| class 声明 - 可嵌套|
| `class Foo(var x:String, val y:Int)`|带有2个公开field 的 class 声明, 一个可变, 一个不可变. 构造函数式自动生成. 只能用new Foo("1",2)|
| `class Foo {`<br>`var x = 5`<br>`val y = 6`<br>`}`|和上面那个差不多 , 但是用的是默认函数, 也就是new Foo()|
| `class Foo {def x = 5}`|默认的构造函数，声明了方法x|
|  `class C(x: R)` _等同于_ <br>`class C(private val x: R)`<br>`var c = new C(4)`                         |  构造参数 - 如果没有明确val,自动转化成私有field. |
|  `class C(val x: R)`<br>`var c = new C(4)`<br>`c.x`                                                      |  构造参数 - 明确val,作为公开 fields |
|  `class C(var x: R) {`<br>`assert(x > 0, "positive please") //class body = constructor`<br>`var y = x // 公开 field`<br>`val readonly = 5 // 只读 field`<br>`private var secret = 1 // 私有 field`<br>`def this = this(42) // 替代构造函数`<br>`}`|包含常用情况的简单例子. 注: 所有的替代构造函数必须调用类声明中声明的主构造函数.以确保不会因为构造函数重载忘记初始化某个field.|
|  `new{ ... }`                                                                                            |  匿名类. more later (see implicits)|
|`new Foo {...}`|Foo的匿名子类. Foo 可能是 class 或 trait|
|  `abstract class D { ... }`                                                                              |  定义一个抽象 class. (不可实例化, 必须创建其子类) |
|  `class C extends D { ... }`                                                                             |  定义一个继承类. |
|  `class D(var x: R)`<br>`class C(x: R) extends D(x)`                                                     |  继承与构造参数)
|`abstract class Foo {def bar:String}`|不带body的方法或没有value的val是抽象的，不需要显示声明 "abstract". 覆盖抽象的 def 或 val 也不需要 "override" 关键字. 但覆盖非抽象的 def 或 val 时必须带有 "override" 关键字.|
|  `classOf[String]`                                                                                       |  class 字面值. |
|  `x.isInstanceOf[String]`                                                                                |  类型检查 (运行时) |
|  `x.asInstanceOf[String]`                                                                                |  类型转换 (运行时) |
|`case class Foo; case object Bar`|遇到关键字 "case" ，编译器会为其生成 equals & hashcode 方法，并且其构造参数都是只读的公开fields|
|  <h2 id="functiondeclaration">函数声明</h2>                                                                       |                 |
| `(i:Int) => i+1`|创建一个函数.| 
| `var func = (i:Int) => i+1`|创建函数并把它声明为一个变量 func: Int => Int = <function1>|
| `func(5)`|执行上面的函数|
| `def func = (i:Int) => i+1`|每次调用方法的时候都创建一个函数并返回该函数,不是i+1 func: Int => Int|
|`val func:(Int) => String = (i:Int) => i.toString`|这样你就了解函数类型的语法了吧 :)|
|`def takesFunction(f:(Int) => String) = f(5)`| 用上面函数类型的函数作为参数的方法，并调用该函数.编译器能推断出返回类型为"string".|
|`def method(i:Int) = t.toString;val func = method _`|在任何方法后面加上 "_" 都可以把他变成一个函数|
|`takesFunction(method)`|这样也行, 当情况很明显的时候，编译器会为你做这种转换|
|`def method(s:String)(s2:String) = s+" "+s2`<br>`val intermediate:(String)=>String = method("hello")`<br>`intermediate("world")`|再访参数列表: the intermediate, "非完整方法调用" 是函数. 最终结果是 "hello world"|
|`func(5)`<br>`func.apply(5)`|实际上是调用了函数实例的apply方法, 只是你不必那么写罢了.如果没有写明方法名,编译器会假定你想调用 "apply" |
|`def createFunction = (i:Int) => i+1`<br>`createFunction(5)`|如果看过了上面的语法部分, 你应该能明白这发生了什么. 首先, createFunction的调用没有 () 也没有参数. 然后, 5 传递给 creatFunction的返回结果的 apply 方法|
|  <h2 id="typeinference">返回类型与类型推断</h2>                                                                       |                 |
|  `val x = "hello"`|编译器总是会选择最可能的类型, 在这里是 java.lang.String|
|  `val x:Serializable = "hello"`|你总能指定更通用的类型|
|  `def x {print("hello world")}`                 | 没有 "=" 的方法表明这个方法没有返回类型，或者返回类型为void（逗你玩呢） | 
|  `def x:Unit = {...}`<br>`def x() {...}`|去掉方法声明中的 "=" 和指定返回类型为 "Unit" 是一样的|
| `val blocks = {{{{5}}}}`|每个代码块都会返回给外层代码块一个值|
| `val block = if (a) foo else bar`|几乎一切都是表达式，因此，都有返回类型，包括if-else结构|
|`def x = {`<br>`if (System.currentTimeMillis() % 2 == 0) Integer.valueOf(1) else java.lang.Double.valueOf(2)`<br>`}`|这个, 编译器选择 Integer 和 Double 的超类 java.lang.Number (实际上并非如此)|
|`def x(i:Int):Int = if (i==0) 1 else i*x(i-1)`|递归方法需要显示的返回类型声明. 编译器没法进行推断.|
|  <h2 id="collections">Scala 集合</h2>                                                                       |                 |
|`1 to 3, Set(1,2,3), Buffer(1,2,3), ArrayBuffer(1,2,3), ListBuffer(1,2,3), List(1,2,3), Array(1,2,3),Vector(1,2,3), Map(1 -> "a", 2 -> "b")`|simple collection creations. scala has mutable and immutable collections.|
|`1 :: 2 :: 3 :: Nil`|In addition to that, Lists have an alternative syntax|
|`1 #:: 2 #:: 3 #:: Stream.empty`|Streams also save an alternative syntax|
|prepend, append, union, remove, insertAll... |the usual methods every collection framework offers are present in scala as well|
|if you like to use operators instead, there are some scary but concise ones. you'll need some practice to get them right:<br>+,++,++=,++:=-,--,--=,:+,:++,:=+,+=:,:++=,++:=, ++=:|method name rules:<br>"+" means add<br>"-" means remove<br>"++" or "--" mean add/remove many elements, not just one<br>"=" either means modify mutable collection or assign new immutable collection to var. in the reassign case, "=" is appended to the actual method name, just like "int i=0;i+=1" in java. <br>":" goes on the side of the target collection and is always the first or last character of a method name. if a method end with :=, the method actually ends with : and = means it's a reassignment<br>if method contains ":" it is an add to an ordered collection, either at the beginning or the end of the collection|
|`mutableColl += elem`|add element to a collection|
|`mutableColl -= elem`|remove element|
|`mutableColl ++= elems`|add elements|
|`mutableColl --= elems`|remove elements|
|`elem +=: mutableColl`|adds element at the beginning of a collection|
|`mutableColl :+= elem`|adds element at the end of a collection|
|`mutableColl(0) = 1`|write access by index on mutable collections|
|`coll(0)`|read access by index|
|`coll - elem`|create new collection that has all elements of coll except elem|
|`coll ++ elems`|create new collection that has all elements of coll and elems|
|`coll -- elems`|create new collection that has all elements of coll except elems|
|`coll :+ elem`|create new collection that has all elements of coll and elem at the end|
|`elem +: coll`|create new collection that has all elements of coll and elem at the beginning|
|`immutableColl += elem`<br>`immutableColl -= elem`<br>`immutableColl ++= elems`<br>`immutableColl --= elems`<br>`elem +=: immutableColl`<br>`immutableColl :+= elem`|same as the operations without "=", but works only if "immutableColl is a var, not a val. the created collection is assigned to "immutableColl".|
|`def isEven(i:Int= if (i%2==0) true else false`<br>`val evenNumbers:List[Int] = List(1,2,3,4).filter(isEven)`|scala collections are a major epic win. they have ~100 methods which operate on the data of a collection and there is *absolutely nothing* you cannot do with them.|
|`hashmap.getOrElseUpdate(key, methodThatCreatesTheValueInCaseItDoesNotExist)`|admit it, you wanted to do this in java for at least a decade|
|`val evenNumbers:List[Int] = List(1,2,3,4).filter((i:Int)=> i%2==0)`|same as above, just shorter|
|`val evenNumbers = List(1,2,3,4).filter(i => i%2==0)`|same as above, just shorter. you can skip the () if there is only one parameter. you can also skip the type of the parameter(s) because it can be inferred from the usage|
|`val evenNumbers = List(1,2,3,4).filter(_ % 2 == 0)`|same as above, just shorter. you can skip part before "=>" if you use a parameter only once and replace the parameter usage by "_"|
|`val doubleNumbers = List(1,2,3,4).map(_ * 2)`|for the non functional programmers: map means convert|
|`listOfManyPersons.filter(_.hasChildren).map(_.getChildren)`|collection operations can be chained. you can do anything without loops and conditions which makes your code very easy to read|
|`List(1,2,3,4,5).foreach(println)`|do something with every element|
|`List(1,2,3,4,5).par.filter(_ % 2 == 0)`|is executed in parallel just like that|
|`List(1).toSet.toArray.toBuffer.iterator.toStream.toSeq`|conversions are easy|
|`Iterator.continually(randomNumber)`|collections and iterators can also be created from functions and methods|
|`Iterator.continually(randomNumber).take(100).max`|highest of 100 random numbers. again: there are methods for everything you can possibly imagine. many are taking functions so the flexibility is epic :)|
|`Iterator.continually(randomThings).take(100).maxBy(comparisonFunction)`|highest of 100 random things. as above, but can be used for anything.|
| <h2 id="fold">集合与函数的力量</h2>|
| using closures, it is possible to avoid repetitions of boilerplate - instead you pass a function to a method that hides the boilerplate. apart from filter and map, two other epic wins are reduce and fold.|
|`List(1,2,3,4,5).reduce((i,i2) => i+i2)`|result: ((((1+2)+3)+4)+5). in human speech, it takes 2 elements and merges them into one. imagine the collection turning from 1,2,3,4,5 into 3,3,4,5. then repeat:6,4,5 -> 10,5 -> 15|
|`List(1,2,3,4,5).reduce(_ + _)`|same as above, using "_" for the first and second parameter|
|`List(1,2,3,4,5).fold(0)((sumSoFar,element) => sumSoFar+element)`|same as above, but fold uses an explicit start value|
|`List(1,2,3,4,5).fold(0)(_ + _)`|same as the fold above, just shorter|
|`"comma separated numbers: " + List(1, 2, 3, 4, 5).fold("")(_ + ", " + _)`|finally, you won't have to fiddle around with the last "," anymore!|
|in java this would all look like:<br>`Acc acc = ?;`<br>` for (T t: coll) {if (acc==null) {acc = t;} else {acc = doStuff(acc,t);}}`|this is boilerplate code you can avoid *every single time!*. write only what (doStuff) should happen, not "what and how" (boilerplate code + doStuff).|
|where else could you save boilerplate? think about it!<br>try-catch-finally. define your error handling once, and just inject your logic there. no need to copy & paste your try-catch blocks anywhere
| <h2 id="generics">泛型</h2>|
| `def foo[BAR](bar:BAR):BAR = bar`|simple type parameter, can be anything|
| `def foo[BAR <: java.lang.Number](bar: BAR) = bar.doubleValue() + 5`|upper bound, BAR must be a java.lang.Number or a subclass of it|
| `def foo[BAR >: java.lang.Number](bar: BAR) = bar`|lower bound, type must be java.lang.Number or a superclass of it, but not a subclass of java.lang.Number. note that you can still pass a double, but the type parameter and therefore the return type will be java.lang.Number. the bound applies to the type parameter itself, not the type of the parameter that is passed to the function|
|`val strings:List[String] = List("hello","generics")`<br>`val objects:List[java.lang.Object] = strings`|in scala, type parameters of collections are covariant. this means they "inherit" the inhertance relations of their type parameters. in java, have to do an ugly cast:<br>`List<Integer> ints = new ArrayList<Integer>()`;<br>`List<Number> numbers = ((List<Number)((Object)Integer)`|
|`class InVariant[A]`|class having an invariant type parameter, meaning `val InVariant[SuperClass] = inVariantWithAnyOther` does not compile|
|`class CoVariant[+A]`|class having a covariant type parameter, meaning `val CoVariant[SuperClass] = coVariantWithSubClass` compiles|
|`class ContraVariant[-A]`|class having a contravariant type parameter, meaning `val ContraVariant[SubClass] = contraVariantWithSuperclass` compiles|
|where does "-Type" make sense? take a look at functions:<br>`val func:(String) => java.lang.Number`|the return type is +, the parameter is -, so the function can be replaced by one declared like<br>`val func2:(java.lang.Object) => java.lang.Integer`<br>Think about it. a function that can take any object can also take a string. a function that returns an integer automatically returns a number, so the second one can replace the first one in every possible case.|
|`def foo[A, B[A]] (nested: B[A])`|nested type parameters are possible. nested must be of a type that has a type parameter. for example, you could pass a List[Int]|
|`def foo[A, B, C <: B[A]](nested: C))`|same as above, using an alias for B[A] named C|
|`def foo[C <: Traversable[_]] (nested: C) = nested.head`|if there is no need to access the inner type explicitly, it can be replaced by an _. in this example, the compiler infers that the return type must be whatever _ is, so the actual return type depends on the call site.|
|`foo(List(5))`|call to the method above, returns an Int|
|`def foo[A: Manifest] {val classAtRuntime = manifest[A].erasure; println(classAtRuntime);}`|Adding ":Manifest" will make the compiler add magic so you can get the type parameter at runtime via `manifest[TYPEPARAM]`|
|`foo[String]`|call to method above, prints "class java.lang.String"|
|`def foo[A <: Bar with Serializable with Foobar]`|A must be a subtype of Bar, implement Serializable and also Foobar at the same time|
|<h2 id="option">Option 即 "类型安全的避免NullPointerException"</h2>
|`def neverReturnsNull:Option[Foo] = ....`|in scala, you can use the type system to tell the caller of a method whether or not "null" is a valid return or parameter value. the way scala offers is "Option". you can follow a simple convention: if a parameter or return type can be null, wrap it in an Option instead.|
|`if (neverReturnsNull.isEmpty) fail(); else success(neverReturnsNull.get);`|this forces the caller to check for null explicitly.|
|`val modified = neverReturnsNull.map(notNullInHere => doStuffAndReturnNewResult(notNullInHere)`|you can use options like collections. the conversion/mapping function is applied to the contained value if there is one.|
|example:<br>`val maybeString:Option[String] = ....`<br>`val mayBeNumber:Option[Int] = maybeString.map(Integer.parseInt)`|this is perfectly safe (let's assume that the string can be parsed, we ignore exceptions here). if there was a string, there now is a number. if there was an empty option, we still have that empty option. (all empty options are actually the same instance)|
|`val emptyOption:Option[Foo] = None`|None is our empty option singleton. by type system magic, it is a subclass of any option and can therefore replace any option.|
|`val filledOption:Option[Foo] = Some(new Foo)`|Some(x) creates an option around x. if you pass null, None is returned|
|`val unsafelyAccessed = option.get`|the compiler does not force you to check if an option is filled|
|`val safelyAccessed = option.getOrElse(bar)`|gets the content of the option or "bar" if the option is empty|
|`val firstNonEmptyOption = option.orElse(option2).orElse(option3)`|no need for if-else|
|<h2 id="objects">Objects-对象</h2>|
|`object Foo {val bar = "hello"}`|declared like a class, but "simply exists", similar to "static" in java. however, it is a real singleton so it can be passed around as an object.|
|`Foo.bar`|field access and method callswork like "Foo" is a val pointing at the object instance. or in java terms "just like static stuff"|
|`object Foo {def apply(s:String) = Integer.parseInt(s)}`|the apply-shortcut works everywhere, Foo("5") becomes Foo.apply("5"). remember Some(x)? it was the same here.
|`class Foo;object Foo`|this is possible. the object Foo is then called a companion object.|
|`object Foo {def apply(i:Int) = new Foo(i+1)}`|you can use methods in the object Foo as factory methods to keep the actual constructor nice and clean|
|`def apply[A](x: A): Option[A] = if (x == null) None else Some(x)`|this is what happens when you call Some(x)|
|<h2 id="Tuples">Tuples-元组</h2>|
|`val x:(Int, Int) = (1,2)`|Sometimes, you want to group things together and pass them around as one object, but creating a class for that would be overkill|
|`x._1`|access the first element of a tuple|
|`def takesTuple(p:(Int, String) {...}`|method accepting a tuple|
|`takesTuple(5,"hello)`|(5, "hello") could be 2 parameters or a tuple. the compiler is smart enough to figure out you meant the tuple one because it looks at the type signature of "takesTuple".|
|`takesTuple((5,"hello))`|same as above, but explicitly creating the tuple|
|`coll zip coll2`|creates a single collection which contains tuples which contain values from coll and coll2, matching by index. for example `List(1,2) zip (List("a","b")` becomes `List((1,"a"),(2,"b"))`
|<h2 id="pattermatching">模式匹配</h2>|
|`x match {`|scala has a very powerful switch|
|`case "hello" => {...}`|gets executed if x equals "hello". is tested via equals
|`case s:String => {...}`|gets executed if x is any string. s then exists in the code block as a val.
|`case i:Int if i < 10 => {...}`|gets executed if x is any Int < 10. i then exists in the code block as a val.
|`case 11 *pipe* 12 *pipe* 13 => {...}`|matches on more than one value. how to escape the pipe character in here?|
|`case _ => `|default case, always matches|
|`}`| these were just the boring cases :)|
|`x match {`|scala can also match on values *inside* x|
|`case Some(e) => {...}`|matches of x is a Some. e then exists as a val inside the following code block|
|`case List(_, _, 3, y, z) if z == 5 => {...}`|matches if x is a list of size 5 which has a 3 at index 3 and if the fifth element is 5. the fourth element of the list then exists as "y" inside the code block, the last one does as "z".|
|`case _ :: _ :: 3 :: y :: z :: Nil if z == 5 => {...}`|same as above. note: the compiler will refuse to compile matches if they are obviously nonsense. for example x must not be something that cannot be a list for this case to compile. try `5 match {case List(5) => ...}`|
|`}`|how the hell did that work? see below|
|<h2 id="unapply">Destructuring-解构</h2>|
|`object Foo { def unapply(i: Int) = if (i%2==2) Some("even") else None`|we need the reverse of an apply method - unapply.|
|`val Foo(infoString) = 42`|here, 42 is passed to unapply. if the result is an option, it's value is now stored in "infoString"|
|`42 match {case Foo(infoText) => {...}}`|if some is returned, the match is considered positive and the options value is bound to a val|
|`42 match {case Foo("even") => {...}}`|or it is tested for equality against a given value or instance|
|`41 match {case Digits(a,b) if (a == 4) => {...}}`|if the returned option contains a tuple instead of a single value, the expected happens: we now can access all the tuples fields|
|one more thing:<br> `unapplySeq(foo:Foo):Option[Seq[Bar]]`|like unapply, but offers special features for matching on sequences|
|`val List(a,b@_*) = List("hello","scala","world")`|a = "hello", b = List("scala", "world")|
|`val a::tl = List("hello", "scala", "world"`|same as above using alternative list syntax|
|`val List(a,_*) = List("hello","scala","world")`|sams as above, but discards the last 2 elements, b does not exist|
|Note: for case classes, an unapply method is automatically generated|
|<h2 id="traits">Traits-特性</h2>|
|`trait Foo {`|Like a java interface, but more powerful. you can:|
|`def getBar():Bar`|define abstract methods like in a java interface|
|`def predefinedMethod(s:String) = "hello world"`|add non-abstract methods as well. a good example is the Ordered-trait. is expects you to implement a compare-method and delivers 4 other methods (<, >, <=, >=) which already come with an implementation based on compare|
|`val someVal = "someString"`|traits can also contain fields|
|`}`| but that's not all!|
|`trait Plugin extends java.lang.Object {`|a trait can "extend" any existing class or trait. the difference to the trait Foo above is that our Plugin here is restricted to classes which it extends. in this case java.lang.Object. why might such a thing be useful?|
|`override int hashcode = {....}`<br>`override int equals(other:Object) = {....}`|you can override a method of the class the trait extends. you can selectively replace or extend implementations of existing methods by adding traits. this way, you can say "these 5 classes should use *this* implementation of method foo" instead of manually overriding the method in each class|
|`override boolean add(t:T) = {`<br>`println(t +" is about to be added to the collection")`<br>`super.add(t)`<br>`}`|this is useful for stuff like logging. you can also use this to add features to specific classes. for example, there is a MultiMap-trait in scala which can be attached to maps having sets as values. it adds a addBinding- and removeBinding-methods that are based on the original put/remove-methods and handle the details| 
|`}`|these traits are called mixins. a class can have more than one mixin, and they can also override each other. from inside the trait, "super" refers to whatever they extend.|
|`new Foo extends FirstTrait with BarTrait with SomeOtherTrait`|create a new instance of foo implementing 3 traits. the first one is extended, the next n are "withed".|
|`class Foo extends BarTrait`|declaring a class which implements Bar directly|
|`class Foo extends BarTrait with Serializable`|first extends, then with|
|`class A extends B with C with D with E`|inside E, super refers to D. inside D, super refers to C, and so on. keep this in mind when overriding the same method with different traits which call they supers.|
|  <h2 id="packages">包</h2>                                                                         |                 |
|  `import scala.collection._`                                                                             |  wildcard import. |
|  `import scala.collection.Vector` <br> `import scala.collection.{Vector, Sequence}`                      |  selective import. |
|  `import scala.collection.{Vector => Vec28}`                                                             |  renaming import. |
|  `import java.util.{Date => _, _}`                                                                       |  import all from java.util except Date. |
|  `package pkg` _at start of file_ <br> `package pkg { ... }`                                             |  declare a package. |
|  <h2 id="control_constructs">control constructs</h2>                                                     |                 |
|  `if (check) happy else sad`                                                                             |  conditional. |
|  `if (check) happy` _same as_ <br> `if (check) happy else ()`                                            |  conditional sugar. |
|  `while (x < 5) { println(x); x += 1}`                                                                   |  while loop. |
|  `do { println(x); x += 1} while (x < 5)`                                                                |  do while loop. |
|  `import scala.util.control.Breaks._`<br>`breakable {`<br>`    for (x <- xs) {`<br>`        if (Math.random < 0.1) break`<br>`    }`<br>`}`|  break. ([slides](http://www.slideshare.net/Odersky/fosdem-2009-1013261/21)) |
|  `for (i <- 1 to 10 {doStuffWith(i)}`|most basic "for loop". actually it's not a loop but the compiler turns it into `1 to 10 foreach {i => *block content*}. and it is not called for loop, but for comprehension. it's a higher level construct - loops are low level.|
|  `for (i <- 1 to 10 if i % 2 == 0) {}`|conditions are possible|
|  `for (i <- 1 to 10;i2 <- 10 to 10) {println(i+i2)}`|no need for nested fors|
|  `val evenNumbers = for (i <- 1 to 10) yield (i*2)`|yield means "the loop should return that". in this case, you get all even numbers from 2 to 20|
|  `for (x <- xs if x%2 == 0) yield x*10` _same as_ <br>`xs.filter(_%2 == 0).map(_*10)`                    |  what you write, and what the compiler does |
|  `for ((x,y) <- xs zip ys) yield x*y`              |  pattern matching works in here |
|<h2 id="implicit conversions">Implicits-隐含</h2>|
|`implicit`|mystic keyword. can be attached to vals, vars, defs and objects and method parameters|
|`def parseInt(s:String) = Integer.parseInt(s)`|simple string -> int method|
|`implicit def parseInt(s:String) = Integer.parseInt(s)`|implicit string -> int method. it still can be used as a normal method, but it has one special feature|
|`val i:Int = "42"`|if there is an implicit method in scope which can convert a "wrong" type (string 42) into a "correct" one, the compiler automatically uses it. the code becomes `val i = parseInt("42")`|
|`implicit def attachMethod(i:Int) = new {def tenTimes = i*10}`|another implicit conversion from "Int" to an anonymous class having a method "tenTimes". the returnes class doesn't need a name.|
|using several implicit conversions attaching methods in a chain, combined with scala's .-inference, you can create code that looks like a text that acually makes sense to a human. for a great example, take a look at ScalaTest|
|`val i:Int = 42`<br>`println(i.tenTimes)`|if there is an implicit method which can convert a type not having a method into one having that method - the compiler uses that conversion to make your code valid. this is a common pattern to attach methods to objects| 
|`abstract class GenericAddition[T] {def add(t:T,t2:T):T}`<br>`implicit object IntAddition extends GenericAddition[Int] {def add(t:Int, t2:Int) = t+t2}`|an implicit object|
|`def addTwoThings[T](t:T, t2:T)(implicit logicToUse:GenericAddition[T]) = logicToUse.add(t,t2)`|method using a second implicit parameter list|
|`addTwoThings(1,2)(IntAddition)`|call to method above. so far, no magic involved.|
|`addTwoThings(1,2)`|if a matching implicit object is in scope, it will automatically be used. you don't have to pass it yourself. it's a nice feature to separate the "what" from "how" and let the compiler pick the "how"-logic automatically.|
|<h2 id="types">抽象类型</h2>|
|`type Str = String`|simple alias. Str and String are equal for the compiler|
|`type Complex[A] = (String,String,String,A)`|if you are too lazy to write a complex type expression because a part of the expression is constant, use this. "Complex" is called a type contructor, and A is its parameter. you can declare something as Complex[Int] and it is considered equal to any Tuple having the type (String, String, String, Int)|
|`type StructualType = {def x:String;val y:Int}`|this is a structural type. is allows type safe duck-typing. if you declare a method that accepts "StructuralType", is accepts all objects that have a method x:String and a field y:Int. at runtime, reflection is used for this, but you cannot mess it up at compile time like in dynamically types languages.| 
|`class X {type Y <: Foo}`|types can be clared in classes and traits and overridden in subclasses. upper and lower bounds apply here, too.|
|`type X = {type U = Foo}`|types can be nested. this is useful for things that are too big to explain here, but i can tease you a bit: you can declare a type that "is an int but with an attached subtype". at runtime, everything is still an int, but at compile time, userids, inches, the number of hairs - everything would have a different type and you would get compile errors if you tried to mix them. for strings, you could tag them with an "is resource key", "is already resolved" or "is already html-escaped"-type to avoid doubly resolved or escaped strings. for details, see http://etorreborre.blogspot.com/2011/11/practical-uses-for-unboxed-tagged-types.html
|`this.type`|this refers to the type "this" has. this is pretty useless as long as you are inside "this". but if you use it as a return type, it get's updated once you use a subclass. simple use case: `def cloneOfMe:this.type`. you'll want a subclass to return it's own type, not the parent type.|
|<h2 id="freeeeeeedom">致java程序员: 升腾限定 / 重要差异</h2>|
|If you are coming from java, you might have gotten used to several restrictions and might not even try to do it in scala because you expect it not to work. here is what does work in scala, but not in java|
|`a == b`|this is a null safe call to equals. if you want a check by reference, use "eq" instead of "=="|
|`var x = 0`<br>`1 to 10 foreach {i => {`<br>`println("changing x from inside another class")`<br>`x += i`<br>`}`|in java, you cannot access local variables from anonymous classes unless they are final. in scala, you can.|
|`class Foo {def x = "a"}`<br>`class Bar extends Foo {override val x = "b"}`|parameterless methods can be overridden by readonly fields|
|`def allExceptionsAreEqual = throw new CheckedException`|in scala, you don't need to declare checked exceptions in your method signature. by default, the compiler handles them just like runtimeexceptions. the common case is that a caller doesn't care about exceptions and just lets them propagate up to the top level where they are logged and handled.|
|`List[Int]`|primitives are possible as generic types. no need to use java.lang.Integer|