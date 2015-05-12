Actors Word Count
----
啥也不说了直接放码：
#####src/main/scala/WordCounterActor.scala
```
object WordCounterActor {
  case object StartProcessFile
}

class WordCounterActor(filename: String) extends Actor {
  private val running = new AtomicBoolean(false)
  private var totalLines = 0
  private var linesProcessed = 0
  private var result = 0
  private var fileSender: Option[ActorRef] = None

  import StringCounterActor._
  import WordCounterActor._

  override def receive: Receive = {
    case StartProcessFile => {
      if(running.getAndSet(true)){
        println("warning: command received, processing file.. ")
      } else {
        fileSender = Some(sender)
        import scala.io.Source._
        fromFile(filename).getLines().foreach{ line =>
          context.actorOf(Props[StringCounterActor]) ! ProcessString(line)
          totalLines += 1
        }
      }
    }
    case StringProcessed(words) => {
      result += words
      linesProcessed +=1
      if(linesProcessed == totalLines) {
        fileSender.map(_ ! result)
      }
    }
    case _ => println("message not recognized.")
  }
}
```

#####src/main/scala/StringCounterActor.scala
```scala
object StringCounterActor {
  case class ProcessString(str: String)
  case class StringProcessed(words: Integer)
}

class StringCounterActor extends Actor{
  import  StringCounterActor._
  def receive ={
    case ProcessString(str) => {
      val wordsInLine = str.split(" ").length
      sender ! StringProcessed(wordsInLine)
    }
    case _ => println("Error: message not recognized.")
  }

}
```
#####src/main/scala/Main.scala
```scala
import WordCounterActor.
import akka.actor.{ActorSystem, Props}
import akka.dispatch.ExecutionContexts._
import akka.pattern.ask
import akka.util.Timeout

import scala.concurrent.duration._

object Main extends App {
  val filename = "test.txt"

  implicit val ec = global

  val system = ActorSystem("System")
  val actor = system.actorOf(Props(new WordCounterActor(filename)))

  implicit val timeout = Timeout(25 seconds)

  val future = actor ? StartProcessFile
  future.map { result => {
    println("Total number of words " + result)
    system.shutdown()
  }}

}
```

<script src="http://yandex.st/highlightjs/7.4/highlight.min.js"></script>
<link rel="stylesheet" href="http://yandex.st/highlightjs/7.3/styles/github.min.css">
<script>
  hljs.initHighlightingOnLoad();
</script>