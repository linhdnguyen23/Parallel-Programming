# Parallel Programming

Contains class materials submitted by Linh Nguyen for Rice University's Parallel
Programming course
## Concepts
### Critical Path
Longest path in your system/program.

### Speedup
If `x` is the time it takes for your program to run sequentially and `y` is the
time it takes when parts of your program has been made parallel, then the
speedup of your parallel version is `x/y`.

### Amdahl's Law
If `q` is the length of the critical path in your program, then the upper bound
for speedup is `1/q`

### Parallel Overhead
As observed in the mini projects, making parallel tasks sometimes make the
program slower. Creating parallel tasks has overhead, so for small N where N is
the size of the input data, the overhead is critical to the overall runtime.

### Functional Programming
Functional programming translate nicely into parallel programming. Suppose we
have A = f(X), X = g(Y), and C = h(X). A and B can run in parallel. If both
parallel tasks have read and write access to a X, it may create data race
conditition. Therefore, we want to create an async future of X, FX = g(Y), and pass it
to f and h => A = f(FA.get()) and B = h(FA.get()). The moment f or h needs to
access X, FA would block X so the caller and read or write to it. X wouldn't be
available for access to any other tasks.

1. A future task extends the RecursiveTask class in the FJ framework, instead of 
RecursiveAction as in regular tasks.
2. compute() method of classes that extend RecursiveTask must return non-void
type where as those that extends RecursiveAction return void.

### Data Race and Determinism
If a system achieves functional and structural determinism, there is no data
race.
* Functional determinism: always give same output given the same input
* Structural determinism: always have same computational graph given the same input
Example of structural non-determinism: a search of trending articles may return
different sets of articles for each user.

### Memoization
Memoization can optimize your program by avoiding redoing tasks.

### Parallel Loops
Some sequential loops can be made parallel although the loop hierarchy may change; 
nested loops become outer loops and vice versa. Look at loop invariant to determine 
which loops to bring out. It's best to have few parallel loops since creating a large 
amount of parallel tasks is 'expensive'. Loop chunking increases the work
 on each loop but reduces the number of tasks.
 
### Barrier
In parallel loops, if we want the lower half of the loop body to not be executed 
until the upper part has finished in all the parallel tasks, we can use a barrier. 
Tasks that finish early will stop and await at the barrier until the last parallel 
task reaches the barrier.

## Mini Project 1
Learn how to use Fork-Join framework to run task in parallel
* Make a ReciprocalArraySumTask to do calculation in parallel by extending from 
RecursiveAction, which contains methods such as invoke(), fork(), join(), etc. See
[documentation](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/RecursiveAction.html)
for more
* A different parallel programming framework is Async-Finish. Here Async is similar 
to Fork, and Finish is similar to Join.
* When dealing with multiple parallel tasks, we could use ForkJoinPool to hold all 
the parallel tasks, and invoke() or invokeAll() to execute the fork-join steps

## Mini Project 2
Learn how do use [Stream Interface](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) and [Collectors](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html) to run parallel tasks. 
Stream is introduced in Java 8 as a way to efficiently iterate or manipulate 
elements in Collections.
* Stream's filter example: 
```
Stream.of(personsArr).filter(p -> p.hasKids()) \\ filter out people who have
kids
```
* Stream's map example:
```
Stream.of(personsArr).map(p -> p.getAge()) \\ multiply persons' age * 2
```
## Mini Project 3
Use (PCDP)[https://habanero-rice.github.io/PCDP/]'s forall2dChunked to calculate
matrix multiplication
* forall is an API for parallel loop iteration
* forallChunked is an API that allows splitting the loop iterations into chunks 
so that each parallel task is responsible for running loop iterations within that chunks.

## Mini Project 4
User phaser arrive and awaitAdvance to implement a fuzzy barriers. When the
task arrives to a certain place, it could do some work while waiting for other
parallel threads to reach that point.
