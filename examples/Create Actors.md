Create Actors
------
####"Hello World"例子
看个简单实例先：
```scala
import akka.actor.Actor
import akka.actor.ActorSystem
import akka.actor.Props

class HelloActor extends Actor {
  def receive = {
    case "hello" => println("hello back at you")
    case _       => println("huh?")
  }
}

object Main extends App {
  val system = ActorSystem("HelloSystem")
  // default Actor constructor
  val helloActor = system.actorOf(Props[HelloActor], name = "helloactor")
  helloActor ! "hello"
  helloActor ! "buenos dias"
  system.shutdown()
}
```
<!--more-->
####定义Actor类
Actors通过继承基类Actor并使实现其receive方法。在receive方法中定义一系列的
case匹配语句来定义将要执行到操作。例如：
```scala
import akka.actor.Actor
import akka.actor.Props
import akka.event.Logging
class MyActor extends Actor {
  val log = Logging(context.system, this)
  def receive = {
    case "test" => log.info("received test")
    case _      => log.info("received unknown message")
  }
}
```
receive返回一个偏函数对象，在actor初始化时存储在其内部，Become／Unbecome
查看更多信息关于怎么改变actor构造函数初始化后的行为。
####Props
Props是创建actors实例的配置类，将它视为创建包含协作信息actor的一个不变量
下面是关于怎么创建Props的例子：
```scala
import akka.actor.Props
val props1 = Props[MyActor]
val props2 = Props(new ActorWithArgs("arg")) // careful, see below
val props3 = Props(classOf[ActorWithArgs], "arg")
```
