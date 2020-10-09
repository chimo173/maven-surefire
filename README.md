# Usage

This repository is a fork of Maven Surefire that contains two main modifications.

1. A Maven extension to ensure that any Maven project one runs ```mvn test``` on will use this custom version of Surefire instead, and
2. the ability to control the ordering of tests run directly with Maven Surefire.

## Setup

To use the plugin, perform the following steps.

1. Run ```mvn install -DskipTests``` in this directory
2. Copy ```surefire-changing-maven-extension/target/surefire-changing-maven-extension-1.0-SNAPSHOT.jar``` into your Maven installation's ```lib/ext``` directory

The copying of the extension helps ensure that any project you run ```mvn test``` on will now use this custom version of Surefire and change certain settings (e.g., reuseForks to false) to prevent issues with fixing the ordering of tests. More information on how to use Maven extensions can be found [here](https://maven.apache.org/examples/maven-3-lifecycle-extensions.html#use-your-extension-in-your-build-s). Note that if you already have ```surefire-changing-maven-extension-1.0-SNAPSHOT.jar``` in your Maven installation's ```lib/ext``` you must first remove the jar before installing again.

## Example

```
mvn test -Dsurefire.methodRunOrder=fixed \
-Dtest=org.apache.dubbo.rpc.protocol.dubbo.DubboLazyConnectTest#testSticky1,\
org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest#testDubboProtocol,\
org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest#testDubboProtocolWithMina
```

By specifying ```-Dsurefire.methodRunOrder=fixed``` Maven test will run the specifed tests in the order that they appear in ```-Dtest```.
Specifically, running the command above will result in the tests running in the following order:

1. org.apache.dubbo.rpc.protocol.dubbo.DubboLazyConnectTest.testSticky1
2. org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest.testDubboProtocol
3. org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest.testDubboProtocolWithMina

## Example with file

```
mvn test -Dtest=./path_to_file -Dsurefire.methodRunOrder=fixed
```

By specifying ```-Dsurefire.methodRunOrder=fixed``` Maven test will run the specifed tests in the order that they appear in the file ```./path_to_file```.

Assume the content of ```./path_to_file``` are the following:

```
org.apache.dubbo.rpc.protocol.dubbo.DubboLazyConnectTest#testSticky1
org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest#testDubboProtocol
org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest#testDubboProtocolWithMina
```

Then running the Maven command above will result in the tests running in the following order:

1. org.apache.dubbo.rpc.protocol.dubbo.DubboLazyConnectTest.testSticky1
2. org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest.testDubboProtocol
3. org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest.testDubboProtocolWithMina


## Caveats

1. **Test methods from different test classes cannot interleave.**  When test methods from various test classes interleave, all test methods from the first time the test class runs will run then as well. E.g., If tests ClassA.testA, ClassB.testA, ClassA.testB are provided, then the run order will be ClassA.testA, ClassA.testB, ClassB.testA.

2. **FixMethodOrder annotations are ignored.** JUnit 4.11+ provides the annotation [FixMethodOrder](https://junit.org/junit4/javadoc/4.12/org/junit/FixMethodOrder.html) to control the ordering in which tests are run. Such annotations are ignored when this plugin is used. E.g., If tests ClassA.testB, ClassA.testA are provided and FixMethodOrder is set to NAME_ASCENDING, then the run order will still be ClassA.testB, ClassA.testA.

## TODOs

The following are features that we would like to have but are yet to be supported.

1. Randomize every test in a class (mostly done by Jon already)
  - Randomize with seed, prints the seed somewhere
2. Have surefire reports save the order in which the test classes are run
3. Allow one to get just the test order list without running tests
4. Reverse mode
