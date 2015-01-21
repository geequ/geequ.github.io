---
layout: post
title: Solid principles
categories: oop
---

Way to achieve good architecture of the application is create boundaries that separate it from external details such as framework, UI or database. And SOLID principles may us help managing dependencies and focusing us to our model.

But, what kind of help it is? These principles can helping us to find some bad design smells. In short way we have five sort of these:

* **rigidility** - is the tendency of a system to be a hard to change
* **fragitility** - a system is fragile when a small change in one module causes other unrelated modules to misbehave
* **immobility** - a system is immobile whe it's internal components cannot easily extracted and reused in new environments
* **viscosity** - a system is viscous when it is hard to perform necessary operations. Long build and test times are a source of viscosity, because they impose a high cost on necessary operations
* **needles complexity** - one of the most common issues that arise in discussions of software design is how to deal with the feature. Should we design for the current requirements only, or should we design a system that anticipates future requirements.

And we have also five principles comes from acronym SOLID:

1. **S**ingle Responsibility Principle
2. **O**pen/Closed Principle
3. **L**iskov Substitotion Principle
4. **I**nterface Segregation Principle
5. **D**ependency Inversion Principle