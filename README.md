# Dependency Injection And Dagger

## What is Dependency Class and Dependency Injection?

### What is Dependency?
In very simple words, If class A needs instance of class B to function, then B is called a dependency class for class A.

### What about Dependency Injection?
Generally every developer has been creating instances of Dependency Classes inside the Dependent Classes, from start which works absolutely fine. But at large scale project it can be very inefficient in terms of configurability, testing memory and performance. (We will see this in Design Approach 1). Using Dependency Injection concept we delegate task of generating and managing instances of Dependency Classes to an External Agent like Dagger, HILT or KOIN. 

Let us see the following Graph:
![](dependenyExamples.png)

In this context and project, MyApplication, the MainActivity class depends on the MainViewModel class, which in turn depends on the DatabaseService and NetworkService classes. The DatabaseService class requires a database name and version number, and the NetworkService class requires an API key. These classes are known as dependency classes because they are required by the dependent classes that depend on them.

There are several approaches to providing instances of these dependency classes to the dependent classes that need them. These approaches include:

    Approach 1: The dependent class creates its own instances of the dependency classes it requires.

    Approach 2: Inversion of Control (IOC), where the instances of the dependency classes are created first 
                and then provided to the dependent classes.
    Approach 3: An external agent, known as a Dependency Component class, creates the instances of the 
                dependency classes.
    Approach 4: Using Dagger, a framework for dependency injection.

Each of these approaches has its own pros and cons, and we will examine them in more detail to determine the best approach for our project.

## Design Approach 1 (Without Dagger Or Any Dependency Injection Framework)
See the following Graph:

![](WithOutDaggerApproach1.png)

In this first design approach we will discuss is creating instances of dependencies directly within the dependent class. This is a common approach that is often taken without realizing it. However, this approach has several drawbacks.

    1. Configurability: With this approach it is very hard to configure it at times. What do I mean? let's 
       see the graph. The database name, version, api key have been hardcoded when  the instance of them was
       created by Dependent classes. Now if we have different DB and resaource for development stage and production
       stage then to change them at production it will be very hard. Without going down all the hierarchy it 
       will not be possible.
    2. Expensiveness: Some instances are very expensive in terms of memory, for exapmle Database Service and
       Network Service. We would like to make as less instances as possible for them. Also, we would like to
       create them only when it needeed and we would like to preserve them as long as possible. In this approaach
       it will not be possible. If there are so many instances in the heap that have not been used then Garbage 
       collector will hae to run very frequently. Running GC frequently is bad for app performance as it runs on 
       MainThread and if it run on MainThread frequently, it will hinder the Mainthread other important tasks, 
       such as rendering the UI elements, navigation, data fetching and so on.
    3. Difficult To Test: (Will add more on this when I get more clarity)
    
I don't have experience with Testing so writing about this will not be very clear, though I have asked CHATGPT
and here is the answer.
    **ChatGPT answer on how DI makes code more testable:**

    Dependency injection is a design pattern that allows a software component to receive its dependencies from
    an external source rather than creating them itself. This can make testing easier in several ways:

    Isolation of dependencies: By injecting dependencies into a class, rather than creating them within the 
    class, it becomes easier to isolate the class from its dependencies during testing. This allows you to 
    focus on testing the behavior of the class itself, rather than the behavior of its dependencies.

    Mocking of dependencies: During testing, you may want to use mock versions of certain dependencies in 
    order to test the behavior of your class under different conditions. Dependency injection makes it easier 
    to substitute mock versions of dependencies, because you can simply pass them in as arguments when creating 
    the class.

    Better test coverage: Dependency injection can make it easier to test a class in a variety of different 
    configurations, because you can easily switch out different implementations of dependencies. This can help 
    you achieve better test coverage and find potential issues earlier in the development process.

    Overall, dependency injection can help you create more maintainable and reliable tests by allowing you to 
    focus on testing the behavior of individual components in isolation, rather than the interactions between 
    components.

Now we will see how we try to solve these challenges with different approaches and ultimately with a DI framework, Dagger, at the end.
## Design Approach 2 (Without Dagger)
Let us improve our previous design approach 1, little bit to overcome some drawbacks. In design approach 1, code was hard to configure because instances of dependency classes were being created from top to down and it was hard to change API name, database name or version. But what if we decouple and break this hierarchy. See the below picture.
![](WithOutDaggerApproach_1_2.png)

In this approach, first we will create string objects for API key, database name and an int variable for version. Similarly instances of dependency classes will be created first in Bottom up approach. See fig 2.
**This is called IOC, Inversion Of Control**. 

Points to be noted.
```
   1. Here instances of dependency classes are not being created inside the dependent class. But
      are being created first outside then it is being provided to Dependent Classes. This is the rule
      of IOC.
   
   What problem does it solve?
   -> Now it will be easy to change database name, api key, version from outside without having to
   getting into the mess of the hierarchy.
   
   But, still 
   - Instances are not being handled in a very smart way.
   - Database service and Network Service class are expensive classes and in this approach will be destroyed
   and recreated again and again with Activity cycle. Also there can be many different instances for these classes.

``` 
**How this approach will be implemented in the project?**
See the picture below:
![](WithOutDagger_Approach_2.png)

Conclusion and Drawbacks:
1. We have created instances of expensive dependency classes, Database Service and Network Service class, in MyApplication class. So with MainActivity they will not get destroyed and system and GC will save a lot of work.
2. But still, MainViewModel class, a dependency class, is being created inside its Dependent class that is against its IOC rule. Also even Database Service and Network Service class are not totally being created independently.

## Initial Stage Of the Project
Here in the initial stage, the dependency tree is like: 
>>MyApplication --> MyActivity --> MainViewModel --> NetworkService,DatabaseService.

In this approach, the MyApplication class is using an external agent called the DependencyComponent class to create instances of the NetworkService and DatabaseService classes. These instance are provided to MainViewModel constructor to create MainViewModel instance, that has to be provided to the MainActivity. This approach is not using Dagger, but it is attempting to mimic the functionality of Dagger for creating dependency class instances.
   
## Getting Rid Of DependencyComponent Class And Introducing Dagger Framework
Using an external component like the DependencyComponent class can help us create and provide instances of our dependency classes, but it can be time-consuming to do so manually. To make this process more efficient, we can use a dependency injection framework like Dagger, Dagger-Hilt, or KOIN. These frameworks provide us with tools and mechanisms to manage and inject dependencies more efficiently and effectively.

We are going to use Dagger here. To use Dagger the first step is to add the following Dependencies.
 ```
  // Dependency injection
   
   implementation "com.google.dagger:dagger:2.21"
   
   annotationProcessor "com.google.dagger:dagger-compiler:2.21"
  ```


## Architecture Of DAGGER
  ![](DaggerArchitecture.png)