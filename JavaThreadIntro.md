# Introduction #
  * Today, we will work on Threads in Java, which is related to concurrency, and transactions, but also different.  Next time, we will talk more specifically about databases, transactions, etc.
  * Create a new Java Project.
  * Create a new class, named Start.
  * Create another new class, named 'SameTimePrint'
  * Put the code from the wiki page 'ThreadIntroCode' into these classes.
  * Look carefully at the main function.  We create 2 threads.  Then, we start running both at the same time.  When both are done, we move to the next line, and repeat.
  * Run the program several times, and compare the outputs.
  * In the SameTimePrint, we use Thread.sleep(0) to pause momentarily, otherwise the printing will happen more quickly.  Delete this line, and run the program to see what happens.

### Part 2 ###
  * Next, instead of printing, we will have 2 threads, which add to a counter.
  * Create a new class, SameTimeAdd, and use the code from the wiki page 'ThreadIntroCode'.
  * Now, change the main function to create two threads of type SameTimeAdd, instead of SameTimePrint.
  * In main, at the end of the loop, print out SameTimeAdd.count, and set it to 0 again.
  * Before you run the program, what do you think will be printed?
  * Now, run the new program several times and compare the results.  Was the number that was printed what you expected?

Look at the line: count = count + 1.  There are 3 steps:
  1. Read count
  1. Add 1
  1. Write new count
If we have two threads, we could have the following:
  1. Thread 1: Read count (count = 5)
  1. Thread 2: Read count (count = 5)
  1. Thread 1: Add 1 (count = 5, temporary 6)
  1. Thread 2: Add 1 (count = 5, temporary 6)
  1. Thread 1: Write count (count = 6)
  1. Thread 2: Write count (count = 6)

In english we call this a **race condition**.

Now, we fix the problem.

  * Add a variable to SameTimeAdd:
```
private static Object lock = new Object();
```
  * Now, we can make sure the 'count = count + 1' is not a problem.  Use the word synchronized to surround this line:
```
synchronized(lock){
  count = count + 1;
}
```

Now, try to run the program again.  Compare the results.