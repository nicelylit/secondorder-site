---
title: "How to Write Unit Tests"
date: 2017-05-23
categories: 
  - "软件开发"
---

This is not an article that guides you to setup a concrete unit test example. It is about discussing the basic principles, including the motivation of writing unit tests, the concepts from popular unit testing frameworks, and the thinking on what ideal unit tests look like. It will give beginners a good foundation on learning and writing unit tests in the future.

# Why do we need to write unit tests?

The purposes of writing unit tests could be various to different roles. To managers, they want to

- increase the software quality with lower crash rate and fewer unexpected behaviors,
- reduce the operational workloads, including easier to extend new features, lower cost for performing regression tests, quicker to do hotfix, etc.

To engineers, they want to

- improve the code quality with clearer design and understandable statements,
- avoid debugging annoying logical defects,
- jump out of repetitive small tasks and deal with unknown patterns.

Of course, the role could be a team or someone else, but in this article, we majorly think as an engineer. We ask ourselves, in terms of code itself, not the artifacts or the products, _what problems does unit testing try to solve_? We found we cannot answer it, unless we know what unit testing is? Apparently, the question omits an important restriction which is to help us, because the motivation for unit testing is partly from that any engineer will definitely make mistakes. We have logical defects and usually lack of knowledge of some areas. Sometimes, laziness and ignorance could facilitate the process. Actually, we can rewrite the question to be “_what problems do engineers want unit testing help them to solve in terms of code itself_?”

The unknowns in this question are ‘problems’. The knowns and restrictions in this question are ‘unit’, ‘testing’, ‘help us’, and ‘code’. To get these unknowns, we can ask us further about below questions by thinking about knowns and restrictions:

- How small is the unit? A function (method) or a class?
- If it is a method, what do we want to test and need to test? The parameters, the returns, or the unexpected conditions?
- If it is a class, what do we want to test? All its members or functions (methods)? How do we deal with its dependencies? Do we need to test method invocations of dependencies as well?
- How about the functions marked with private, final, static[\[1\]](#_ftn1)? Are they needed be tested?
- How is the testing itself? In other words, how does it work?
- Will the testing framework be a part of the artifacts?
- What is the boundary of unit testing? What problems cannot unit testing deal with? What is the relation between unit testing and integration testing? What is the relation between unit testing and manual code review?
- How do we measure the effectiveness of the tests?
- How will unit tests affect the code quality?
- Do we need to test the unit tests themselves?

After we find out answers for above questions, we will not only get the answers about the ‘problems’ that unit testing helps us to solve, but form an opinion on what good unit tests should look like. As a result, we will become followers to enjoy writing unit tests rather than feel sick of it.

Since we are not the pioneer to come up with the idea of doing unit testing, we do not need to reinvent the wheel to write a unit testing library to get all these answers or lose ourselves in the details of how to write such a library. We are lucky to seek answers from studying prevalent unit testing libraries. Their solutions are tested by a large number of projects.

# The basic concepts and how does unit testing work?

A popular solution in Java for unit testing are JUnit + Hamcrest + Mockito + Jacoco. Do not treat them as just a set of libraries or frameworks. Treat them as keys to some locks of continuous integration. They solve real world problems and form the steps towards full automation.

Let us study a typical workflow first.

1. Start a **_runner[**\[2\]**](#_ftn2)_** by clicking an IDE button or running a command in console.
2. The _runner_ either iteratively start a set of _runners_ or to start loading a **_test class_**.
3. The set of runners start loading a set of _test classes_.
4. Each _runner_ iteratively runs **_test methods_**_,_ which is marked with @Test, in the test class.

_Runner_ is the core concept abstracted by JUnit to start running unit tests. It provides the functionality to manually run a specific _test method_ or automatically run a group of _test methods_.

A _test class_ holds a set of test methods. These _test methods_ can share the same preprocessing of some objects with a method marked with @Before and post-processing with a method marked with @After.

Inside a _test method_, a method from production code is invoked with **_mocked_** dependencies and get the actual return value. Afterwards, **_assertions_** are executed to decide if the **_expected value_** and the **_actual value_** are matched or not. Any assertion failure will cause the _runner_ to report an error.

The whole process is quite simple and easy. However, the workflow is also flexible and dangerous to beginners if they do not stay calm and think about questions we put forward in the previous section. Instead of directly answering questions in the first section, let us continue to dive deep via examining the core concepts _in italic_ one by one.

Runner

In JUnit, a [runner](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runner/Runner.java) contains a main method **run()** which describes the ability of running. A runner could only be a class. To achieve the goal of automation, runners are organized in tree structure. All runners are [parent runners](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runners/ParentRunner.java) in a runner tree. A parent runner knows all its children and knows how to run its children, about the order, the shared initialization and the rules.

A typical widely used parent runner is [BlockJUnit4ClassRunner](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runners/BlockJUnit4ClassRunner.java) (aka [JUnit4](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runners/JUnit4.java)). It is omitted by default. Its children are methods which are [FrameworkMethod](https://github.com/junit-team/junit4/blob/41d44734f41aba0cf6ba5a11ff5d32ffed155027/src/main/java/org/junit/runners/model/FrameworkMethod.java)s and which are annotated with @Test. Since a FrameworkMethod is not a runner, it can be treated as a leaf in a runner tree. The following three ways are equivalent when using this runner

```
public class FileOnceLoaderTest {
}

@RunWith(BlockJUnit4ClassRunner.class)
public class FileOnceLoaderTest {
}

@RunWith(JUnit4.class)
public class FileOnceLoaderTest {
}
```

JUnit also provides ‘[Suite](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runners/Suite.java)’ to run a suite of runners, ‘[Parameterized](https://github.com/junit-team/junit4/wiki/Parameterized-tests)’ to run a set of inputs and expected values, etc.

Other popular runners are [PowerMockRunner](https://github.com/powermock/powermock/blob/master/powermock-modules/powermock-module-junit4/src/main/java/org/powermock/modules/junit4/PowerMockRunner.java), SpringJUnit4ClassRunner, JUnit4IdeaTestRunner.

All in all, when writing unit tests, a runner is not only the executor, it also prepares the running context for the test classes and test methods. For example, BlockJUnit4ClassRunner can recognize annotations like @Test, @Before, @After, etc.; PowerMockRunner can understand @PrepareForTest.

Test Class

[Test class](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runners/model/TestClass.java) is the basic unit for runners to be annotated in JUnit, for the Target of RunWith is TYPE. As previously summarized, runners are not only the executor, they are also the context providers. Some runners need to use a special ClassLoader to inject byte codes before a method or inside a class for a special purpose. For example, PowerMock use the technique to make mock static methods and mock new objects possible.

After knowing these two facts, we feel like putting unit tests which share the same context information in one test class. The context information in a test class is usually the running context provided by the runner and most importantly mocked objects. We often expected one unit tests to run within several milliseconds or dozens of milliseconds. It is not very good to inject too many useless mocked objects.

Another question that developers may have confusion is that why all test classes must come with Test suffix. Is it because JUnit requires that? I think the answer is no. It is a convention from JUnit. It is from build tools that you use. For example, Gradle java plugin requires that by default. Of course, we can [do some filtering](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_test) for the test class folders and matchers for the test class name, but following the convention make everyone easier.

Test Method

As described in Runner section, [test methods](https://github.com/junit-team/junit4/blob/master/src/main/java/org/junit/runners/model/FrameworkMethod.java) are leaves in a runner tree. Each test method is designed to be independent from other test methods. All test methods are running in unknown order by default. If you begin to consider orders of tests, you may need to consider about the boundary of unit testing. Are you doing integration testing now? Did you mock all the objects which this test depends on in correct state?

Since the emerge of assembly language, function (or subroutine or method or procedure) has been a great abstraction for breaking code length and modularizing functionalities. It also shows great benefit for improving the utilization of limited resources, such as CPU and memory.

A function in Java is also the smallest unit for organizing and executing a piece of code. A function has a set of inputs and a set of outputs. Some inputs are from its object and others are from callers. It is the same with outputs.

From language level, we cannot execute a piece of code which is smaller than a function in modern language, therefore, ‘unit’ in unit testing is refer to function. In this paragraph, a ‘unit test’ and a ‘test method’ is equivalent concept.

One test method should only test one unit instantly. Due to variations of inputs, one unit can be tested by several test methods.

Mock Object

Mock objects, as the name described, are simulated objects that reduce the complex behaviors of real objects. They are for unit testing of object-oriented language. In Java, dependencies are managed in object level. Most functions belong to some objects. In unit testing, we have the following reasons to use mock objects rather than creating the real objects:

- Real objects could be heavy, but the unit test only relies on one or two methods of the object. An example of heavy object is the context object in Android Framework. None wants to create them. Even mock objects of context are provided by library.
- Redundant testing could happen. If real objects are from the same library and their methods are in the bottom of the call stack, they will be invoked for many times.
- Real objects could depend on some resources that the test environment cannot provide. A typical scenario is that unit testing is executed in a sandbox which blocks the network, but the real objects need to communicate with remote objects.
- Methods in real objects could have bugs. Mocking objects can avoid the problem.

After persuading ourselves why we have to use them, we may also need to know when to use them.

- Should we mock the object we are trying to test one of its method? No. We are testing it. We shouldn’t mock it.
- Should we mock objects from Java library? No. We shouldn’t mock them. We don’t have to.
- Should we mock objects which the tested object depends on or the tested method depends on and which are from the current library? Yes. We should mock them. We had better do that.
- Should we mock objects which are from third libraries? If the third library provide some mocks for us, then we shouldn’t. If they don’t, we should mock them.

If all the objects are created outside the class or the method to be tested, it will be perfect and make mocking easier, for we can mock the object, get its reference and pass to the class or the method to be tested. However, when the mock objects are created inside the class or method to be tested. Mockito cannot handle the case, since Mockito does not provide a running context by modifying the byte codes and each running will generate a different reference and Mockito cannot get the reference. We have two options by either moving the creation out of the class or method or using PowerMock. When facing this choice, we need to think if there is something wrong with our design. We can ask ourselves questions. Why the creation of this object must come with the code for messaging or calculation? Why the creation of this object must inside this method? Can we move it to method parameters? Can we move it to constructor parameters? Does this object indeed have one to one relationship with the object to be tested?

If there are only objects in the object-oriented world, it will be perfect and make mocking easier. However, Static methods are everywhere. They are designed to save resources and improve efficiency. They only depend on global running context rather than any objects. Mocking static methods need a different technique. Mockito cannot handle the case, we also have two options by either creating an interface for the static method then we can mock the interface or using PowerMock. When facing this choice, we also need to think about our design. Keep asking questions to ourselves. Why do we need this static method? Singleton? To limit the creation of objects?

Assertion

Assertions are the last step of unit testing. Making assertions for a unit test shows how well we know the output. It also shows our purpose for doing this unit test.

For function with returns, we can assert the outputs. For function without returns, the method must have modified some variables in object scope. We can assert them by calling public methods which can get the value of these variables. If we cannot get the value of these variables, we need to think about our design again.

For functions, either with or without returns, exceptions should always be properly considered and asserted.

JUnit provides a set of basic APIs to do assertions. Hamcrest gives more complicated ones. They are both quick to learn and use. Take half an hour to read their documents for beginners.

By far, we nearly get the whole picture about how the unit testing works except for measuring the effectiveness of our unit tests. How do we know if we have tested all the branches inside a method? How do we know if we have covered all the lines inside a method? ‘Covered’ means executed at least once. How do we know if we catch all the exceptions thrown by the method?

The answer is to use code coverage library. Jacoco is a popular library for Java. The following picture is a typical HTML report that Jacoco generates. Green lines are the code which has been run at least once in unit tests. Red lines are the code which has not been run at all. Yellow lines are the code which has been run partially, because some of the branches are missed. When you hover the mouse pointer, a tooltip will tell us how many branches are missed.

[![Screen Shot 2017-05-21 at 12.55.52](http://mjm1990.com/wp-content/uploads/2017/05/Screen-Shot-2017-05-21-at-12.55.52.png)](http://mjm1990.com/wp-content/uploads/2017/05/Screen-Shot-2017-05-21-at-12.55.52.png)

Line coverage rate and branch coverage rate are the two metrics that we often use to measure the effectiveness of our unit tests. They are easy to understand by reading the words, but different library could give different reports due to technique defects. The following picture illustrates what a Jacoco report looks like.

[![Screen Shot 2017-05-22 at 19.47.09](http://mjm1990.com/wp-content/uploads/2017/05/Screen-Shot-2017-05-22-at-19.47.09.png)](http://mjm1990.com/wp-content/uploads/2017/05/Screen-Shot-2017-05-22-at-19.47.09.png)

Jacoco helps us to do test-driven development faster and robust.

# What should good unit tests look like?

After going through these core concepts in unit testing, it is not too hard to get answers for questions in the first part. By answering them, we can get what good unit tests should look like. So, below rules are just best practices, they are not truth. If you think they are not defined well, give reasons why they are not defined well and how should we make them better.

- A unit in unit testing should be a public method, either static or non-static, either final or non-final.
- A unit test should only test one method.
- One methods could be tested by several unit tests.
- A unit test should be named like this test{$MethodName}\_{$testPurpose}.
- A unit test can have multiple assertions.
- A test class should only test one class. One class can be tested in several test classes.
- Each test class had better be put in the same package of the class to be tested and named with suffix Test.
- All dependencies of a method to be tested either from the current library or third library should be mocked, except for methods or objects from Java library.
- When meeting with mocking an object whose creation process is inside a method or class to be tested, the first choice should be to consider changing the design, the second choice is to mock them with powerful mock library.
- When meeting with mocking a static method, the first choice should be to consider changing the design, the second choice is to mock them with powerful mock library.
- Had better put methods that need powerful mock library in a separate test class.
- First version of unit tests does not need to extract shared mocking objects.
- Configure coverage report library in the build configuration file.
- Minimum line coverage rate and branch coverage rate should be set.

Finally, let us go back to the question in the beginning of this article. “_what problems do engineers want unit testing help them to solve in terms of code itself_?” I think most importantly unit testing help engineers to rethink their design which may cause modules tightly coupled and mocking difficult. Unit testing helps engineers to rethink the necessity of every input, out and exception. Unit testing help engineers automate the process of running cases. Unit testing helps engineers to put into their users’ shoes, so they can know if the classes or methods are easy to use or not. Unit testing is documentation to engineers themselves and their users (other engineers). It helps them locate bugs and find bugs easily and early. It also helps them learn the functionality of methods written by others.

Unit testing has limitations too. It cannot and should not test interactions between modules. It cannot catch every error in the program. We cannot list the condition for all errors that could happen in a program. It is like the halting problem of a Turing machine.

I wrote a light-weight example for how to use JUnit + Hamcrest + Mockito + PowerMock + Spring Test Framework + Jacoco. Unit tests in this example follows best practices I stated above. If you are interested in it, check [this link](https://github.com/jeremy1990/UnitTestsJudgeLib).

 

[\[1\]](#_ftnref1) Programming language beginners may think ‘private’, ‘final’, ‘static’ as language specific stuffs, but they actually stand for some engineering solutions for factual problems.

[\[2\]](#_ftnref2) Important concepts are _italicized_ and each word is in **_bold_** for the first time of occurrence.
