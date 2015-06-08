Vogar is a command line tool that attempts to boost your productivity when developing Java programs.

It accepts prebuilt .jar files or .java source files. It understands tests, [benchmarks](http://code.google.com/p/caliper/), and classes with main methods. It targets your desktop JVM, attached Android devices, or on a far away machine. And it runs interactively during development, or automatically in your continuous build. [More examples...](http://code.google.com/p/vogar/wiki/Examples)

<font face='monospace'>
<br /><strong>$ vogar --mode jvm --stream test/java/util/TreeMap/</strong>
<br />Actions: 6<br>
<br />Action java.util.TreeMap.ContainsValue <font color='green'><strong>OK (SUCCESS)</strong></font>
<br />Action java.util.TreeMap.NullPermissiveComparator<br>
<br />    {} not equal to {null=NULL, a=A, b=B, c=C}	at java.lang.Thread.dumpStack(Thread.java:1158)<br>
<br />        at NullPermissiveComparator.fail(NullPermissiveComparator.java:102)<br>
<br />        at NullPermissiveComparator.main(NullPermissiveComparator.java:110)<br>
<br />  <font color='red'><strong>FAIL (EXEC_FAILED)</strong></font>
<br />Action java.util.TreeMap.NullAtEnd <font color='green'><strong>OK (SUCCESS)</strong></font>
<br />Action java.util.TreeMap.HeadTailTypeError <font color='green'><strong>OK (SUCCESS)</strong></font>
<br />Action java.util.TreeMap.SubMap <font color='green'><strong>OK (SUCCESS)</strong></font>
<br />Action java.util.TreeMap.SubMapClear <font color='green'><strong>OK (SUCCESS)</strong></font>
<br />Failure summary:<br>
<br /><font color='red'><strong>java.util.TreeMap.NullPermissiveComparator</strong></font>
<br />Outcomes: 6. Passed: 5, Failed: 1, Skipped: 0</font>