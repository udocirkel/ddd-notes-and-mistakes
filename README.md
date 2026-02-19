# Domain-Driven Design (DDD) – Personal Notes

This repository contains my personal notes on **Domain-Driven Design (DDD)**.

DDD keeps showing up in my work – in different forms, maturity levels, and interpretations.  
These notes evolve over time, shaped by practical experience, reflection, and real-world projects.

I am fully aware that there are far more comprehensive and authoritative DDD resources available
(e.g. books, blogs, and material from the DDD community / DDD Crew).

This repository is **not** intended to be a complete knowledge base.  
Instead, it serves as:

- a personal reference  
- a thinking and communication aid  
- a reflection on recurring patterns, problems, and architectural decisions  

---

## What is Domain-Driven Design?

**Domain-Driven Design (DDD)** is an architectural and conceptual approach for tackling complex software systems by aligning the structure of the software with the business domain.

The core goal is that **business and technology share the same language**, and that software boundaries
reflect domain boundaries rather than accidental technical or organizational structures.

---

## Strategic Domain-Driven Design

The focus of **Strategic Domain-Driven Design** is primarily:
- identifying and protecting bounded contexts
- defining relationships between contexts
- managing integration and coupling
- avoiding accidental complexity and Big Balls of Mud

---

### Bounded Context

A **Bounded Context** is a clearly defined boundary within a domain. Within this boundary:
- the domain model has a clear meaning
- the terminology (Ubiquitous Language) is consistent and unambiguous
- the business concepts and rules are closely related
- the API for integration with other contexts is clearly defined

It is useful for reducing complexity, avoiding domain overlap and helping domain experts and developers to focus on specific area of the domain.
Modeling Bounded Contexts allows domains to be broken down into smaller, more manageable parts that are easier to reason about, maintain, and evolve over time independently.

Bounded Contexts are **isolated from each other**, meaning that models, language, and rules from one context should **not leak or be mixed** into another context.
This isolation is crucial to prevent accidental complexity, inconsistent terminology, and the emergence of “Big Balls of Mud” when different contexts influence each other without explicit translation or integration patterns.

#### Characteristics

**Ubiquitous language**  
A Bounded Context has its own ubiquitous language that is used by business owners, domain experts and developers to communicate concepts within the according part of the domain.
Each term has a specific meaning within a Bounded Context.
When the same term is used in different Bounded Contexts, then it typically has different meanings.

**Boundary**  
A Bounded Context has a clearly defined boundary that separates it from other Bounded Contexts.
The boundary defines which concepts reside within the Bounded Context and which do not.

**Domain model**  
A Bounded Context has its own domain model that is tailored to the specific needs of that context.
The domain model within a Bounded Context may be different from the domain model used in other contexts.

**Relationships**  
A Bounded Context may have relationships with other Bounded Contexts.
These relationships may include sharing data or collaborating on business processes.
The relationships between Bounded Contexts should be clearly defined and mapped to ensure that they are consistent and well-defined.

**Consistency**  
The domain model within a Bounded Context should be internally consistent, with a clear understanding of the business rules and constraints that apply within that context.

**Ownership**  
According to the ubiquitous language the ownership of a Bounded Context is owned by exactly one team.
A team may also be responsible for multiple Bounded Contexts.
A Bounded Context is never owned by more than one team, because this would lead to the loss of clarity about the meaning of terms in the ubiquitous language.

#### Modeling hints

Bounded Contexts are useful to create a clear and well-defined boundary around a specific subdomain or business capability, with a shared language and consistent set of rules and concepts.
The following criteria may guide with modeling.

**Business capabilities**  
A Bounded Context should align with a specific business capability or subdomain of the (overall) domain.
The boundary should be defined based on the business capabilities that are being served by the context.

**Ubiquitous language**  
A Bounded Context should have a well-defined, shared language that is specific to that context.
The language should be used consistently by everyone involved in the context, including business owners, domain experts and developers.
A term or concept might mean one thing in one context and something different in another context.

**Team structure**  
A Bounded Context should be aligned with the way that teams are organized and communicate with each other.
It should take into account the team structure and communication patterns within the organization.

**Consistency**  
A Bounded Context should be internally consistent and coherent.
The concepts and rules within the context should make sense and be logical in relation to each other.

**Technology**  
A Bounded Context may also define a technical boundary, if there are technical requirements that must be considered.

---

### Big Ball of Mud

A "Big Ball of Mud" describes a large, complex model in which **different domain concerns are mixed** and **context boundaries are inconsistent or unclear**.

As a model is extended and more dependencies are added, it becomes **increasingly complex and harder to understand**, and can eventually **degenerate into a Big Ball of Mud**.

It is crucial that such models are **not adopted uncontrolled in other contexts**, as this spreads complexity and undermines the clarity of bounded contexts.

---

### Relationship Patterns between Bounded Contexts

#### Upstream / Downstream

In an **Upstream/Downstream relationship**, the success of the downstream team depends on the success of the upstream team.

This relationship type is the foundation for several DDD patterns.

---

#### Customer / Supplier

A regulated customer–supplier relationship.

The priorities of the downstream team feed into the planning of the upstream team.

Downstream requirements are negotiated and scheduled for implementation by the upstream team.

Implementation is verified through acceptance tests, which are executed as part of continuous integration.

---

#### Conformist

An upstream/downstream relationship in which the upstream team shows **no obligation** to accommodate downstream requirements.

The downstream team integrates the upstream model and is constrained by changes and a suboptimal model.

No additional complexity arises from translating the upstream model into the downstream context.

---

#### Partnership

A mutual relationship in which failed implementation impacts multiple teams.

Requirements are negotiated and coordinated jointly, and integration is managed collaboratively.

Teams work closely together during development to meet requirements for all products.

---

#### Separate Ways

Explicitly separated domain contexts without connection.

Domain cohesion is too low to justify integration, eliminating unnecessary dependencies and integration effort.

Teams work autonomously, without any dependencies.

---

### Integration Patterns

Integration patterns describe how upstream and downstream bounded contexts collaborate and exchange information, while protecting domain models and controlling complexity.

---

#### Translation Layer

A translation layer exists **outside the domain layer** and translates between an external model and the internal domain model, and vice versa.

Its purpose is to **protect the internal domain model from foreign structures, meanings, and instability**.

Examples:
- DTO → Domain Model  
- Domain Model → API Response  
- JSON → internal command objects  
- XML → value objects  
- Domain Model → database rows  

Occurrence:
- Independent of upstream/downstream
- Commonly between domain and application or infrastructure layers

---

#### Anticorruption Layer (ACL)

An **isolating layer** in the downstream that exposes upstream functionality in the form of the downstream’s own model.

It **prevents the upstream model from being adapted directly** into the downstream model without filtering.

Typical use cases include:
- very large or domain-weak upstream models (so-called Big Ball of Mud)  
- situations where adapting directly to the upstream model would be impractical, while integration remains valuable or even required

The ACL **protects the downstream domain model from semantic pollution**, including:
- foreign semantics  
- conflicting domain concepts  
- unstable or undesirable models  
- upstream change pressure

It can be seen as a **specialized translation layer** and occurs only downstream.

---

#### Open Host Service (OHS)

A publicly defined protocol (clearly specified interface) that exposes a bounded context to all potential consumers.

Typically, for every bounded context that needs to integrate, a translation layer is defined to enable the integration.

An Open Host Service is suitable when many bounded contexts need to integrate, and maintaining separate translation layers for each context would be too costly or slow the team down.

Teams that need to integrate with the bounded context can adopt the protocol.

The protocol is **governed and evolved** to support new and general integration requirements.

The service provider takes the **upstream position**.
Every consuming bounded context is downstream – some teams will follow the upstream model (conformists), while others will implement an **anticorruption layer** to protect their own model.

---

#### Published Language

A **Published Language** is a stable, domain-oriented language used between bounded contexts.

When information needs to be exchanged between bounded contexts, translation occurs **to and from the existing domain models**.

Direct translation between domain models is often **not a good solution**, because:

- it may expose internal domain objects that were never meant to be public  
- it may include internal structures, workflows, and dependencies  
- it may be poorly documented  
- it can change frequently, breaking downstream consumers  

Therefore, a **stable, well-documented, and mutually agreed language** is provided as a translation medium between bounded contexts (i.e., a data exchange format).

The Published Language serves as a **ubiquitous language on the inter-context level** and is maintained and evolved based on data exchange requirements.

In many industries, standardized data exchange formats already exist as published languages.  
However, such languages can also be developed internally within an organization for its specific needs.

---

#### Shared Kernel

A **Shared Kernel** is a deliberately agreed-upon part of a model that is **used and maintained jointly** by two teams or bounded contexts.

The shared core can include, for example:
- libraries  
- data structures  
- domain concepts or terminology

The origin of a Shared Kernel often lies in:
- a team that has grown too large, or  
- a domain that has become too broad, making integration either impossible or highly costly

To manage this, the core domain functionality is **extracted**, and the original team is split.

The resulting teams then **use the shared core** and **commit to its joint maintenance and ongoing evolution**.

---

## Tactical Domain-Driven Design

The focus of **Tactical Domain-Driven Design** is primarily:
- designing and implementing the domain model
- applying core building blocks (Aggregates, Entities, Value Objects, Factories, Repositories, Services, Domain Events, etc.)
- translating the Ubiquitous Language into code
- enforcing business rules and invariants
- maintaining consistency and integrity within a Bounded Context

---

### Aggregate

An Aggregate is an abstraction for modeling a group of closely related domain objects with complex relationships.
The purpose of an Aggregate is to treat related objects as a single unit so that consistency of relationships can be established according domain rules when data changes.

#### Characteristics

**Root Entity**  
An Aggregate consists of a root Entity and related objects.
The root Entity defines the boundary of the Aggregate.
The objects within the Aggregate are referred to as Aggregate members.
All members must be accessed through the root Entity so that consistency of the Aggregate can be maintained.

**Boundary**  
An Aggregate has a clearly defined boundary that separates it from other Aggregates.
The boundary defines which objects are Aggregate members and thus which operations and data are in the Aggregate and which are not.
The boundary of an aggregate should be chosen based on the specific requirements of the domain model and the use cases of the application.
When defining an aggregate, it is important to ensure that the resulting design is consistent, cohesive, and performant.

**Identity**  
An Aggregate has a unique identity that is represented by the root Entity.
Aggregate members need only to be distinguishable within the boundary and therefore may have local identities.

**Consistency**  
Objects in the domain model are subject to domain rules.
A more concise term for domain rule is invariant.
Invariants must be met to maintain consistency of data and relationships.
Within an Aggregate the root Entity is responsible for applying invariants.
The root Entity acts as gateway for all operations on the Aggregate and establishes that the Aggregate remains in a consistent state when data changes.

**Relationships**  
Objects that are outside an Aggregate and hold references to the Aggregate may only refer to the root Entity.
Members of the aggregate may refer to each other freely.
If clients need to access Aggregate members, then they must do so through the root Entity.
Aggregate members may also refer to other Aggregates but must do so by value.

#### Modeling hints

Aggregates are useful to create domain models that are easier to reason about, maintain, and evolve over time.
An important task is to determine reasonable Aggregate boundaries.
The following criteria may guide with modeling.

**Consistency**  
An Aggregate must remain in a consistent state when data changes.
If changes to an object could impact the consistency of other objects, then they should be modeled as part of the same Aggregate.

**Boundary**  
Aggregate members should be accessed and modified together frequently.
If objects are accessed and modified independently, then they should be part of separate Aggregates.

**Transactions**  
Operations to an Aggregate should be atomic, as part of a single transaction.
If changes to an Aggregate member require changes to other objects outside the Aggregate, then these objects should be modeled as part of the same Aggregate.

**Domain relevance**  
Aggregate members should represent a cohesive set of logic and data that are related to a specific aspect of the domain.
If objects belong to multiple aspects of the domain, then they should be part of separate Aggregates.

**Granularity**  
An Aggregate boundary should provide an appropriate granularity.
If an Aggregate is too broad, it could impact maintainability negatively.
An Aggregate that is too limited may not offer sufficient context for certain use cases.

**Dependencies**  
An Aggregate boundary should be chosen to minimize interdependencies among Aggregates.
Tight coupling between Aggregates can affect scalability and maintainability.

---

### Domain Event

A Domain Event is an abstraction for modeling a significant occurrence or change within the domain that is relevant for domain experts.
The purpose of a Domain Event is to allow interested parties to respond to the event and take appropriate action.

#### Characteristics

**Significant occurrence**  
A Domain Event represents a significant occurrence within the domain.
This could be a change to the state of an Entity or an Aggregate, the completion of a business process, or the occurrence of a significant business event.

**Immutable**  
A Domain Event is immutable.
This ensures that the event is a true historical record of what happened in the domain.

**Contextual**  
A Domain Event is specific to a particular Bounded Context.
This allows to define Events that represent specific requirements of the context.

**Published**  
A Domain Event is published, meaning that it is broadcast to all interested parties within the domain.
This allows other parts of the system to respond to the event and take appropriate action.

**Asynchronous**  
A Domain Event is typically processed asynchronously, meaning that it is handled independently of the process that generated it.
This allows the system to continue processing without waiting for the event to be handled.

---

### Entity

---

### Value Object

---

### Factory

---

### Repository

---

### Service

A Service in general is an abstraction for modeling operations that cannot be clearly assigned to the responsibility of a domain object.
A Service is the appropriate place for operations that, in an object, would compromise its conceptual clarity and ease of understanding.
A Service does not hold state and therefore can easily scale to handle increasing traffic.
A Service can offer a group of related operations that together realize a specific requirement or goal.

#### Domain Service

A Domain Service focus on business logic and coordinating tasks that are closely tied to the domain model.
It offers significant domain functionality and can be reused more easily.
A Domain Service can reduce the complexity of the domain model, which may arise through many Aggregates and Entities with fine-grained operations, by providing easy-to-use operations.
Clients of the domain model are decoupled from Aggregates and Entities and only need to pass them as parameters of operations and receive them as results.

#### Application Service

An Application Service focus on the interaction with users and as supplier with other systems.
It performs tasks that are not relevant to the Domain Layer.
Therefore an Application Service may orchestrate fine-grained operations on Services and Objects in the Domain Layer and operations on Services in the Infrastructure Layer.

#### Infrastructure Service

An Infrastructure Service has no significant meaning in the domain and covers purely technical aspects. Domain and Application Services work together with Infrastructure Services.

#### External Service

---

### Module

Conceptual Module / Module in the model / Module as namespace

---

These notes are intentionally incomplete and will be revisited, refined, and extended over time.
