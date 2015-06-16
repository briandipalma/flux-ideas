# Flux ideas
As discussed in Dan Abramov's
[the evolution of flux frameworks](https://medium.com/@dan_abramov/the-evolution-of-flux-frameworks-6c16ad26bb31)
the latest crop of flux libraries are tending to share an interesting mutation on the original pattern. Their stores
are stateless. In the original flux pattern stores contained the domain logic and state. They would listen to actions,
and if the action was relevant to the store their logic would mutate their state and emit change events.

In the latest flux implementations stores only contain their domain logic, ideally they become a collection of pure
functions. The state that used to be local to a store is now in a shared data tree. State that was once spread out over
multiple stores is now centralized. This allows the implementation of some powerful features. If you are familiar with
Om you will probably be aware of the efficient undo abilities that it's centralized state atom provides it. There are
other benefits such as time-travelling debugging, easy serialization and introspection of the system state.

Centralizing state also makes it easier to hot reload code and replay stored user actions through the system when using
the newly loaded code. It would allow the developer to record the series of actions required to trigger a bug. Fix the
bug by modifying the offending code and have the build tools automatically reload the new code, reset the state to its
initial value and replay the saved actions.

It is also easier to reason about and test pure functions as opposed to stateful stores. This approach also deals with
server side rendering issues as previous requests state isn't stored in the stores instead each request is provided
with a clean state tree.
