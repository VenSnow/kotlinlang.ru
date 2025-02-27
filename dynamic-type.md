---
type: doc
layout: reference
category: "Syntax"
title: "Динамические типы Javascript"
url: https://kotlinlang.ru/docs/dynamic-type.html
---

# Динамические типы

> Динамический тип не поддерживается в таргетах для JVM

Являясь статически типизированным языком, Котлин все еще вынужден взаимодействовать с нетипизированными или слаботипизированными средами,
таких как экосистема JavaScript. Для облегчения таких случаев использования на языке доступен динамический тип `dynamic`:

```kotlin

val dyn: dynamic = ...

```

Динамический" тип в основном отключает проверку типов в Kotlin:

  - значение этого типа может быть присвоено любой переменной или передано в качестве параметра в любое место;
  - любое значение может быть присвоено переменной типа `dynamic` или передано функции, которая принимает `dynamic` параметр;
  - Для таких значений отключены `null` проверки.

Наиболее характерной особенностью типа `dynamic` является то, что мы можем вызывать свойство **any** или функцию с любыми параметрами для `dynamic` переменной


```kotlin
dyn.whatever(1, "foo", dyn) // 'whatever' не опеределен
dyn.whatever(*arrayOf(1, 2, 3))
```

В Javascript этот код будет будет скомпилирован как есть `dyn.whatever(1)` в Kotlin станет `dyn.whatever(1)` в Javascript.


When calling functions written in Kotlin on values of `dynamic` type, keep in mind the name mangling performed by the
Kotlin to JavaScript compiler. You may need to use the [@JsName annotation](js-to-kotlin-interop.html#jsname-annotation)
to assign well-defined names to the functions that you need to call.

При вызове функций, написанных в Kotlin со значениями типа `dynamic`, учитывайте, что имя функции может быть искажено при компиляции Kotlin в Javascript. 
Возможно, вам потребуется использовать `@JsName Annotation` чтобы присвоить четко определенные имена функциям, которые необходимо вызвать.

```kotlin
// Module 'kjs'
class Person(val name: String) {
    fun hello() {
        println("Hello $name!")
    }

    @JsName("helloWithGreeting")
    fun hello(greeting: String) {
        println("$greeting $name!")
    }
}
```


Динамический вызов функции всегда вернет `dynamic` как результат выполнения, поэтому вы можете объединять в цепочку такие вызовы:

```kotlin
dyn.foo().bar.baz()
```

Когда мы передаем лямбду (стрелочную функцию) в динамический вызов, то все ее параметры будут иметь `dynamic` тип по умолчанию:

```kotlin
dyn.foo {
    x -> x.bar() // x динамический
}
```

Выражения, использующие значения типа `dynamic` переводятся в Javascript "как есть" и не используют соглашения операторов из Kotlin
Следующие операторы поддерживаются:

* бинарные: `+`, `-`, `*`, `/`, `%`, `>`, `<` `>=`, `<=`, `==`, `!=`, `===`, `!==`, `&&`, `||`
* унарные
    * префикс: `-`, `+`, `!`
    * префикс и постфикс: `++`, `--`
* присваивания: `+=`, `-=`, `*=`, `/=`, `%=`
* доступ по индексу:
    * чтение: `d[a]`, больше одного аргументы вызовет ошибку
    * запись: `d[a1] = a2`, больше одного аргумента в `[]` вызовет ошибку

`in`, `!in` и `..` операторы со значением типа `dynamic` запрещены.

Для получения более подробной информации по динамической типизации вы можете обратится к спецификации [spec document](https://github.com/JetBrains/kotlin/blob/master/spec-docs/dynamic-types.md).
