- Title: Standard Prototyping Facilities
- Date proposed: yyyy-mm-dd
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary
Towards an official implementation of prototypes as pseudo-classes.

# Motivation

Prototyping is in SC a wide-spread technique, with a long history. It provides a way to model objects in real-time (pseudo-classes), without the need to recompile the class library and thus losing all of the application's state.

It is currently implemented by exploiting Environment's doesNotUnderstand method, and easily performed by manipulating Events. However, working with the current official prototyping infrastructure has some problems:
- difficult code organization: prototypes are individual objects (Events) which gets commonly stored in global variables, thus potentially creating confusion implementing inheritance, composition, and saving/loading them to/from files across projects
- even more cumbersome debugging because prototypes' methods all appear as anonymous functions in stack traces, thus becoming identifiable only by looking at which variables are defined in which method.
- if using Events, clashes with normal Event methods: e.g. `.play` can't be defined by a prototype because it is already defined for Events, even if a prototype doesn't necessairly need the same play functionality Events have.

I am personally aware of three approaches to Prototyping:
- Alberto De Campo is writing about it in the SuperCollider book. His style works directly with Events, stored in global variables. Methods are appended directly to the event, and thus have the event itself as the first argument in their definition. A simple typical example could look like: `q = q ? (); q.myMethod = {|q, arg1,arg2| [arg1,arg2].postln }; q.myMethod('a','b');`
- James Harkins' Prototype class, in ddwPrototype quark. 
- my own ProtoDef quark, which I developed to add functionalities to Alberto De Campo's approach (I was completely unaware of ddwPrototype). I'm also used to q.method syntax and methods having 'q' as their first argument

While a variety of approaches is a value in itself, in this case it looks like we could be reinvent the same wheel with slightly different details and names, thus putting unnecessary barriers to code communication and sharing.

# Specification

A discussion is required, toward a common implementation of new prototyping features.

Candidates list for missing prototyping features:

- a global register for prototypes as the one we have for, e.g., Ndefs. This allows calling prototypes by name, helping code organization, reuse, inheritance and save/load from files.
- something like `init` and `super` calls for prototypes. `init` should be called automatically when the prototype is first instantiated, and `super` would just be a shortcut for calling parent prototype's methods
- on error: since prototype methods all look like anonymous functions in stack traces, at least a message printing which prototype method(s) failed
- decoupling prototypes from Events, to avoid clashes, and derive them directly from Environments

# Drawbacks

- performance: prototyping adds some extra calls to every prototype method call (e.g. doesNotUnderstand)
- focus: maybe it would be best to focus efforts into making a runtime re-compilable class library
- an official Prototype class could break code for people who already developed their own version of it.

# Unresolved Questions
- Are there other implementation and approaches than the ones listed above?
- Do we need an official Protoype class? I think we do.
