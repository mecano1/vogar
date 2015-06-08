# Troubleshooting #

### Cannot find symbol: KnownFailure ###
Consider setting the build classpath to `out/target/common/obj/JAVA_LIBRARIES/core_intermediates/classes.jar`. Dalvik tests depend on marker annotations, but these annotations aren't on the build classpath for host builds.

### Builds of targets failing due to missing packages or classes ###
Sometimes, for example when you provide Vogar with a .java file to run as a target, target builds will fail due to missing dependencies.

Try adding jar search directories to ~/.vogarconfig. For example, if you are running Android tests, you may want to add a line like this:

```
--jar-search-dir <android>/out/target/common/obj/JAVA_LIBRARIES/
```

Then try running the target again and Vogar will recursively search the directory given for classes and packages that match the one you're missing. You may add multiple jar search dirs if there are multiple places from which you commonly draw jars.

Note that this won't catch all missing dependencies, but can help in some simple cases.

### No classes in package: libcore.net.http.URLConnectionTest; ###

Dx silently ignores `.class` files compiled with Java 7 and above. This leads to a runtime error like the following:

```
    java.lang.IllegalArgumentException: No classes in package: libcore.net.http.URLConnectionTest; classpath is [/sdcard/vogar/run/libcore.net.http.URLConnectionTest.dex.jar, /sdcard/vogar/run/mockwebserver-20120731.dex.jar, /sdcard/vogar/run/okhttp-0.8-SNAPSHOT.dex.jar, /sdcard/vogar/run/vogar.dex.jar]
    	at vogar.target.ClassFinder.find(ClassFinder.java:43)
    	at vogar.target.TestRunner.run(TestRunner.java:197)
    	at vogar.target.TestRunner.run(TestRunner.java:170)
    	at vogar.target.TestRunner.main(TestRunner.java:257)
    	at dalvik.system.NativeStart.main(Native Method)
    Action libcore.net.http.URLConnectionTest did not complete normally.
    timedOut=false
    lastStartedOutcome=null
    lastFinishedOutcome=null
    command=adb shell cd /sdcard/vogar/run/libcore.net.http.URLConnectionTest && ANDROID_DATA=/sdcard/vogar dalvikvm -classpath /sdcard/vogar/run/libcore.net.http.URLConnectionTest.dex.jar:/sdcard/vogar/run/mockwebserver-20120731.dex.jar:/sdcard/vogar/run/okhttp-0.8-SNAPSHOT.dex.jar:/sdcard/vogar/run/vogar.dex.jar -Djava.io.tmpdir=/sdcard/vogar/run/libcore.net.http.URLConnectionTest -Duser.home=/sdcard/vogar/run/user.home -Duser.name=root -Duser.language=en -Duser.region=US -Xverify:none -Xdexopt:none -Xcheck:jni -Xjnigreflimit:2000 vogar.target.TestRunner
```

Workaround this by building with a Java 6 compiler.