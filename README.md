# check-22.1

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

Building with **21.3.0**:

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

**Note that both setups work fine when building with 22.0.0.2.**
