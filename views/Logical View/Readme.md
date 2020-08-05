
* Describe the application architecture;
* Annotate the pictorial to illustrate where application functionality is executed.
* Can the application tiers be separated on different machines?
* Layers represent a logical grouping of components. For example, use separate layers for user interface, business logic, and data access components.
* Components within each layer are cohesive. For example, the business layer components should provide only operations related to application business logic.

# Coupling and Cohesion
* Application is partitioned into logical layers.
* Layers use abstraction through interface components, common interface definitions, or shared abstraction to provide loose coupling between layers.
* The components inside layers are designed for tight coupling, unless dynamic behavior requires loose coupling.
* Each component only contains functionality specifically related to that component.
* The tradeoffs of abstraction and loose coupling are well understood for your design. For instance, it adds overhead but it simplifies the build * process and improves maintainability.

