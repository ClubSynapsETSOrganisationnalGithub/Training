<center>

Framework Guide
========================

******

</center>


# Our Goal
The framework emerge when we felt the need to have helpful tools to handle common and recurrent design pattern with Embedded C. Framework offer purely software implementation in [oriented-object](./object-oriented.md) base. We provide sub framework for hardware abstraction, for event-driven architecture, [state-machine](implementation/state-machine.md) and many more..
Framework will help the developer design his firmware respecting the [SOLID principles](solid-principles.md).

Popular framework like [.NET](https://dotnet.microsoft.com/) or [Qt](https://www.qt.io/) are design to help produce Graphic User Interfaces and any PC or phone application. Our framework is design to help create Embedded-C firmware.

All the modules created are unit-tested with a [Test-Driven](test-driven.md) approach. 

# Get Started with Framework
Framework offer [code generating](framework-guideline.md) script to help the user start with creating objects and interfaces.

To create an interface simply call `./modgen.sh`.
Let's say we want an abstract object called hammer. We execute the following:
```dotnetcli
./modgen.sh abstract -d src/hammer/ -m Hammer
```
This will create the module following the object-oriented [3-files strategy](object-oriented.md).

if we need an object that inherit from hammer we do:
```dotnetcli
./modgen.sh concrete -d src/hammer/ -m HammerConcrete -s Hammer
```

All generated module will be accompanied with their test file. We strickly follow TDD and it's imperative that the design of the module is test-driven.

