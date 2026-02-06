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

A **Bounded Context** is a clearly defined domain and technical boundary within which a **model, a language, and an API are explicit and unambiguous**. 

Within this boundary:  
- the domain model has a clear meaning  
- the terminology is consistent (Ubiquitous Language)  
- the API is clearly defined for integration with other contexts  

Bounded Contexts are **isolated from each other**, meaning that models, language, and rules from one context should **not leak or be mixed** into another context.  

This isolation is crucial to prevent accidental complexity, inconsistent terminology, and the emergence of “Big Balls of Mud” when different contexts influence each other without explicit translation or integration patterns.

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

The service provider takes the **upstream position**. Every consuming bounded context is downstream – some teams will follow the upstream model (conformists), while others will implement an **anticorruption layer** to protect their own model.

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

These notes are intentionally incomplete and will be revisited, refined, and extended over time.
