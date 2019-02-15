+++
title = "scala pattern match on Array and List"
author = ["jcchen"]
date = 2019-02-15T15:14:00+08:00
tags = ["scala"]
draft = false
+++

The following shows you how to use scala pattern match on Array and List.

<!--more-->

Pattern match on Array.

```scala
val arr = Array(1,2,3,4,5)
arr match {
  case Array() => println("empty array")
  case Array(head, _*) => println(head)
}

arr match {
  case Array() => println("empty array")
  case Array(_, tail @ _*) => println(tail.mkString(","))
}
```

Pattern match on List.

```scala
val list = List(1,2,3,4,5)
list match {
  case Nil => println("empty list")
  case head::tail => println(s"head:$head, tail:${tail.mkString(",")}")
}
```
