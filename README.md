# Dagger Various Implementations

  ## Initial Stage Of the Project
Here in the initial stage, the dependency tree is like: 
>>MyApplication --> MyActivity --> MainViewModel --> NetworkService,DatabaseService.
    
In this approach what we are doing is we are creating instances of NetworkService and DatabaseService class in the MyApplication
class but not exactly in the MyApplication class. It is actually delegating this work to an External Agent called 
DependencyComponent class. It has 2 inject methods that will create those instances including MainViewModel instance 
and provide it to MainActivity. This is not Dagger but it is trying to imitate functionality of Dagger for creating 
Dependency classes instances.
   
 ## Getting Rid Of DependencyComponent Class And Introducing Dagger Framework
  We have seen how using an External Component called DependencyComponent Class we can create and provide instances. But we 
  will use Dagger instead. Why? Providing instances of Dependency classes is a tedious work and to do this efficiently we are 
  provided with many Dependency Injection Frameworks like Dagger, Dagger-Hilt, KOIN and so on.

  We are going to use Dagger here. To use Dagger the first step is to add the following Dependencies.
  >>// Dependency injection
  > 
  > implementation "com.google.dagger:dagger:2.21"
  > 
  > annotationProcessor "com.google.dagger:dagger-compiler:2.21"
 ## Architecture Of DAGGER
  ![](DaggerArchitecture.png)