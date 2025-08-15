# Domain Driven Design

## Domains & Subdomains

Domains are broad business areas in your organization such as Order Management.

Subdomains are one level down from domains, eg Order Management may have subdomains: Order Processing, Payment Processing & Shipping.

## Ubiquitous language

Domains & subdomains are modelled using a ubiquitous language
- shared vocab used by both developers and domain experts
- goals:
  - eliminate misunderstandings and ambiguities
  - ensure clear and concise communication

## Domain Entities

- significant business concepts such as a customer or an order
- described using the Ubiquitous Language
- have unique identifiers such as an order ID
- have their own set of attributes and behaviors *within the domain*

## Bounded Contexts

- boundary within which the concepts, terminologies and rules are consistent
  and isolated from other bounded contexts
- bounded context encapsulates a domain or subdomain
- helps manage complexity by breaking down a large system into smaller more manageable parts
- stops different models of the world from interfering with each other

### Rationalle

The same entity might exist in different domains but with very different
attributes and behaviors, for example: "Product" might appear in multiple
domains: Inventory Management, Order Management & Marketing. It will always
have a Product ID and Name, but:

- in Inventory Management we need: quantity in stock, which warehouse and the
  supplier
- in Order Management we need: price, order number, quantity sold
- in Marketing we need the campaign id, whether a featured product and customer
  reviews

## ACL - Anti Corruption Layer

- a popular pattern for context maps
- translates entities from an external bounded context into an internal one
- moves coupling between two bounded contexts into a single layer
- protects core domain logic from upstream changes

## Context Mapping

model the relationship between bounded contexts & how they should interact with each other

### Rationalle

Bounded contexts can't always live in isolation: they need to talk to each
other and we need to understand the relationships that exists between them.

## Types of Relationships

- mutually dependent
  - their software artifacts or systems need to be delivered together
  - close reciprocal link between the data and the functionalities of these teams
  - need a lot of communication each other in order to coordinate their efforts
  - which often shows up in the partnership pattern in context Maps which we will see in a moment
- Upstream Downstream relationship
  - indicate with as you can see with the U and the d labels which will see in many examples later as well in
  - actions of an upstream team will have an effect on the downstream team
  - actions of the downstream team do not have a significant impact on the Upstream team
  - this power Dynamic is pretty one-sided and puts the downstream team in a precarious position and that's
    - why they will often need some protection and you will often find anti-corruption layers deployed in situations like this
- free from each other's influence
  - if changes in one bounded context do not influence the other success or failure there is
  - therefore no organizational or technical link of any kind between these two

## Common Patterns

Nine common patterns, some technical, some organizational and some are both.

- Mutually Dependent

  - shared kernel pattern

    - two teams have some shared artifact in their system, eg shared npm module or shared database
    - high degree of coupling
    - requires a lot of coordination between the teams
    - not necessarily an anti pattern, but use with caution
    - some cases where work really well eg. one team is responsible for two bounded contexts and there are some overlap between these two in terms of the language
      - but typically when you have two teams with share kernels then they should really form a partnership instead

   - partnership pattern

    - not really a technical pattern is purely an organization one
    - recommended for teams with a shared kernel
    - one thing to look out for with this pattern is when a team enters into a partnership with multiple other teams
      - because when this happens this team in the middle usually find themselves inundated with meetings and struggles to find time to get any actual work done
      - they suffer from a lack of agency where they can't even set their own agenda because they have too many partners to satisfy

- Free

  -  published language
    - loosely couple through shared language
    - every bounded context can translate in and out of this language
    - often combined with OHS pattern
    - shared language will often come in the form of a well-defined API, eg.
      - e-commerce platforms integrates with stripe for payment processing
        - through a published language in the form of a well-defined API
        - stripe is the OHS 
        - e-commerce platforms are independent of each other
        - each translates messages possibly through ACLs

  - separate ways pattern

    - for teams with
      - free relationship
      - no technical integration between two bounded contexts
      - possibly only only a manual process to integrate them
    - interesting pattern for building MVPs
    - avoids direct Integrations which can be expensive or time consuming to implement

- Upstream Downstream Patterns

  - open host service (OHS)

    - shared API that provides some common fun ality to multiple consumers
    - the team that provides the open host service is an upstream team so the AFF

  - Anti Corruption Layer (ACL)

    - translates one model to another
    - often deployed in the client
    - deduces coupling to a single layer
    - requires upfront investment

  - conformist pattern

    - no ACL or translation: downstream system just uses the Upstream model directly
    - quicker to implement but higher coupling
    - simpler and gives you higher velocity in the short term it can
    - useful tactical move if intention is to pay back tech debt or if upstream is stable
    - can create hidden coupling between systems if used across multiple bounded contexts
      - changes in one can propagate all the way to another that it has no direct links with

  - customer supplier pattern

    - for teams with an upstream Downstream relationship
    - Upstream team gives the downstream team some influence on its priorities and backlog
      - power dynamic between them is no longer one-sided
    - you should avoid having too many customer relationships and yield too much inference to the customers
      - such as giving them the ability to veto your planned changes because again the team in the middle is going to lose much of its autonomy as his road map becomes driven by its customers
      - you end up spending a lot of time in meetings to prioritize requirements and the juggling between the different requirements from all these different customers

- other

  - big ball of mud pattern

    - a mass of different models all mixed together
    - inconsistent boundaries everywhere and to prevent
    - to prevent upstream big ball of mud mess from propagating into your bounded context use an ACL

