# IO
## Task 1
Provide your own implementation of a subset of `IO` functionality. 
It should be stack safe: sequencing of `IO`s should not blow the stack. 
Do not bother with asynchronicity, this `IO` should be executed in a synchronous manner.
Do not forget about tests on each method.

Meaning of some functions:
* `*>[B](another: IO[B]): IO[B]` sequences this `IO` with `another` and returns the latter
* `as[B](newValue: => B): IO[B]` replaces inner value of this `IO` with `B`
* `attempt` Materializes any sequenced exceptions into value space, where they may be handled
* `option` replaces failures in this `IO` with None
* `redeem` Returns a new value that transforms the result of the source, given the recover or map functions, which get executed depending on whether the result ends in error or if it is successful
* `redeemWith` same as `redeem`, but for side effectful transformations
* `raiseError` returns failed `IO`
* `raiseUnless` returns `raiseError` when `cond` is `false`, otherwise `IO.unit`
* `raiseWhen` returns `raiseError` when `cond` is `true`, otherwise `IO.unit`
* `unlessA` returns the given argument if `cond` is `false`, otherwise `IO.Unit`
* `whenM` returns the given argument if `cond` is `true`, otherwise `IO.Unit`

To get deeper understanding of each method and gain some insides about implementation refer to:
* https://typelevel.org/cats-effect/docs/concepts
* https://typelevel.org/cats-effect/api/3.x/cats/effect/IO.html

## Task 2
The next task will be a little harder, but we know that you can deal with it.
Focus in this task will be on working with files IO and streams. 

Streams model a continuous flow of data from source to sink with optional transformations along the way.
Main advantage of streams is the ability to continually process big amount of data without total memory consumption 
due to holding only a small chunk of data in memory at a time.

There is a streaming library in scala functional called FS2. 
It is a library for purely functional, effectful, and polymorphic stream processing library. 
It puts forward streams composability, resource safety, speed and side effectful execution by using cats effect as a core.

You should implement the next algorithm:
1. Read from the console the file path. 
   1. Check the transmitted data(Error Handling + Validation). Input should not be empty. Only files of `.txt` extensions supported
2. Read from the console the constant. 
   1. Check the transmitted data(Error Handling + Validation). It should be a positive integer
3. Read the data from the file using `fs2.io.file`
   1. All blank lines and whitespaces should be skipped
   2. All special chars should be filtered out
4. Calculate the signature in parallel with parallelization limit equal to 10. 
   1. Calculate hash for each word using the hash function below (implement it in a purely functional way)
5. Save the minimal hash to the output file with name `s"${original_file_name}_out"` in `out` dir
6. Try to split different stream stages in different business logic units and classes. Test only validation, text cleaning out and hashing stages

```scala
def hash(word: String, constant: Int): Int = {
    var hash = 0
    for (ch <- word.toCharArray)
      hash = ((hash << 5) ^ (hash >> 27)) ^ ch.toInt
    hash % constant
  }
```

As source files you should use `ciceron.txt` and `shakespear.txt` files in root

* fs2 guide https://fs2.io/#/guide
* guide about I/O with fs2 https://fs2.io/#/io

