# Usage

To use the plugin, first run ```mvn install -DskipTests```. Then copy ```surefire-changing-maven-extension-1.0-SNAPSHOT.jar``` into your Maven installation's ```lib/ext``` directory. More information on how to use Maven extensions can be found [here](https://maven.apache.org/examples/maven-3-lifecycle-extensions.html#use-your-extension-in-your-build-s).

## Example

```
mvn test -Dtest=org.apache.dubbo.rpc.protocol.dubbo.DubboLazyConnectTest#testSticky1,org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest#testDubboProtocol,org.apache.dubbo.rpc.protocol.dubbo.DubboProtocolTest#testDubboProtocolWithMina -Dsurefire.methodRunOrder=fixed
```

By specifying ```-Dsurefire.methodRunOrder=fixed``` Maven test will run the specifed tests in the order they appear in ```-Dtest```.
Running the command above will result in the tests running in the following order:

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

1. **Test methods from different test classes cannot interleave.**  When test methods from various test classes interleave, all test methods from the first time the test class runs will be run then as well. E.g., If tests ClassA.testA, ClassB.testA, ClassA.testB are provided, then the run order will be ClassA.testA, ClassA.testB, ClassB.testA.

2. **FixMethodOrder annotations are ignored.** JUnit 4.11+ provides the annotation [FixMethodOrder](https://junit.org/junit4/javadoc/4.12/org/junit/FixMethodOrder.html) to control the ordering in which tests are run. Such annotations are ignored when this plugin is used. E.g., If tests ClassA.testB, ClassA.testA are provided and NAME_ASCENDING is used, then the run order will be ClassA.testB, ClassA.testA.
