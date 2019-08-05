# Akka
## 1. Akka Model
- Actor: 
Akka has a thread pool that it shares with actors.
a few threads handles lots of actors by schedule actors for execution

##### Communication:
1. Seding a message:
message is enqueued in actor's mailbox.
thread-safe.

2. Processing a message
a thread is scheduled to run this actor.
messages are extracted from the mailbox in order.
the thread invokes thr handler on each message.
at some point the actor is unscheduled.

3. Guarantees:
only one thread operates on an actor at any time: no locks needed
Message delievry guarantees:
    at most once delivery
    for any sender-receiver pair, the messageorder is maintained

```scala
// Have an actor systems
object ActorsIntro extends App {
    val actorSystem = ActorSystem("firstActorSystem") 
    //a data structure that controls a number of threads under the hood, then allocate to running actors
    pirntln(actorSystem.name)
}

// Create actors
class WordCountActor extends Actor {
    var totalWords = 0;
    // behavior
    def receive: PartialFunction[Any, Unit] = {
        case message: String => totalWords += message.split(" ").length
        case msg => println("[word counter] I cannot undertstand %{msg.toString}")
    }
}

/// Instantiate actor
val wordCounter = actorSystem.actorOf(Props[WordCountActor], "wordCounter")
// ActorRef: a data structure that akk expose to you, you can only communicate this actor using the actorRef.

val anotherWordCounter = actorSystem.actorOf(Props[WordCountActor], "anotherWordCounter")

// Communicate (asynchronous)
wordCounter.!("I am learning Akka and it is pretty damn cool")
wordCounter.!("A different mesaage")



class Person(name: String) extends Actor {
    override def receive: Receive = {
        case "hi" => println(s"Hi, my name is $name")
        case _ =>
    }
}

object Person {
    def props(name: String) = Props(new Person(name))
}

val person = actorSystem.actorOf(Person.props("Bob"))
person ! "hi"

```

ordering of messages?
aren't we causing race conditions?
what does "asynchronous actually mean?"
