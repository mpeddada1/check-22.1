# check-22.1

## GraalVM 22.1.0 and graal-sdk 21.3.2
Using the following setup:

```
openjdk version "11.0.15" 2022-04-19
OpenJDK Runtime Environment GraalVM CE 22.1.0 (build 11.0.15+10-jvmci-22.1-b06)
OpenJDK 64-Bit Server VM GraalVM CE 22.1.0 (build 11.0.15+10-jvmci-22.1-b06, mixed mode, sharing)

```
and the following dependency in the pom.xml:
```
  <dependency>
      <groupId>org.graalvm.nativeimage</groupId>
      <artifactId>svm</artifactId>
      <version>21.3.2</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>org.graalvm.sdk</groupId>
      <artifactId>graal-sdk</artifactId>
      <version>21.3.2</version>
      <scope>provided</scope>
    </dependency>
```

Results in the following error:
```
Executing: /usr/local/google/home/mpeddada/.sdkman/candidates/java/22.1.0.r11-grl/bin/native-image @/tmp/native-image16016507497671105966args org.graalvm.junit.platform.NativeImageJUnitLauncher
Warning: Ignoring server-mode native-image argument --no-server.
Fatal error: com.oracle.svm.core.util.VMError$HostedError: Option name "AddAllCharsets" has multiple definitions: com.oracle.svm.hosted.jdk.localization.LocalizationFeature$Options.AddAllCharsets and com.oracle.svm.core.jdk.localization.LocalizationFeature$Options.AddAllCharsets
	at com.oracle.svm.core.util.VMError.shouldNotReachHere(VMError.java:68)
	at com.oracle.svm.hosted.option.HostedOptionParser.lambda$collectOptions$0(HostedOptionParser.java:74)
	at com.oracle.svm.common.option.CommonOptionParser.collectOptions(CommonOptionParser.java:170)
	at com.oracle.svm.core.option.SubstrateOptionsParser.collectOptions(SubstrateOptionsParser.java:116)
	at com.oracle.svm.hosted.option.HostedOptionParser.collectOptions(HostedOptionParser.java:63)
	at com.oracle.svm.hosted.option.HostedOptionParser.<init>(HostedOptionParser.java:58)
	at com.oracle.svm.hosted.NativeImageClassLoaderSupport.setupHostedOptionParser(NativeImageClassLoaderSupport.java:169)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.installNativeImageClassLoader(NativeImageGeneratorRunner.java:181)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.main(NativeImageGeneratorRunner.java:123)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner$JDK9Plus.main(NativeImageGeneratorRunner.java:615)
Error: Image build request failed with exit status 1

```
Possibly related to https://github.com/oracle/graal/issues/4210.

However, using the following dependencies:

```
    <dependency>
      <groupId>org.graalvm.nativeimage</groupId>
      <artifactId>svm</artifactId>
      <version>22.0.0.2</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>org.graalvm.sdk</groupId>
      <artifactId>graal-sdk</artifactId>
      <version>21.3.2</version>
      <scope>provided</scope>
    </dependency>
```

Results in the following error:

```
Error: ImageSingletons.add must not overwrite existing key com.oracle.svm.core.jdk.ProtectionDomainSupport
Existing value: com.oracle.svm.core.jdk.ProtectionDomainSupport@65f00478
New value: com.oracle.svm.core.jdk.ProtectionDomainSupport@2424686b
```
Related to: https://github.com/oracle/graal/issues/4518
*Conclusion: GraalVM 22.1.0 runtime is incompatible with graal-sdk 21.3.2 project setup


## GraalVM **21.3.0**:

```
openjdk version "11.0.13" 2021-10-19
OpenJDK Runtime Environment GraalVM CE 21.3.0 (build 11.0.13+7-jvmci-21.3-b05)
OpenJDK 64-Bit Server VM GraalVM CE 21.3.0 (build 11.0.13+7-jvmci-21.3-b05, mixed mode, sharing)
```
And the following pom.xml:
```
    <dependency>
      <groupId>org.graalvm.nativeimage</groupId>
      <artifactId>svm</artifactId>
      <version>22.0.0.2</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>org.graalvm.sdk</groupId>
      <artifactId>graal-sdk</artifactId>
      <version>21.3.2</version>
      <scope>provided</scope>
    </dependency>
```

Results in:

```
INFO] Executing: /usr/local/google/home/mpeddada/.sdkman/candidates/java/21.3.0.r11-grl/bin/native-image @/tmp/native-image873591198839571670args org.graalvm.junit.platform.NativeImageJUnitLauncher
Warning: Ignoring server-mode native-image argument --no-server.
Fatal error:com.oracle.svm.core.util.VMError$HostedError: Option name "PrintFlags" has multiple definitions: com.oracle.svm.common.option.CommonOptions.PrintFlags and com.oracle.svm.core.SubstrateOptions.PrintFlags
	at com.oracle.svm.core.util.VMError.shouldNotReachHere(VMError.java:68)
	at com.oracle.svm.hosted.option.HostedOptionParser.collectOptions(HostedOptionParser.java:87)
	at com.oracle.svm.hosted.option.HostedOptionParser.<init>(HostedOptionParser.java:60)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.buildImage(NativeImageGeneratorRunner.java:277)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.build(NativeImageGeneratorRunner.java:569)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner.main(NativeImageGeneratorRunner.java:122)
	at com.oracle.svm.hosted.NativeImageGeneratorRunner$JDK9Plus.main(NativeImageGeneratorRunner.java:599)

```
**However GraalVM 21.3.2 amd project pom 21.3.2 work well**

## Graalvm 22.0.0.2

**Note that both setups work fine when building with 22.0.0.2.**

## Testing with 22.1.0 runtime and 22.1.0 project pom.xml
1) Update version of graal-sdk to 22.1.0 in gax [dependencies.properties](https://github.com/googleapis/gax-java/blob/main/dependencies.properties). 
2) Call `./gradlew publishToMavenLocal`
3) Make sure that the gax version in shared-dependencies is set to the SNAPSHOT version you're using.
4) Update the version of `shared-dependencies` to the SNAPSHOT version. 
5) Call `mvn clean install -DskipTests`.
6) On your environment, call `sdk use java 22.1.0.r11-grl`. Check that you're using the correct graalvm version by calling `java -version`. The following message should show up:

```
openjdk version "11.0.15" 2022-04-19
OpenJDK Runtime Environment GraalVM CE 22.1.0 (build 11.0.15+10-jvmci-22.1-b06)
OpenJDK 64-Bit Server VM GraalVM CE 22.1.0 (build 11.0.15+10-jvmci-22.1-b06, mixed mode, sharing)
```

7) In java-pubsub, update the shared-dependencies version to the SNAPSHOT.
8) Call `mvn clean install -DskipTests`. 
9) Call `mvn test -Pnative`. 

Testing with Pub/Sub results in the following error at build time:

```
[INFO] Executing: /usr/local/google/home/mpeddada/.sdkman/candidates/java/22.1.0.r11-grl/bin/native-image @/tmp/native-image5789583573657594787args org.graalvm.junit.platform.NativeImageJUnitLauncher
...
Fatal error: org.graalvm.compiler.debug.GraalError: com.oracle.graal.pointsto.constraints.UnsupportedFeatureException: Detected a PlatformManagedObject (a MXBean defined by the virtual machine) in the image heap. This bean is introspecting the VM that runs the image builder, i.e., a VM instance that is no longer available at image runtime. Class of disallowed object: com.sun.management.internal.HotSpotDiagnostic  Object has been initialized by the com.oracle.svm.hosted.jfr.JfrFeature class initializer with a trace: 
 	at com.sun.management.internal.HotSpotDiagnostic.<init>(HotSpotDiagnostic.java:42)
	at com.sun.management.internal.PlatformMBeanProviderImpl.getDiagnosticMXBean(PlatformMBeanProviderImpl.java:274)
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Unknown Source)
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:566)
	at com.oracle.svm.hosted.jfr.JfrFeature.getDiagnosticBean(JfrFeature.java:154)
	at com.oracle.svm.hosted.jfr.JfrFeature.<clinit>(JfrFeature.java:125)
. Try to avoid initializing the class that stores the object in a static field The object was probably created by a class initializer and is reachable from a static field. You can request class initialization at image runtime by using the option --initialize-at-run-time=<class-name>. Or you can write your own initialization methods and call them explicitly from your main entry point.
	at com.oracle.graal.pointsto.util.AnalysisFuture.setException(AnalysisFuture.java:49)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:269)
	at com.oracle.graal.pointsto.util.AnalysisFuture.ensureDone(AnalysisFuture.java:63)
	at com.oracle.graal.pointsto.heap.ImageHeapScanner.lambda$postTask$9(ImageHeapScanner.java:611)
	at com.oracle.graal.pointsto.util.CompletionExecutor.executeCommand(CompletionExecutor.java:193)
	at com.oracle.graal.pointsto.util.CompletionExecutor.lambda$executeService$0(CompletionExecutor.java:177)
	at java.base/java.util.concurrent.ForkJoinTask$RunnableExecuteAction.exec(ForkJoinTask.java:1426)
	at java.base/java.util.concurrent.ForkJoinTask.doExec(ForkJoinTask.java:290)
	at java.base/java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(ForkJoinPool.java:1020)
	at java.base/java.util.concurrent.ForkJoinPool.scan(ForkJoinPool.java:1656)
	at java.base/java.util.concurrent.ForkJoinPool.runWorker(ForkJoinPool.java:1594)
	at java.base/java.util.concurrent.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:183)
Caused by: com.oracle.graal.pointsto.constraints.UnsupportedFeatureException: Detected a PlatformManagedObject (a MXBean defined by the virtual machine) in the image heap. This bean is introspecting the VM that runs the image builder, i.e., a VM instance that is no longer available at image runtime. Class of disallowed object: com.sun.management.internal.HotSpotDiagnostic  Object has been initialized by the com.oracle.svm.hosted.jfr.JfrFeature class initializer with a trace: 
 	at com.sun.management.internal.HotSpotDiagnostic.<init>(HotSpotDiagnostic.java:42)
	at com.sun.management.internal.PlatformMBeanProviderImpl.getDiagnosticMXBean(PlatformMBeanProviderImpl.java:274)
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Unknown Source)
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:566)
	at com.oracle.svm.hosted.jfr.JfrFeature.getDiagnosticBean(JfrFeature.java:154)
	at com.oracle.svm.hosted.jfr.JfrFeature.<clinit>(JfrFeature.java:125)
. Try to avoid initializing the class that stores the object in a static field The object was probably created by a class initializer and is reachable from a static field. You can request class initialization at image runtime by using the option --initialize-at-run-time=<class-name>. Or you can write your own initialization methods and call them explicitly from your main entry point.
	at com.oracle.svm.hosted.image.DisallowedImageHeapObjectFeature.error(DisallowedImageHeapObjectFeature.java:173)
	at com.oracle.svm.hosted.image.DisallowedImageHeapObjectFeature.checkDisallowedMBeanObjects(DisallowedImageHeapObjectFeature.java:162)
	at com.oracle.svm.hosted.image.DisallowedImageHeapObjectFeature.replacer(DisallowedImageHeapObjectFeature.java:119)
	at com.oracle.graal.pointsto.meta.AnalysisUniverse.replaceObject(AnalysisUniverse.java:582)
	at com.oracle.svm.hosted.ameta.AnalysisConstantReflectionProvider.replaceObject(AnalysisConstantReflectionProvider.java:257)
	at com.oracle.svm.hosted.ameta.AnalysisConstantReflectionProvider.interceptValue(AnalysisConstantReflectionProvider.java:228)
	at com.oracle.svm.hosted.heap.SVMImageHeapScanner.transformFieldValue(SVMImageHeapScanner.java:126)
	at com.oracle.graal.pointsto.heap.ImageHeapScanner.onFieldValueReachable(ImageHeapScanner.java:331)
	at com.oracle.graal.pointsto.heap.ImageHeapScanner.onFieldValueReachable(ImageHeapScanner.java:310)
	at com.oracle.graal.pointsto.heap.ImageHeapScanner.lambda$computeTypeData$1(ImageHeapScanner.java:153)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
```

This was likely introduced with https://github.com/oracle/graal/commit/09f491e57c3f35c7ea6f35ef3cc5dfbbe229b469. 
