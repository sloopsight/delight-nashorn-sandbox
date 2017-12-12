# Nashorn Sandbox

A secure sandbox for executing JavaScript in Java apps using the [Nashorn](https://docs.oracle.com/javase/8/docs/technotes/guides/scripting/nashorn/) engine.

Also see [Rhino Sandbox](https://github.com/javadelight/delight-rhino-sandbox).

Part of the [Java Delight Suite](https://github.com/javadelight/delight-main#java-delight-suite).

[![Build Status](https://travis-ci.org/javadelight/delight-nashorn-sandbox.svg?branch=master)](https://travis-ci.org/javadelight/delight-nashorn-sandbox)

## Usage

The sandbox by default **blocks access to all** Java classes.

Classes, which should be used in JavaScript, must be explicitly allowed.

```java
NashornSandbox sandbox = NashornSandboxes.create();
     
sandbox.allow(File.class);
     
sandbox.eval("var File = Java.type('java.io.File'); File;")
```

Or you can inject your java object as a JS global variable

```java
NashornSandboxes sandbox = NashornSandboxes.create();

sandbox.inject("fromJava", new Object());

sandbox.eval("fromJava.getClass();");
```

The sandbox also allows limiting the CPU time and memory usage of scripts. This allows terminating scripts which contain infinite loops and other problematic code.

```java
NashornSandbox sandbox = NashornSandboxes.create();
     
sandbox.setMaxCPUTime(100);
sandbox.setMaxMemory(50*1024);
sandbox.allowNoBraces(false);
sandbox.setMaxPerparedStatements(30); // because preparing scripts for execution is expensive
sandbox.setExecutor(Executors.newSingleThreadExecutor());
     
sandbox.eval("var o={}, i=0; while (true) {o[i++]='abc';};");
```

This code will raise a ScriptCPUAbuseException.

The sandbox beautifies the JavaScript code for this and injects additional statements into the submitted code. It is thus possible that the original line numbers from
the submitted JS code are not preserved. To debug the code, which is generated by the sandbox, activate its debug mode as follows using log4j.properties file:

```
log4j.logger.delight.nashornsandbox.internal.NashornSandboxImpl=DEBUG
```

This will output the generated JS on the console as follows:

```
--- Running JS ---
var \__it = Java.type('delight.nashornsandbox.internal.InterruptTest');var \__if=function(){\__it.test();};
while(true) {__if();
  i = i+1;
}
--- JS END ---
```

## Maven

Just add the following dependency to your projects.

```
<dependency>
    <groupId>org.javadelight</groupId>
    <artifactId>delight-nashorn-sandbox</artifactId>
    <version>[insert latest version]</version>
</dependency>
```

This artifact is available on [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Cdelight-nashorn-sandbox) and 
[BinTray](https://bintray.com/javadelight/javadelight/delight-nashorn-sandbox).

[![Maven Central](https://img.shields.io/maven-central/v/org.javadelight/delight-nashorn-sandbox.svg)](https://search.maven.org/#search%7Cga%7C1%7Cdelight-nashorn-sandbox)

If you are looking for a JAR with all dependencies, you can also download it from [here](https://github.com/javadelight/delight-nashorn-sandbox/releases).

## Contributors

[Eduardo Velasques](https://github.com/eduveks): API extensions to block/allow Rhino system functions; Capability to block/allow variables after Sandbox has been created. 

[Marcin Gołębski](https://github.com/mgolebsk): Major refactoring and performance improvements. Among other things improved the performance
for JS evaluation and better handling of monitoring for threads for possible CPU abuse ([#23](https://github.com/javadelight/delight-nashorn-sandbox/pull/23)).

## Version History

- 0.1.2: Improving way JsBeautifier instance is obtained ([PR 24](https://github.com/javadelight/delight-nashorn-sandbox/pull/24)]) 
- 0.1.1: Making all fields in NashornSandboxImpl `protected` rather than `private` (see issue #19)
- 0.1.0: Major rework and performance improvements implemented by [Marcin Gołębski](https://github.com/mgolebsk) ([PR 23](https://github.com/javadelight/delight-nashorn-sandbox/pull/23))

## Further Documentation

- [JavaDocs](http://modules.appjangle.com/delight-nashorn-sandbox/latest/apidocs/index.html)
- [All Maven Reports](http://modules.appjangle.com/delight-nashorn-sandbox/latest/project-reports.html)