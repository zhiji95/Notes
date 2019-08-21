# Akka
## 1. Akka Model
1. Actor: 
Akka has a thread pool that it shares with actors.
a few threads handles lots of actors by schedule actors for execution

2. Communication:
- Sending a message:
message is enqueued in actor's mailbox.
thread-safe.

- Processing a message
a thread is scheduled to run this actor.
messages are extracted from the mailbox in order.
the thread invokes thr handler on each message.
at some point the actor is unscheduled.

- Guarantees:
only one thread operates on an actor at any time: no locks needed
Message delievry guarantees:
    at most once delivery
    for any sender-receiver pair, the messageorder is maintained
- Example
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
3. Features
- Actor lifecycle: started, stopped, suspended, resumed, restarted
- supervisor strategy: how parent actor acts for child actor's failure
```scala
override def supervisorStrategy: SupervisorStrategy = oneForOneStrategy() {
    case _: RuntimeException => Restart
    case _ => stop
}
```
- configure akka infrastructure: dispatchers, routers, mailboxes
- Schedulers
```scala
import scala.concurent.duration
import system.dispatcher
system.scheduler.scheduleOnce(2 seconds) {
    actor ! "delayed happy birthday!"
}
```
- Akka patterns: FSM + ask pattern 
```scala
import akka.pattern.ask
implicit val timeout = Timeout(3 seconds)
val future = actor ? "question"
// pipe pattern
import akka pattern.pipe
val anotherActor = system.actorOf(Props[SimpleActor], "anotherSimpleActor")
future.mapTo[String].pipeTo(anotherActor)
```

## 2. Akka Remoting
1. Actor model principles:
- Every interaction based on sending messages
- Full actor excapsulation
- No locking
- Message-sending latency
- At most once message delivery
- Message ording maintained per sender/receiver pair

2. The principles hold
- on the same JVM in a parallel application
- locally on multiple JVMs
- in a distributed environment of any scale

3. Location Transparancy
- the real actor can be deployed anywhere
- Location Transparancy: we don't need to care where the object is
- Transparent remoting: we are using the object as if it were local, you assume you have a local object behind the scene that make the communcation remotely

## 3. Akka Clustering
1. Cluster: 
- Composed of member nodes: node = host + port + UID
- on the same JVM
- on multiple JVMs on the same machine
- on a set of machines of any scale

2. Cluster membership
- Convergent gossip protocol
- Phi accrual failure detector - same as Remoting
- No leader election - leader is deterministically chosen

3. Join a Cluster:
- contact seed nodes (initial contact points) in order
- Node is in the "joining" state: wait for gossipe to converge and all nodes in the cluster must acknowledge the new node
- The leader will set the state of the new node to "up"

4. Leaving a cluster
- Safe and quiet way
    - switches its state to "leaving"
    - gossip converges
    - leader sets the state to "exiting"
    - gossip converges
    - leader marks it removed
- the hard way
    - a node becomes unreachable
    - gossip convergence and leader action are not possible
    - must be removed downed manually
    - can also be auto-downed by the leader (do not use this in production)
    ```scala
    val cluster = Cluster.get(system)
    cluster.down(cluster.selfAddress)
    ```
