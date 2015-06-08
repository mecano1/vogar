# Quick Summary / Cheat Sheet #

To automatically record the results of tests (this option mostly to be used on a continuous build server) (these can be included either in ~/.vogarconfig or on the command-line):

```
--record-results
--results-dir path/to/desired/results/dir
```

To automatically compare against results generated with --record-results (this can be included either in ~/.vogarconfig or on the command-line):

```
--results-dir path/to/results/dir
```

To save the flags and targets for a test, and record a snapshot of its result (include this on the command-line. The tag directory may be given in .vogarconfig):

```
--tag <tagname> [--tag-overwrite] (if the tag already exists, this will overwrite it and its results)
--tag-dir path/to/desired/tags/dir
```

To run a previously created tag (include this on the command-line, any additional flags provided will override those provided by the tag. The tag directory may be given in .vogarconfig):

```
--run-tag <tagname>
--tag-dir path/to/desired/tags/dir
```

Note that you can run somebody else's tags by simply specifying the correct tag directory (as long as its permissions allow you to see it).

# Introduction #

One of the primary uses of Vogar is as a test runner. It can be used to run a large number of regression tests as, for example, part of a continuous build. However, in many projects with large regression test suites, not all tests may be passing all the time. It can be difficult for a developer to know whether a failure of a particular test is due to a change he made, or is a preexisting failure. Similarly, the continuous build needs to keep track of test failures so that it can detect true regressions and address them quickly.

Vogar has features that can help with these problems.

# Keeping a test history #

There are a couple of methods that Vogar supports for saving a history of the results of tests. You can choose to either keep a deliberate test history (using "tags") for certain tests using tags, or keep an automatic test history either locally or on a continuous build.

Either way, these test histories can give you interesting information when running tests. For example:

```
$ vogar --run-tag pq

Actions: 1
Action tests.api.java.util.concurrent.PriorityQueueTest
  tests.api.java.util.concurrent.PriorityQueueTest#testAdd OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testAddAll1 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testAddAll2 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testAddAll3 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testAddAll5 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testAddNull OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testClear OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor1 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor2 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor3 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor4 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor5 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor6 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testConstructor7 OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testContains OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testContainsAll OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testElement OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testEmpty OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testIterator OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testIteratorRemove OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testOffer OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testOfferNonComparable OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testOfferNull OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testPeek OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testPoll OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testRemove OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testRemoveAll OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testRemoveElement OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testRetainAll OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testSerialization OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testSize OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testToArray OK (SUCCESS)
  tests.api.java.util.concurrent.PriorityQueueTest#testToArray2
    junit.framework.AssertionFailedError
    	at tests.api.java.util.concurrent.PriorityQueueTest.testToArray2(PriorityQueueTest.java:415)
    	at java.lang.reflect.Method.invokeNative(Native Method)
    	at vogar.target.JUnitRunner$TimeoutTestResult$1.call(JUnitRunner.java:173)
    	at vogar.target.JUnitRunner$TimeoutTestResult$1.call(JUnitRunner.java:169)
    	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:305)
    	at java.util.concurrent.FutureTask.run(FutureTask.java:137)
    	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1081)
    	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:574)
    	at java.lang.Thread.run(Thread.java:1021)

  tests.api.java.util.concurrent.PriorityQueueTest#testToArray2 FAIL (EXEC_FAILED)
  tests.api.java.util.concurrent.PriorityQueueTest#testToString OK (SUCCESS)
Failure summary:
  tests.api.java.util.concurrent.PriorityQueueTest#testToArray2 [✓ at tag pq] [last 4: X✓X✓] [result last changed: 2010-06-28 16:19:58] (you probably broke this)
Outcomes: 34. Passed: 33, Failed: 1, Skipped: 0. Took 11.88 s.
```

This tells us that in recent history, this test has worked, but that it's been broke twice before. It tells us that when we created the tag "pq", the test worked, so something between then and now broke the test. We could easily see if the test is chronically broken (if the "last 4" results ended in a failure), so this tells us that something we did broke the test.

In addition, Vogar will try to tell you when you've fixed tests, so that you can verify that you fixed what you intended to fix.

## Automatic test history ##

### Recording test history ###

Keeping an automatic test history is done by simply adding a "--record-results" flag to invocations of Vogar (consider adding this to ~/.vogarconfig if you want to do this often).

You can specify where test results are kept by passing a directory to the "--results-dir" flag.

### Using automatic test history ###

Simply point "--results-dir" to a directory where results are being recorded (either locally or remotely) and Vogar will find historic results for any tests you run.

For example, if a continuous build is putting results in /home/continuous-build/vogar-results/, then add this line to your .vogarconfig:

```
--results-dir /home/continuous-build/vogar-results/
```

Note that if you're running an automatic test history locally and have both "--results-dir" and "--record-results" on, then you will be reading and writing to history whenever you invoke a test. This may mask regressions, so when running a test to check for regressions, you should consider using a "--no-record-results" flag.

### Disadvantages of a local automatic test history ###

It is preferable to use the test history of a continuous build for a couple of reasons:

  * A local test history updates on every run, so if the affected tests are run several times after a regression, they may mask the fact that a regression occurred at all. (Note: this may be alleviated by running Vogar with "--no-record-results" to override the flag in .vogarconfig when testing after a change.)
  * VCS branch switching may create inconsistencies in the test history. Tests may fail on one branch and not on another, and this may create the false appearance of an inconsistent test, and may make it difficult to detect a regression.
  * If a given test has never been run locally before, it will not have a history against which to compare, so regressions will be invisible.

If a continuous build is not available, consider keeping a deliberate test history for key tests that you suspect may regress after a change, rather than depending on a local automatic test history. This also helps with the last problem, since you will be guaranteed to have run the test at least once before making changes.

## Deliberate test history (tags) ##

Tags serve two purposes in Vogar. The first is to record run configuration, and the second is to record the results of a run in a certain configuration at a given time.

Here's an example workflow using tags:

Making a tag that represents a set of priority queue tests. This saves the results of this invocation of Vogar for later comparison, as well as the specific flags and targets used.

```
$ vogar --tag pq <SOME_FLAGS> <android>/libcore/luni/src/test/java/tests/api/java/util/concurrent/PriorityQueueTest.java
```

Tags are immutable, but you can overwrite them.

```
$ vogar --tag pq --tag-overwrite --verbose <SOME_DIFFERENT_FLAGS> <android>/libcore/luni/src/test/java/tests/api/java/util/concurrent/PriorityQueueTest.java
```

Then, after making some changes in the PriorityQueue code, rerun the tests. This will automatically use all the same flags and targets that the test was originally run with.

```
$ vogar --run-tag pq
```

Vogar will point out any ways in which this run differs from the run in which the tag was created.

Note that any new flags you give Vogar will override any given by the tag. So in this example, you could turn off verbose mode.

```
$ vogar --run-tag pq --no-verbose
```