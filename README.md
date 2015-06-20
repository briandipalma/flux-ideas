# Functional flux

As discussed in Dan Abramov's
[the evolution of flux frameworks](https://medium.com/@dan_abramov/the-evolution-of-flux-frameworks-6c16ad26bb31)
the latest crop of flux libraries tend to share an interesting mutation on the original pattern. Their stores
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

Let's specify how an application or component built on functional flux principles behaves by breaking the system down
into its constituent parts and analyzing them.

## View

The view layer is assumed to be React, it doesn't have to be but it's probably the best choice for the job.

There are two major types of components in the React world.

 * The generic, pure functional ones. These are provided almost all the data they need to render correctly by their
parent components. They are configured via `props` passed in by the parent and would not have any coupling to data
sources outside their component scope. They have no notion of a domain specific store/data provider. For example a
button, or an autocomplete input. They tend to be generic and therefore easy to reuse.

 * The ones coupled to a specific store or backing data structure. In flux they are called controller-views.

## State tree

You cannot use a flat array or a simple flat-ish object (one or two levels of data properties) to store the state for a
complex application or component. Such an approach won't scale. Complex UIs are trees of components each a reflection
of their state. The backing datastructure used to store the app or component state should acknowledge this reality.
Like many datastructure problems in computer science the answer is to use a tree.
