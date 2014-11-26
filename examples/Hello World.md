Actors Hello World
------
Akka 库可以通过非常简便大通过创建SBT项目添加，仅仅需要做的事编辑工程目录下的
build.sbt文件
> ###SBT是什么?
> Simple Build Tool([SBT](http://www.scala-sbt.org)),Scala，Java或其他基于JVM语言的自动部署工具，类似于maven。接下来我们也将使用它来管理依赖和编译我们的可执行的程序。

####build.sbt
```sbt
name := "actor-test"

version := "1.0"

scalaVersion := "2.10.4"

libraryDependencies ++= Seq(
  "com.typesafe.akka" %% "akka-actor" % "2.3.7"
)
```
<!--more-->
然后，我们创建简单的actor class和调用实例
####src/main/scala/HelloActor.scala
```scala
import akka.actor.{Props, ActorSystem, Actor}
import akka.actor.Actor.Receive

class HelloActor extends Actor {
  override def receive: Receive = {
    case "hello" => println("hello received. hello to you!")
    case _ => println("huh?")
  }
}

object Main extends App {
  val system = ActorSystem("HelloSystem")
  // default Actor constructor
  val helloActor = system.actorOf(Props[HelloActor], name = "helloactor")
  helloActor ! "hello"
  helloActor ! "你好"
  system.shutdown()
}
```
完成之后，我们就可以在工程更目录下打开终端输入**sbt**然后在输入**run**。第一次
输入命令时可能会要编译一会儿。然后就可以看到如下输出：
####Console Output
```bash
[info] Running Main
hello received. hello to you!
huh?
[success] Total time: 7 s, completed 2014-11-26 11:18:51
```
嗯，很简单！
