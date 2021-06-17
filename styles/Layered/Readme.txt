In this approach, the system is decomposed into a number of higher and lower layers , and each layer has its own sole responsibility in the system.

Each layer consists of a group of related classes that are encapsulated in a package, in a deployed component, or as a group of subroutines in the format of method library or header file.

Each layer provides service to the layer above it and serves as a client to the layer below i.e. request to layer i +1 invokes the services provided by the layer i via the interface of layer i. The response may go back to the layer i +1 if the task is completed; otherwise layer i continually invokes services from layer i -1 below.

Closed Layered
A closed layer means that as a request moves from layer to layer, it must go through the layer right below it to get to the next layer below that one.

Open Layered
Meaning requests are allowed to bypass this open layer and go directly to the layer below it. 

Micro Kernel
The microkernel architecture pattern consists of two types of architecture components: a core system and plug-in modules. Application logic is divided between independent plug-in modules and the basic core system, providing extensibility, flexibility, and isolation of application features and custom processing logic.
Perhaps the best example of the microkernel architecture is the Eclipse IDE. Downloading the basic Eclipse product provides you little more than an editor. However, once you start adding plug-ins, it becomes a highly customizable and useful product.

An example is a task scheduler. The microkernel could contain all the logic for scheduling and triggering tasks, while the plug-ins contain specific tasks. As long as the plug-ins adhere to a predefined API, the microkernel can trigger them without needing to know the implementation details.

Another example is a workflow. The implementation of a workflow contains concepts like the order of the different steps, evaluating the results of steps, deciding what the next step is, etc. The specific implementation of the steps is less important to the core code of the workflow.

DDD Layered
Domain-Driven Design is an approach to software development that centers the development on programming a domain model that has a rich understanding of the processes and rules of a domain. 

Hexagonal
The idea behind it is to put inputs and outputs at the edges of your design. In doing so, you isolate the central logic (the core) of your application from outside concerns. Having inputs and outputs at the edge means you can swap out their handlers without changing the core code. 

we do have layers, but the inner layers are the abstractions, while the other layers are the actual implementation.
 
Inversion of control, To ensure the isolation of the Hexagon, the dependencies on downstream layers have been inverted.


