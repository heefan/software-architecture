# Dependency Inversion Principle

IoC: Inversion



















Dependency relationship:  
[UML Denpendency Explaination](https://www.uml-diagrams.org/dependency.html)

A --> B
wrong: B class has a dependency on A class.
right: A class depends on B class.

If A class depends on B class, the inversion will change it as B class depends on A class. 

A <-- B

> Misunderstanding: 
> 1. Dependency Inversion Principle => SOLID Principle
> 2. Dependency Injection 


> **Dependency Inversion Principle**
>
>1. High-level module should not depend upon low-level modules; instead they should depend upon abstractions
>2. low-level modules too should depend upon abstractions


1. who is high-level module and who is low-level module? 

```swift
struct GithubRepo { }


class CoreDataStack {  
  func loadGithubRepos() -> [GithubRepo] {
    // fetch from core data and return the list of github repos
  }
}

class GithubRepositoryLoader { 
  private var coreDataStack: CoreDataStack
  
  init(coreDataStack: CoreDataStack) {
    self.coreDataStack = coreDataStack
  }
  
  func loadRepo() -> [GithubRepo]  {
    return coreDataStack.loadGithubRepos()
  }
}
```

Why the code is bad? 

High level module (GithubRepositoryLoader) depends on loww level module (CoreDataStack)

GithubRepositoryLoader -->  CoreDataStack

GithubRepositoryLoader -->  Abstruction (protocol) <-- CoreDataStack

```swift
struct GithubRepo { }

protocol GithubRepoLoader {
  func loadRepos() -> [GithubRepo]
}

class GithubRepositoryLoader {
  private var loader: GithubRepoLoader
  
  init(loader: GithubRepoLoader) {
    self.loader = loader
  }
  
  func loadRepo() -> [GithubRepo]  {
    return loader.loadRepos()
  }
}
```

```swift
class CoreDataStack: GithubRepoLoader {
  func loadRepos() -> [GithubRepo] {
    // fetch from core data and return the list of github repos
  }
}
```