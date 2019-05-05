# Spark

## 1. Programming Model

### 1. RDD (Resilient Distributed Datasets)

1. RDD is a read-only collection of objects partitioned across a set of machines that can be rebuilt if a partition is lost.
2. RDDs does exist in physical storage, instead can be computed
3. user can control persistence and partitioning.
4. Represented by a Scala object
5. For ways to construct
   1. from a file(Hadoop Distributed File System, HDFS)
   2. Parallelizing a Scala collection (array) (divided into multiple slices and send to multiple nodes)
   3. transforming from and existing RDD (map, reduceByKey)
   4. Changing the persistence of an existing RDD
      1. Cache 
      2. Save 
6. Persist method: Spark keeps persistent RDDs in memory by default but it can spill them to disk if there is not enough RAM. User can also set a persistence priority on each RDD to specify which in-memory data should spill to disk first

### 2. Parallel Operations

1. Reduce: combines dataset elements using an associative function to produce a result at the driver program
2. Collect: sends all elements of the dataset to the driver program
3. foreach: passess each element through a function

### 3. Shared Variables

1. Broadcast variables: wraps the value and ensures that it is only copied to each worker once
2. Accumulators: add-only and only driver can read

### 4. Advantages of RDD Model

1. RDD can only be created through coarse-grained transformations.
2. immutable natures lets a system mitigate slow nodes by running backup copies of slow tasks as in MapReduce. 
3. In bulk operations on RDDs, a runtime can schedule tasks beased on data locality.
4. Degrade gracefully when there is not enough memory. Partitions that do not fit in RAM can be stored on disk and will provide similar performance to current data-parallel systems. 

## 2. Examples:

### 1. Text Search

```scala
val file = spark.textFile("hdfs://...")
val errs = file.filter(_.contains("ERROR"))
val ones = errs.map(_ => 1)
val count = ones.reduce(_+_)
```

### 2. Logistic Regression

```scala
// Read points from a text file and cache them
val points = spark.textFile(...)
                  .map(parsePoint).cache()
// Initialize w to random D-dimensional vector
var w = Vector.random(D)
// Run multiple iterations to update w
for (i <- 1 to ITERATIONS) {
	val grad = spark.accumulator(new Vector(D))
    for (p <- points) { // Runs in parallel
      val s = (1/(1+exp(-p.y*(w dot p.x)))-1)*p.y
      grad += s * p.x
    }
    w -= grad.value
}
```

### 3. Alternating Least Squares

```scala
val Rb = spark.broadcast(R)
for (i <- 1 to ITERATIONS) {
  U = spark.parallelize(0 until u)
           .map(j => updateUser(j, Rb, M))
           .collect()
  M = spark.parallelize(0 until m)
           .map(j => updateUser(j, Rb, U))
           .collect()
}
```
