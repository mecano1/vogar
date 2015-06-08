# Actions #
Vogar accepts `.java` files, `.jar` files, directories, and class names as arguments to actions. It prepares the code for execution and then executes each action in its own VM.
```
vogar dalvik/libcore/xml/src/test/java/tests/xml/DomTest.java
```
```
vogar xmltests.jar tests.xml.DomTest
```

# Output #
Vogar is a command line application.

Vogar can emit XML compatible with Ant's [JUnitReport task](http://ant.apache.org/manual/OptionalTasks/junitreport.html) and continuous integration tools like [Hudson](http://hudson-ci.org/). Use `--xml-reports-directory` to specify a directory where XML files should be written.


# What to run #

### JUnit tests ###
Write some JUnit tests. Every project includes a bunch of these.
```
vogar <android>/dalvik/libcore/xml/src/test/java/tests/xml/DomTest.java
```

### Caliper benchmarks ###
You'll need a benchmark to run. Caliper includes some in its `src/examples` directory. Then point vogar at a benchmark. Be sure to include the `--stream` option, which forces output to be printed:
```
vogar --stream <caliper>/test/examples/EnumSetContainsBenchmark.java
```

### Executable Java Programs ###
Vogar can run Java programs that have a `public static void main(String[] args)` method.
```
vogar scratch/Main.java
```

### jtreg tests ###
Write or download some [jtreg](http://openjdk.java.net/jtreg/index.html) tests! [OpenJDK](http://openjdk.java.net/) includes a large suite of tests for finding bugs in core class libraries. Then point vogar at a source **directory** containing tests.
```
vogar <openjdk>/test/java/util/TreeMap/
```
Vogar doesn't support prebuilt jtreg tests.

# Where to run it #

### Running on a local Java VM ###
Use `--mode jvm` to target a JVM on the local machine. You can optionally also specify `--java-home` to target a specific JVM:
```
vogar \
    --mode jvm \
    --java-home /System/Library/Frameworks/JavaVM.framework/Versions/1.6/Home/
    <actions>
```

### Running on local Harmony VM ###
Download and build [Apache Harmony](http://harmony.apache.org/). Then run:
```
vogar \
    --mode jvm \
    --java-home <harmony>/target/hdk/jdk/ \
    <actions>
```

### Running on a Dalvik VM ###
You'll need an [Android SDK](http://developer.android.com/sdk/index.html) and a device or emulator. If you have multiple devices, run `adb devices`, and set the `ANDROID_SERIAL` environment variable to the target's device ID. The device or emulator should be reachable via `adb`, so commands like `adb ls` should work.

Make sure your `PATH` environment variable includes Android development tools like: `adb` and `dx`. You can enter this at the command line for one-time-use, or add it to `~/.bashrc` for durable use.
```
export PATH=<sdk>/platform-tools/:<sdk>/tools/:$PATH
```
Specify `--mode device` when launching vogar:
```
vogar \
  --mode device \
  <actions>
```
If your device doesn't have an SD card, vogar can be configured to use another directory with the option `--device-runner-dir /data/vogar`.

If you're running Caliper benchmarks, see [Caliper on Android](http://code.google.com/p/caliper/wiki/CaliperOnAndroid) for some important gotchas.

### Running in an Android Activity ###
This is a variant of running on a Dalvik VM, so all of its setup applies. But instead of running on a bare JVM, code run as an activity participates in the Android application lifecycle. It inherits the same environment as real Android applications. This is most useful for running code that interacts with environment facets like class loaders and system properties.
```
vogar \
  --mode activity \
  <actions>
```

# More Options #
Full option descriptions are available in the online help.
```
vogar --help
```