---
layout: singlepage-overview
title: Туториал по Scala для Java-программистов

partof: scala-for-java-programmers
languages: ru
---

Авторы: Michel Schinz, Philipp Haller.

## Введение

Эта статья является быстрым вводом в язык Scala и его компилятор.
Она предназначена для людей, которые уже имеют некоторый опыт 
программирования и хотят увидеть, что они могут делать на языке Scala. 
Предполагается базовое владение объективно-ориентированным 
программированием, в особенности на Java.

## Первый пример

В качестве первого примера используем стандартную *Hello world* программу. 
Пример не очень увлекательный, но позволяет легко продемонстрировать 
использование инструментов Scala, без особых знаний языка. Вот как 
это выглядит:

    object HelloWorld {
      def main(args: Array[String]): Unit = {
        println("Hello, world!")
      }
    }

Структура этой программы должна быть знакома Java-программистам:
она состоит из одного метода под названием `main`, который принимает 
аргументы командной строки (массив строк в качестве параметра).
Тело этого метода состоит из единственного вызова предопределенного 
метода `println` с дружеским приветствием в качестве аргумента. 
Метод `main` не возвращает значение. Поэтому его возвращаемый тип 
объявлен как `Unit`.

Что менее знакомо программистам на Java, так это `object` объявление, 
содержащее метод `main`. Такое объявление вводит то, что обычно известно 
как *singleton объект*, то есть класс с единственным экземпляром. 
Таким образом, в приведенном выше объявлении описывается как класс под названием 
`HelloWorld`, так и экземпляр этого класса, который также называется `HelloWorld`. 
Этот экземпляр создается по запросу, при первом использовании.

Проницательный читатель мог заметить, что `main` метод - не объявлен 
здесь как `static`. Это потому, что статические члены (методы или поля) 
не существуют в Scala. Вместо определения статических членов, 
программист Scala объявляет их в singleton объекте.

### Компиляция примера

Чтобы скомпилировать пример, нужно использовать `scalac` - компилятор 
языка Scala. `scalac` работает подобно большинству компиляторов: он 
берет исходный файл (файл с исходным кодом) в качестве аргумента, возможно, 
некоторые параметры, и производит один или несколько файлов. 
Файлы, которые он производит, являются стандартными Java-class файлами.

Если мы сохраним программу выше в файле `HelloWorld.scala`, 
мы сможем скомпилировать его используя команду (знак "больше-чем",
`>` представляет собой ввод командной строки и может быть опущен):

    > scalac HelloWorld.scala

Это создаст несколько файлов в текущей директории. Один из них будет 
называться `HelloWorld.class` и будет содержать класс, который может 
быть напрямую запущен используя команду `scala`, как демонстрирует 
следующая секция.

### Запуск примера

После компиляции, программа на языке Scala может быть запущена 
используя команду `scala`. Она очень схожа с `java` - команда, 
используемая для запуска программ на языке Java. Пример выше
может быть запущен используя следующую команду, которая 
создает тривиальный вывод:

    > scala -classpath . HelloWorld

    Hello, world!

## Взаимодействие с Java

Одна из сильных сторон Scala заключается в том, что можно очень 
легко взаимодействовать с кодом написанным на Java. Все классы из 
пакета `java.lang` импортируются по умолчанию, а другие нужно 
импортировать явно.

Давайте посмотрим на демонстрирующий это пример. Мы хотим получить
и отформатировать текущую дату в соответствии с соглашениями, 
используемыми конкретной страной, скажем Францией. (Другие 
регионы, такие как Франкоязычная часть Швейцарии использует те же 
соглашения.)

Библиотеки классов Java определяют мощные служебные классы, такие как
`Date` и `DateFormat`. Поскольку Scala без проблем взаимодействует
с Java, нет необходимости реализовывать эквивалентные классы в библиотеке 
классов Scala - мы можем просто импортировать классы соответствующих 
пакетов Java:

    import java.util.{Date, Locale}
    import java.text.DateFormat._

    object FrenchDate {
      def main(args: Array[String]): Unit = {
        val now = new Date
        val df = getDateInstance(LONG, Locale.FRANCE)
        println(df format now)
      }
    }

Оператор импорта в Scala очень схож с эквивалентным в Java,
однако он более мощный. Несколько классов можно импортировать из
того же пакета, заключив их в фигурные скобки, как на первой строке. 
Другое отличие состоит в том, что при импорте всех имен пакета или 
класса, вместо звездочки (`*`) используется символ подчеркивания (`_`). 
Это потому, что звездочка - действительный идентификатор Scala 
(например, имя метода), как мы увидим позже.

Оператор импорта во второй строке импортирует все элементы класса 
`DateFormat`. Это делает статический метод `getDateInstance` и 
статическое поле `LONG` напрямую видимыми.

Внутри метода `main` мы сначала создаем экземпляр Java класса `Date`, 
который по умолчанию содержит текущую дату. Далее мы определяем формат 
даты с помощью статического метода `getDateInstance`, который мы 
импортировали ранее. Наконец, печатаем текущую дату, отформатированную 
в соответствии с локализованным экземпляром `DateFormat`. Эта последняя 
строка демонстрирует интересное свойство синтаксиса Scala. Методы
использующие один аргумент могут использоваться с инфиксным синтаксисом. 
Это выражение

    df format now

это просто еще один, менее подробный способ написания выражения

    df.format(now)

Это может показаться незначительной синтаксической деталью, но у 
нее есть важные последствия, одно из которых будет исследовано в 
следующем разделе.

В заключение этого раздела об интеграции с Java следует 
отметить, что также возможно наследование от классов Java и 
реализация интерфейсов Java непосредственно в Scala.

## Все есть объектом

Scala чистый объектно-ориентированный язык в том смысле, 
что *все* является объектом, включая числа или функции. В 
этом отношении с Java есть отличия, поскольку Java отделяет
примитивные типы (такие, как `boolean` или `int`) от ссылочных 
типов.

### Числа - это объекты

Поскольку числа являются объектами, у них также есть методы. 
И на самом деле арифметическое выражение, подобное следующему:

    1 + 2 * 3 / x

состоит исключительно из вызовов методов, потому что он эквивалентен 
следующему выражению, продемонстрированном в предыдущем разделе:

    1.+(2.*(3)./(x))

Это также означает, что `+`, `*` и т.д. являются допустимыми 
идентификаторами в Scala.

### Функции - это объекты

Функции также являются объектами в Scala. Следовательно, функции 
возможно передавать как аргументы, хранить в переменных и возвращать 
их из других функций. Возможность манипулировать функциями как значениями 
является одним из краеугольных камней очень интересной парадигмы, которая 
носит название *функциональное программирование*.

В качестве простого примера полезности использования функции в качестве значений,
давайте рассмотрим функцию-таймер, которая направлена на выполнение некоторых 
действий каждые несколько секунд. Как мы укажем ей, какое действие выполнять ? 
Очень логично, функцией. Этот очень простой вид передачи функции должен быть
знаком многим программистам: регистрация call-back функций которые будут вызываться,
когда происходит ошибка очень часто это используется в программировании 
пользовательских интерфейсов.

В этой программе функция-таймер называется `oncePerSecond`, и получает 
callback-функцию как аргумент. Тип этой функции записан как `() => Unit` 
и является типом всех функций, которые ничего не берут в качестве аргумента
и ничего не возвращают (Тип `Unit` схож с типом `void` в языках C/C++). 
Функция `main` этой программы просто вызывает эту функцию-таймер 
с call-back функцией, которая печатает слова в терминал. Другими словами,
эта программа бесконечно пишет "time flies like an arrow" каждую секунду.

    object Timer {
      def oncePerSecond(callback: () => Unit): Unit = {
        while (true) { callback(); Thread sleep 1000 }
      }
      def timeFlies(): Unit = {
        println("time flies like an arrow...")
      }
      def main(args: Array[String]): Unit = {
        oncePerSecond(timeFlies)
      }
    }

Запомните: чтобы напечатать строку мы используем предопределенный метод
`println` вместо использования `System.out`.

#### Анонимные функции

Хотя эту программу легко понять, ее можно немного усовершенствовать.
Прежде всего, обратите внимание, что функция `timeFlies` определена 
только для того, чтобы позже передаваться в функцию `oncePerSecond`.
Необходимость называть эту функцию, которая используется только один 
раз, может показаться ненужной, и на самом деле было бы неплохо иметь 
возможность сконструировать эту функцию так, как она передается в 
`oncePerSecond`. Это возможно в Scala с использованием 
*анонимных функций*, а именно: функций без имени. Обновленная версия 
нашей программы таймера, использующая анонимную функцию вместо 
*timeFlies*, выглядит так:

    object TimerAnonymous {
      def oncePerSecond(callback: () => Unit): Unit = {
        while (true) { callback(); Thread sleep 1000 }
      }
      def main(args: Array[String]): Unit = {
        oncePerSecond(() =>
          println("time flies like an arrow..."))
      }
    }

Наличие анонимной функции в этом примере показано стрелкой вправо `=>`, 
которая отделяет список аргументов функции от ее тела. В этом примере 
список аргументов пуст, о чем свидетельствует пустая пара круглых 
скобок слева от стрелки. Тело функции такое же, как и у `timeFlies` 
выше.

## Классы

Как мы видели выше, Scala - объектно-ориентированный язык, и поэтому 
он имеет понятие класса. (Для полноты картины следует отметить, что
некоторые объектно-ориентированные языки не имеют концепции класса,
но Scala не входит в их число.) Классы в Scala объявляются с
использованием синтаксиса, близкого к синтаксису Java. Одно важное
отличие состоит в том, что классы в Scala могут иметь параметры.
Это показано в следующем определении комплексных чисел.

    class Complex(real: Double, imaginary: Double) {
      def re() = real
      def im() = imaginary
    }

Класс `Complex` принимает два аргумента, которые являются
действительной и мнимой частью комплексного числа. Эти аргументы 
должны быть переданы при создании экземпляра класса `Complex`
следующим образом: `new Complex (1.5, 2.3)`. Класс содержит два 
метода, называемых `re` и `im`, которые предоставляют доступ к
этим двум частям.

Следует отметить, что тип возвращаемого значения этих двух методов
не указывается явно. Он будет автоматически выведен компилятором,
который смотрит на правую часть этих методов и делает вывод, что оба
возвращают значение типа `Double`.

Компилятор не всегда может выводить типы, как здесь, и, к сожалению,
нет простого правила, чтобы точно знать, когда он сможет это сделать.
На практике это обычно не проблема, поскольку компилятор жалуется,
когда не может определить тип, который не был задан явно. Как простое
правило, начинающие программисты на Scala должны попытаться опустить
объявления типов, которые кажутся легко выводимыми из контекста, и
посмотреть, согласен ли компилятор. По прошествии некоторого времени
программист должен хорошо понимать, когда следует опускать типы, а
когда указывать их явно.

### Методы без аргументов

Небольшая проблема методов `re` и `im` в том, что для их вызова нужно 
поставить пустую пару скобок после их имени, как показано в 
следующем примере:

    object ComplexNumbers {
      def main(args: Array[String]): Unit = {
        val c = new Complex(1.2, 3.4)
        println("imaginary part: " + c.im())
      }
    }

Было бы лучше иметь доступ к реальным и мнимым частям, как если бы 
они были полями, без добавления пустой пары скобок. В Scala это 
можно сделать, просто определив их как методы *без аргументов*. 
Такие методы отличаются от методов с нулевыми аргументами тем, что 
у них нет скобок после имени, ни в их определении, ни в их 
использовании. Наш класс `Complex` можно переписать следующим образом:

    class Complex(real: Double, imaginary: Double) {
      def re = real
      def im = imaginary
    }


### Наследование и переопределение

Все классы в Scala наследуются от супер-класса. Если супер-класс не 
указан, как показано в классе `Complex` из предыдущей секции
то будет неявно использован класс `scala.AnyRef`.

В Scala можно переопределять методы унаследованные от супер-класса. 
Однако, обязательно нужно указать что метод переопределяется другим 
используя модификатор `override`, чтобы избежать случайного переопределения.
Например, наш класс `Complex` может быть дополнен с переопределением метода
`toString` унаследованного из класса `Object`.

    class Complex(real: Double, imaginary: Double) {
      def re = real
      def im = imaginary
      override def toString() =
        "" + re + (if (im >= 0) "+" else "") + im + "i"
    }

Мы можем вызвать переопределенный метод `toString` как показано ниже.

    object ComplexNumbers {
      def main(args: Array[String]): Unit = {
        val c = new Complex(1.2, 3.4)
        println("Overridden toString(): " + c.toString)
      }
    }

## Case Classes and Pattern Matching

A kind of data structure that often appears in programs is the tree.
For example, interpreters and compilers usually represent programs
internally as trees; XML documents are trees; and several kinds of
containers are based on trees, like red-black trees.

We will now examine how such trees are represented and manipulated in
Scala through a small calculator program. The aim of this program is
to manipulate very simple arithmetic expressions composed of sums,
integer constants and variables. Two examples of such expressions are
`1+2` and `(x+x)+(7+y)`.

We first have to decide on a representation for such expressions. The
most natural one is the tree, where nodes are operations (here, the
addition) and leaves are values (here constants or variables).

In Java, such a tree would be represented using an abstract
super-class for the trees, and one concrete sub-class per node or
leaf. In a functional programming language, one would use an algebraic
data-type for the same purpose. Scala provides the concept of
*case classes* which is somewhat in between the two. Here is how
they can be used to define the type of the trees for our example:

    abstract class Tree
    case class Sum(l: Tree, r: Tree) extends Tree
    case class Var(n: String) extends Tree
    case class Const(v: Int) extends Tree

The fact that classes `Sum`, `Var` and `Const` are
declared as case classes means that they differ from standard classes
in several respects:

- the `new` keyword is not mandatory to create instances of
  these classes (i.e., one can write `Const(5)` instead of
  `new Const(5)`),
- getter functions are automatically defined for the constructor
  parameters (i.e., it is possible to get the value of the `v`
  constructor parameter of some instance `c` of class
  `Const` just by writing `c.v`),
- default definitions for methods `equals` and
  `hashCode` are provided, which work on the *structure* of
  the instances and not on their identity,
- a default definition for method `toString` is provided, and
  prints the value in a "source form" (e.g., the tree for expression
  `x+1` prints as `Sum(Var(x),Const(1))`),
- instances of these classes can be decomposed through
  *pattern matching* as we will see below.

Now that we have defined the data-type to represent our arithmetic
expressions, we can start defining operations to manipulate them. We
will start with a function to evaluate an expression in some
*environment*. The aim of the environment is to give values to
variables. For example, the expression `x+1` evaluated in an
environment which associates the value `5` to variable `x`, written
`{ x -> 5 }`, gives `6` as result.

We therefore have to find a way to represent environments. We could of
course use some associative data-structure like a hash table, but we
can also directly use functions! An environment is really nothing more
than a function which associates a value to a (variable) name. The
environment `{ x -> 5 }` given above can simply be written as
follows in Scala:

    { case "x" => 5 }

This notation defines a function which, when given the string
`"x"` as argument, returns the integer `5`, and fails with an
exception otherwise.

Before writing the evaluation function, let us give a name to the type
of the environments. We could of course always use the type
`String => Int` for environments, but it simplifies the program
if we introduce a name for this type, and makes future changes easier.
This is accomplished in Scala with the following notation:

    type Environment = String => Int

From then on, the type `Environment` can be used as an alias of
the type of functions from `String` to `Int`.

We can now give the definition of the evaluation function.
Conceptually, it is very simple: the value of a sum of two expressions
is simply the sum of the value of these expressions; the value of a
variable is obtained directly from the environment; and the value of a
constant is the constant itself. Expressing this in Scala is not more
difficult:

    def eval(t: Tree, env: Environment): Int = t match {
      case Sum(l, r) => eval(l, env) + eval(r, env)
      case Var(n)    => env(n)
      case Const(v)  => v
    }

This evaluation function works by performing *pattern matching*
on the tree `t`. Intuitively, the meaning of the above definition
should be clear:

1. it first checks if the tree `t` is a `Sum`, and if it
   is, it binds the left sub-tree to a new variable called `l` and
   the right sub-tree to a variable called `r`, and then proceeds
   with the evaluation of the expression following the arrow; this
   expression can (and does) make use of the variables bound by the
   pattern appearing on the left of the arrow, i.e., `l` and
   `r`,
2. if the first check does not succeed, that is, if the tree is not
   a `Sum`, it goes on and checks if `t` is a `Var`; if
   it is, it binds the name contained in the `Var` node to a
   variable `n` and proceeds with the right-hand expression,
3. if the second check also fails, that is if `t` is neither a
   `Sum` nor a `Var`, it checks if it is a `Const`, and
   if it is, it binds the value contained in the `Const` node to a
   variable `v` and proceeds with the right-hand side,
4. finally, if all checks fail, an exception is raised to signal
   the failure of the pattern matching expression; this could happen
   here only if more sub-classes of `Tree` were declared.

We see that the basic idea of pattern matching is to attempt to match
a value to a series of patterns, and as soon as a pattern matches,
extract and name various parts of the value, to finally evaluate some
code which typically makes use of these named parts.

A seasoned object-oriented programmer might wonder why we did not
define `eval` as a *method* of class `Tree` and its
subclasses. We could have done it actually, since Scala allows method
definitions in case classes just like in normal classes. Deciding
whether to use pattern matching or methods is therefore a matter of
taste, but it also has important implications on extensibility:

- when using methods, it is easy to add a new kind of node as this
  can be done just by defining a sub-class of `Tree` for it; on
  the other hand, adding a new operation to manipulate the tree is
  tedious, as it requires modifications to all sub-classes of
  `Tree`,
- when using pattern matching, the situation is reversed: adding a
  new kind of node requires the modification of all functions which do
  pattern matching on the tree, to take the new node into account; on
  the other hand, adding a new operation is easy, by just defining it
  as an independent function.

To explore pattern matching further, let us define another operation
on arithmetic expressions: symbolic derivation. The reader might
remember the following rules regarding this operation:

1. the derivative of a sum is the sum of the derivatives,
2. the derivative of some variable `v` is one if `v` is the
   variable relative to which the derivation takes place, and zero
   otherwise,
3. the derivative of a constant is zero.

These rules can be translated almost literally into Scala code, to
obtain the following definition:

    def derive(t: Tree, v: String): Tree = t match {
      case Sum(l, r) => Sum(derive(l, v), derive(r, v))
      case Var(n) if (v == n) => Const(1)
      case _ => Const(0)
    }

This function introduces two new concepts related to pattern matching.
First of all, the `case` expression for variables has a
*guard*, an expression following the `if` keyword. This
guard prevents pattern matching from succeeding unless its expression
is true. Here it is used to make sure that we return the constant `1`
only if the name of the variable being derived is the same as the
derivation variable `v`. The second new feature of pattern
matching used here is the *wildcard*, written `_`, which is
a pattern matching any value, without giving it a name.

We did not explore the whole power of pattern matching yet, but we
will stop here in order to keep this document short. We still want to
see how the two functions above perform on a real example. For that
purpose, let's write a simple `main` function which performs
several operations on the expression `(x+x)+(7+y)`: it first computes
its value in the environment `{ x -> 5, y -> 7 }`, then
computes its derivative relative to `x` and then `y`.

    def main(args: Array[String]): Unit = {
      val exp: Tree = Sum(Sum(Var("x"),Var("x")),Sum(Const(7),Var("y")))
      val env: Environment = { case "x" => 5 case "y" => 7 }
      println("Expression: " + exp)
      println("Evaluation with x=5, y=7: " + eval(exp, env))
      println("Derivative relative to x:\n " + derive(exp, "x"))
      println("Derivative relative to y:\n " + derive(exp, "y"))
    }

You will need to wrap the `Environment` type and `eval`, `derive`, and
`main` methods in a `Calc` object before compiling.  Executing this
program, we get the expected output:

    Expression: Sum(Sum(Var(x),Var(x)),Sum(Const(7),Var(y)))
    Evaluation with x=5, y=7: 24
    Derivative relative to x:
     Sum(Sum(Const(1),Const(1)),Sum(Const(0),Const(0)))
    Derivative relative to y:
     Sum(Sum(Const(0),Const(0)),Sum(Const(0),Const(1)))

By examining the output, we see that the result of the derivative
should be simplified before being presented to the user. Defining a
basic simplification function using pattern matching is an interesting
(but surprisingly tricky) problem, left as an exercise for the reader.

## Traits

Apart from inheriting code from a super-class, a Scala class can also
import code from one or several *traits*.

Maybe the easiest way for a Java programmer to understand what traits
are is to view them as interfaces which can also contain code.  In
Scala, when a class inherits from a trait, it implements that trait's
interface, and inherits all the code contained in the trait.

(Note that since Java 8, Java interfaces can also contain code, either
using the `default` keyword, or as static methods.)

To see the usefulness of traits, let's look at a classical example:
ordered objects. It is often useful to be able to compare objects of a
given class among themselves, for example to sort them. In Java,
objects which are comparable implement the `Comparable`
interface. In Scala, we can do a bit better than in Java by defining
our equivalent of `Comparable` as a trait, which we will call
`Ord`.

When comparing objects, six different predicates can be useful:
smaller, smaller or equal, equal, not equal, greater or equal, and
greater. However, defining all of them is fastidious, especially since
four out of these six can be expressed using the remaining two. That
is, given the equal and smaller predicates (for example), one can
express the other ones. In Scala, all these observations can be
nicely captured by the following trait declaration:

    trait Ord {
      def < (that: Any): Boolean
      def <=(that: Any): Boolean =  (this < that) || (this == that)
      def > (that: Any): Boolean = !(this <= that)
      def >=(that: Any): Boolean = !(this < that)
    }

This definition both creates a new type called `Ord`, which
plays the same role as Java's `Comparable` interface, and
default implementations of three predicates in terms of a fourth,
abstract one. The predicates for equality and inequality do not appear
here since they are by default present in all objects.

The type `Any` which is used above is the type which is a
super-type of all other types in Scala. It can be seen as a more
general version of Java's `Object` type, since it is also a
super-type of basic types like `Int`, `Float`, etc.

To make objects of a class comparable, it is therefore sufficient to
define the predicates which test equality and inferiority, and mix in
the `Ord` class above. As an example, let's define a
`Date` class representing dates in the Gregorian calendar. Such
dates are composed of a day, a month and a year, which we will all
represent as integers. We therefore start the definition of the
`Date` class as follows:

    class Date(y: Int, m: Int, d: Int) extends Ord {
      def year = y
      def month = m
      def day = d
      override def toString(): String = year + "-" + month + "-" + day

The important part here is the `extends Ord` declaration which
follows the class name and parameters. It declares that the
`Date` class inherits from the `Ord` trait.

Then, we redefine the `equals` method, inherited from
`Object`, so that it correctly compares dates by comparing their
individual fields. The default implementation of `equals` is not
usable, because as in Java it compares objects physically. We arrive
at the following definition:

    override def equals(that: Any): Boolean =
      that.isInstanceOf[Date] && {
        val o = that.asInstanceOf[Date]
        o.day == day && o.month == month && o.year == year
      }

This method makes use of the predefined methods `isInstanceOf`
and `asInstanceOf`. The first one, `isInstanceOf`,
corresponds to Java's `instanceof` operator, and returns true
if and only if the object on which it is applied is an instance of the
given type. The second one, `asInstanceOf`, corresponds to
Java's cast operator: if the object is an instance of the given type,
it is viewed as such, otherwise a `ClassCastException` is
thrown.

Finally, the last method to define is the predicate which tests for
inferiority, as follows. It makes use of another method,
`error` from the package object `scala.sys`, which throws an exception with the given error message.

    def <(that: Any): Boolean = {
      if (!that.isInstanceOf[Date])
        sys.error("cannot compare " + that + " and a Date")

      val o = that.asInstanceOf[Date]
      (year < o.year) ||
      (year == o.year && (month < o.month ||
                         (month == o.month && day < o.day)))
    }

This completes the definition of the `Date` class. Instances of
this class can be seen either as dates or as comparable objects.
Moreover, they all define the six comparison predicates mentioned
above: `equals` and `<` because they appear directly in
the definition of the `Date` class, and the others because they
are inherited from the `Ord` trait.

Traits are useful in other situations than the one shown here, of
course, but discussing their applications in length is outside the
scope of this document.

## Genericity

The last characteristic of Scala we will explore in this tutorial is
genericity. Java programmers should be well aware of the problems
posed by the lack of genericity in their language, a shortcoming which
is addressed in Java 1.5.

Genericity is the ability to write code parametrized by types. For
example, a programmer writing a library for linked lists faces the
problem of deciding which type to give to the elements of the list.
Since this list is meant to be used in many different contexts, it is
not possible to decide that the type of the elements has to be, say,
`Int`. This would be completely arbitrary and overly
restrictive.

Java programmers resort to using `Object`, which is the
super-type of all objects. This solution is however far from being
ideal, since it doesn't work for basic types (`int`,
`long`, `float`, etc.) and it implies that a lot of
dynamic type casts have to be inserted by the programmer.

Scala makes it possible to define generic classes (and methods) to
solve this problem. Let us examine this with an example of the
simplest container class possible: a reference, which can either be
empty or point to an object of some type.

    class Reference[T] {
      private var contents: T = _
      def set(value: T) { contents = value }
      def get: T = contents
    }

The class `Reference` is parametrized by a type, called `T`,
which is the type of its element. This type is used in the body of the
class as the type of the `contents` variable, the argument of
the `set` method, and the return type of the `get` method.

The above code sample introduces variables in Scala, which should not
require further explanations. It is however interesting to see that
the initial value given to that variable is `_`, which represents
a default value. This default value is 0 for numeric types,
`false` for the `Boolean` type, `()` for the `Unit`
type and `null` for all object types.

To use this `Reference` class, one needs to specify which type to use
for the type parameter `T`, that is the type of the element
contained by the cell. For example, to create and use a cell holding
an integer, one could write the following:

    object IntegerReference {
      def main(args: Array[String]): Unit = {
        val cell = new Reference[Int]
        cell.set(13)
        println("Reference contains the half of " + (cell.get * 2))
      }
    }

As can be seen in that example, it is not necessary to cast the value
returned by the `get` method before using it as an integer. It
is also not possible to store anything but an integer in that
particular cell, since it was declared as holding an integer.

## Conclusion

This document gave a quick overview of the Scala language and
presented some basic examples. The interested reader can go on, for example, by
reading the *[Tour of Scala](https://docs.scala-lang.org/tour/tour-of-scala.html)*, which
contains more explanations and examples, and consult the *Scala
  Language Specification* when needed.
