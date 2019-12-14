# Basic Programming Styles

> Borrowed from [Style guides](https://github.com/bkuhlmann/style_guides)

> *If you don’t actively fight for simplicity in software, complexity will win...and it will suck.*
> -- Henrik Joreteg

<!-- Tocer[start]: Auto-generated, don't remove. -->

## Table of Contents

  - [General Rules](#general-rules)
  - [Architecture](#architecture)
  - [Sandi Metz Rules](#sandi-metz-rules)
  - [Comments](#comments)
  - [Bare Words](#bare-words)
  - [Naming](#naming)
  - [Messages and Methods](#messages-and-methods)
  - [Command-Query Separation (CQS)](#command-query-separation-cqs)
  - [Tell Don't Ask](#tell-dont-ask)
  - [Pluggable Selector](#pluggable-selector)
  - [Law of Demeter (LoD)](#law-of-demeter-lod)
  - [S.O.L.I.D.](#solid)
  - [T.R.U.E.](#true)
  - [Recognizing Dependencies](#recognizing-dependencies)
  - [Inheritance](#inheritance)
  - [Composition](#composition)
  - [Duck Types](#duck-types)
    - [Template Method Pattern](#template-method-pattern)
    - [Hook Method Pattern](#hook-method-pattern)
  - [Null Objects](#null-objects)
  - [Primitive Obsession](#primitive-obsession)
  - [Self-Saving Perils](#self-saving-perils)
  - [Testing](#testing)
    - [Mocks](#mocks)
    - [Stubs](#stubs)
  - [Security](#security)
  - [Resources](#resources)

<!-- Tocer[finish]: Auto-generated, don't remove. -->

## General Rules

- Prefer spaces over tabs (i.e. soft tabs).
- Avoid trailing whitespace.
- Remove spaces from blank lines.
- End every file with a blank new line.
- Prefer small methods/objects - They are easier to read, maintain, and build upon.
- Prefer duplication over the wrong abstraction - Duplication is much cheaper to fix than
  refactoring the wrong abstraction. Allow duplication up to three times, as a maximum, before
  refactoring.
- Write tests to demonstrate passing and failing requirements.

## Architecture

- Be focused on ephemeralization. In other words, "do more and more with less and less until
  eventually you can do everything with nothing." Another way to think about this is: "It seems that
  perfection is reached not when there is nothing left to add, but when there is nothing left to
  take away" -- Antoine de Saint Exupéry. By default, architect software to be minimalisitic in
  nature. Constantly strive to remove that which you don't need. It takes maintenance and discipline
  to see this through but will produce a well tuned system that is easier to maintain and
  understand. For more on this, read:
  [In Pursuit of Production Minimalism](https://brandur.org/minimalism).

## Sandi Metz Rules

1. Classes must not be more than one hundred lines of code.
1. Methods must not be more than five lines of code.
1. Methods must not accept more than four parameters (this includes hash options).
1. Controllers must instantiate only one object. Therefore, views can only know about a single
   instance variable and should only send messages to that object.

## Comments

- Avoid commenting out unused code. Always delete the code and use your Git history to restore later
  if need be.

## Bare Words

- Bare words are lowercase words with no special modifier in front of them. Example:
    - example = This is a bare word which is also a method.
- The following are NOT bare words but introduce scope limitations (using Ruby syntax but applicable
  to other languages):
  - $example = Global variable
  - @@example = Class variable
  - @example = Instance variable
  - EXAMPLE = Constant variable
- It is good design to use bare words as they allow for greater flexibility when
  enhancing/refactoring future code due to fewer scope limitations/complexities.

## Naming

Naming objects, methods, etc. can be hard. It requires a strong understanding of the problem space
before a good name can be derived. Here are some guidelines worth following:

- Prefer short, specific, and honest names when possible. You want a name that conveys what the code
  does without having to read the code to grasp its meaning.
- Prefer long, descriptive names when the problem space isn't fully understood. This will unblock
  you so you can move forward while figuring out the code and the problem space. The long name can
  be renamed later, after you've acquired more understanding when refactoring.
- Prefer a name that *describes what it does and not what it is*. A name that describes what it does
  restricts it to a single responsiblity and prevents expanding beyond its original meaning.
- Prefer using a `_list` suffix, for example, as the plural form of a variable name. This improves
  readability and reduces debugging hazards due to a one character difference. Using Ruby syntax,
  here is an example:

      # No
      lines.map.with_index {|line, index| "#{index}: #{line}"}

      # Yes
      line_list.map.with_index {|line, index| "#{index}: #{line}"}

- Avoid obvious words given a variable or parameter's type. Examples:
  - Avoid prefixing/suffixing a variable/parameter with "String" if you can see that it is a string.
  - Avoid method names that include the type when the type is supplied. For instance,
    `#sort(orders)` is better than `#sort_orders(orders)`.
- Avoid words that disambiguate (when in doubt, leave extraneous words out). Examples:
  - `#report` is better than `#autogenerated_important_report`.
- Avoid words used in the surrounding context. Examples:

        # No
        class Order
          def order_payment
          end
        end

        # Yes
        class Order
          def payment
          end
        end
- Avoid words that don't clarify meaning (remove the word if it doesn't help clarify/identify the
  object). Examples: manager, service, etc.

## Messages and Methods

- **Message** - A name for a responsibility an object may have. In other words, you send a message
  to a collaborator (i.e. object) not a method.
- **Method** - A named, concrete, implementation for which a responsibility may be fullfilled. There
  can be many methods for a single responsibility. There are four parts to a method:
  - Collect Input
  - Perform Work
  - Deliver Output
  - Handle Failures

## Command-Query Separation (CQS)

- Specifies methods should be categorized as follows:
  - **Queries**: Answer a result without changing the observable system state (i.e. no side side
    effects). Also means that queries can be chained (in any order) without consequences.
  - **Commands** (a.k.a. modifiers/mutators): Change the system state but do not answer a result.
- Caveats:
  - Adhering strictly to this principal makes popping values off a collection, for example,
    cumbersome because two steps are involved to answer the value to be removed AND changing the
    state of the collection. Being able pop a value off a collection (which answers the value and
    changes state) in one step can be convenient.

## Tell Don't Ask

- As mentioned in the CQS caveats, above, it is best to tell an object what to do rather than ask it
  to do something. This allows objects to couple behavor with the data being manipulated (i.e.
  commands). However, there are many situations where it useful to query an object for information
  as well.
- Further reading:
  - [Tell Don't Ask](http://martinfowler.com/bliki/TellDontAsk.html) - Martin Fowler.
  - [Converting Queries to Commands](https://michaelfeathers.silvrback.com/converting-queries-to-commands) - Michael Feathers.

## Pluggable Selector

- Defines an object (to be initialized) or a method which accepts an object and method, as
  arguments, to be messaged.
- Allows for different objects, of similar behavior, to be dynamically plugged in.
- Allows for highly customizable code but introduces additional levels of indirection and
  complexity.

## Law of Demeter (LoD)

- Assume no knowledge of an object's internal structure.
- A method can call other methods in its class directly.
- A method can call methods on its own fields directly (but not on the fields' fields)
- When a method takes parameters, the method can call methods on those parameters directly.
- When a method creates local objects, that method can call methods on the local objects.
- Chained messages can be sent to a target of similar types:
  - Example: "example string".strip.upcase.reverse (i.e. string --> string --> string --> string)
- Chained messages should not be sent to the target of different types:
  - Bad: task.owner.full_name. (i.e. task --> owner --> string)
  - Good: task.owner_full_name.

## S.O.L.I.D.

Represents for the following principals:

- [Single Responsibility Pattern (SRP)](https://en.wikipedia.org/wiki/Single_responsibility_principle) - A
  class/module/method should only have one, and only one, reason to change.
  - Should do the smallest possible useful thing.
  - Should be explainable in a sentence (it is code smell if the word "and" is used in a sentence).
  - Everything should be highly related to a single purpose.
  - Does not allow extraneous responsibilities to leak in.
  - Isolation allows change without consequence and reuse without duplication.
- [Open/Closed Principle (OCP)](https://en.wikipedia.org/wiki/Open/closed_principle) - A
  class/module/method should be open for extension, but closed for modification.
- [Liskov Substitution Principle (LSP)](https://en.wikipedia.org/wiki/Liskov_substitution_principle)
  - An object should be replaceable via a subtype without altering/breaking the correctness/contract
  of the supertype.
- [Interface Segregation Principle
  (ISP)](https://en.wikipedia.org/wiki/Interface_segregation_principle) - An object should not be
  forced to depend upon interfaces it does not use.
- [Dependency Inversion Principle (DIP)](https://en.wikipedia.org/wiki/Dependency_inversion_principle)
  - High-level modules should not depend on low-level modules. Both should depend on abstractions.
  - Abstractions should not depend on details. Details should depend on abstractions.

## T.R.U.E.

T.R.U.E. code not only meets today's needs but can also be changed to meet the needs of the future.

- **Transparent** - The consequences of change should be obvious in the code that is changing and in
  distant code that relies upon it.
- **Reasonable** - The cost of any change should be proportional to the benefits the change
  achieves.
- **Usable** - Existing code should be usable in new and unexpected contexts.
- **Exemplary** - The code itself should encourage those who change it to perpetuate these
  qualities.

## Recognizing Dependencies

An object has a dependency when it knows:

- The name of another class.
  - Solution: Dependency Injection. Inject an instance of the object via an argument rather than
    hard code the class/instance within the calling object.
- The name of a message that it intends to send to someone other than self.
  - Solution: Isolate external messages to a single method which can be messaged within the class.
    If anything changes with sending messages to the external object, only the one method needs to
    be refactored.
- The arguments that a message requires.
  - Solution: Use a hash as a single argument which can then set default values possibly making the
    argument list optional.
- The order of those arguments.
  - Solution: Use a hash as a single argument. Allows arguments to be passed in any order. If not a
    single hash then possibly use a required argument with a hash as the last argument for
    additional/optional arguments.

The goal is to keep as few dependencies as possible so that a class knows enough to do it's job and
nothing else.

## Inheritance

- Defines an *is-a* relationship.
- Used for specialization, not for sharing code.
- Defines a parent/child relationship where behavior defined in the superclass is
  inherited/overwritable by the subclass.
- Also known as automatic message delegation whereby messages received by the subclass automatically
  bubble up to the superclass when not found in the subclass.
- Things to do:
  - Code defined in the abstract/super class should always apply to all subclasses.
  - Each subclass must conform to the superclass interface and implement expected behavior.
  - When subclassing, stick to shallow/narrow or shallow/wide hierarchies as they are easier to
    maintain. Deep hierarchies that are either narrow or wide are harder to maintain, difficult to
    understand, and are best avoided.
- Things to avoid:
  - Avoid calling *super* from the subclass. Use hook methods instead.
  - If an object uses a variable to determine the type/category of a message to send, then the
    inherited code has not been properly abstracted. Classic inheritence can solve this problem by
    creating an abstract class from which subclasses can extend to define specialized behavior.
  - If the sending object has to check the class of the receiving object, then a duck type has been
    missed. It is better to extract this behavior into a common interface for which all subsequent
    objects can inherit the same behavior from.
  - Don't allow a subclass to throw an exception when overridding a superclass method because it
    doesn't need/want to implement it. One should question whether it is even a sublcass at that
    point or whether inheritance is the correct solution.

## Composition

- Defines a *has-a* relationship.
- Defines an object that is composed of many objects which exhibits behavior that is separate from
  and includes the behavior of the sum of its parts.
- Composed of objects of smaller well-defined behavior, the composed object benefits from the
  greater sum of its parts but can become hard to manage should the number of parts grow to a large
  size.
- Uses dependency inversion to inject an object which can play the role of the behavior that varies.

## Duck Types

- Defines a *behaves-like-a* relationship.
- Defines an object that behaves idential to objects of different types due to methods and/or
  properties that enable this behavior.

### Template Method Pattern

- Basic object structure is defined by the superclass but overwritable by the subclass.
- Methods defined in the subclass provide customized behavior which the superclass will message.
- Useful for initializing similar objects with different default behavior.
- Imposes sublcass requirements that is not obvious but can be alleviated by defining default
  methods in the superclass that throw NotImplementedError exceptions. NOTE: These exceptions should
  explain themselves by indicating why the exception was thrown via a useful error message. Example:

        fail NotImplementedError, "The method, #method, is not implemented yet."

### Hook Method Pattern

- Allows customized subclass method behavior without needing to know the abstract algorithm used by
  the superclass.
- Each hook method is defined by the superclass but overwrittable by the subclass.
- Most importantly, hook methods remove the need for subclasses to call the super method and reduces
  tight coupling between the subclass and superclass. Example:

        class Vehicle
          def initialize options = {}
            after_initialize options
          end

          def after_initialize options = {}
          end
        end

        class Truck < Vehicle
          def after_initialize options = {}
            options.reverse_merge! wheel_size: 10
          end
        end

## Null Objects

- Prefer creating objects which implement the expected behavior but doesn't answer `null`. It should
  behave as a pass through which does nothing or the expected type should be answered (be it a safe
  default value or empty).
- Null objects should stand in the place of `null` in order to increase preditability and reduce
  unexpected side-effets.
- Remember: Nothing is always something -- Identifying what the nothing is and giving it a name is
  better than letting nulls permeate throughout the code base.

## Primitive Obsession

- Avoid using primitive data types (i.e. integer, string, array, etc.) to represent domain
  knowledge. The following are signs of primitive obsession:
  - When primitives are used instead of value objects. Good, correctly typed, value objects might
    be: version, currency, units of measure, etc.
  - When constants or class methods are used to denote *special* values.
  - When booleans are used as inputs to functions/methods (as the generally means you'll need
    conditional logic to process the boolean input).
  - When booleans are returned as the result of a function/method. Means the caller will need to use
    conditional logic to process the response.
- Use value objects to denote special types and avoid primitive obsession.

## Self-Saving Perils

- First and foremost, don't allow objects to self-save.
- Business logic and persistence shouldn't mix within the same object.
- Allowing objects to self-save leads to many of the following problems:
  - Inability to test an object in isolation.
  - Validations that don't work in all scenerios (the use of conditional logic is a strong
    indicator).
  - Before, after, etc. hooks that cause unexpected consequences (leads to complicated tests).
  - Failures when processing transactions and related after effects.
  - Implicit order of events that aren't realized until much later.
  - Slow tests due to needless saving an object multiple times.

## Testing

- Always test the interface, not the implementation - Avoid testing the private/protected methods of
  the implementation via assertions/expectations. These will be be tested, indirectly, via the
  public interface. This will also make it easier to refactor in the future.

### Mocks

- Avoid mocking the object under test.
- Only mock what you own. In cases where objects exist that talk to external APIs (for example)
  switch to stubbing instead.
- Prefer mocks when unit testing an object under test that needs to message other objects. These
  mock objects can be injected and expectations can be set to ensure they are messaged properly. The
  actual unit testing of these mock objects should be provided in their own unit tests. Switch to
  integration tests when needing to test the communication between all the objects as a whole.

### Stubs

- Avoid stubbing objects being tested.

## Security

- Use generic/user friendly error messages when an exception occurs in order to provide a good user
  experience. Avoid showing stack traces or internal error messages via your API or UI. Make sure
  these situations are accounted for otherwise you risk exposing information to those who might wish
  to infiltrate and harm your system.

## Resources

- [Sandi Metz' Rules For Developers](http://robots.thoughtbot.com/sandi-metz-rules-for-developers)
- [Practical Object-Oriented Design in Ruby: An Agile Primer](http://www.poodr.com) by Sandi Metz
- [Keep Code Small](https://www.youtube.com/watch?v=8bZh5LMaSmE) by Sandi Metz
- [Nothing is Something](https://www.youtube.com/watch?v=9lv2lBq6x4A) by Sandi Metz
- [The Magic Tricks of Testing](https://www.youtube.com/watch?v=URSWYvyc42M) by Sandi Metz
- [Command-Query Separation](http://martinfowler.com/bliki/CommandQuerySeparation.html)
- [Ruby Tapas - Barewords (Episode 4)](http://www.rubytapas.com)
- [Ruby Tapas - Message and Method (Episode 11)](http://www.rubytapas.com)
- [Ruby Tapas - The End of Mocking (Episode 52)](http://www.rubytapas.com)
- [Ruby Tapas - Self-Save Part 1 (Episode 402)](http://www.rubytapas.com)
- [Ruby Tapas - Self-Save Part 2 (Episode 403)](http://www.rubytapas.com)
- [Ruby Tapas - Self-Save Part 3 (Episode 404)](http://www.rubytapas.com)
- [Ruby Tapas - Self-Save Part 4 (Episode 405)](http://www.rubytapas.com)
- [Ruby Tapas - Plural Name (Episode 437)](http://www.rubytapas.com)
