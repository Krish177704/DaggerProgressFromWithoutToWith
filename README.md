# Dependency Injection And Dagger

## What is Dependency Injection?
Let us see the following Graph:
![](dependenyExamples.png)

In this context and project, MyApplication, the MainActivity class depends on the MainViewModel class, which in turn depends on the DatabaseService and NetworkService classes. The DatabaseService class requires a database name and version number, and the NetworkService class requires an API key. These classes are known as dependency classes because they are required by the dependent classes that depend on them.

There are several approaches to providing instances of these dependency classes to the dependent classes that need them. These approaches include:

    Approach 1: The dependent class creates its own instances of the dependency classes it requires.
    Approach 2: Inversion of Control (IOC), where the instances of the dependency classes are created first and then provided to the dependent classes.
    Approach 3: An external agent, known as a Dependency Component class, creates the instances of the dependency classes.
    Approach 4: Using Dagger, a framework for dependency injection.

Each of these approaches has its own pros and cons, and we will examine them in more detail to determine the best approach for our project.

## Design Approach 1 (Without Dagger Or Any Dependency Injection Framework)
See the following Graph:

![](WithOutDaggerApproach1.png)

In this first design approach we will discuss is creating instances of dependencies directly within the dependent class. This is a common approach that is often taken without realizing it. However, this approach has several drawbacks.

    1. Configurability: With this approach it is very hard to configure it at times. What do I mean? let's see the graph. The database name, version, api key have been hardcoded when  the instance of them was created by Dependent classes. Now if we have different DB and resaource for development stage and production stage then to change them at production it will be very hard. Without going down all the hierarchy it will not be possible.
    2. Expensiveness: Some instances are very expensive in terms of memory, for exapmle Database Service and Network Service. We would like to make as less instances as possible for them. Also, we would like to create them only when it needeed and we would like to preserve them as long as possible. In this approaach it will not be possible. If there are so many instances in the heap that have not been used then Garbage collector will hae to run very frequently. Running GC frequently is bad for app performance as it runs on MainThread and if it run on MainThread frequently, it will hinder the Mainthread other important tasks, such as rendering the UI elements, navigation, data fetching and so on.
    3. Difficult To Test: (Will add more on this when I get more clarity)
Now we will see how we try to solve these challenges with different approaches and ultimately with a DI framework, Dagger, at the end.
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