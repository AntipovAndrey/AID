# Scenario tests

Scenario tests explain product behavior through actors’ actions and the backend API calls that implement them.
Product behavior drives the story; explicit API calls show how clients interact with the backend.

## Agree on the scenario first

Before writing a test, obtain the scenario from the user or follow their instructions for discovering it. Submit a
discovered scenario as actor actions and expected outcomes for the user to confirm before implementation. Reading the
code alone is not a substitute for that agreement.

Test the relevant part of the story, not necessarily the entire journey. Establish unrelated prerequisites with
fixtures; direct storage setup is allowed.

Prioritize successful paths. Include negative outcomes when they follow from real actor actions. Internal timers,
expiration checks and failures without an actor-driven story belong in other tests.

## Identify actors and domain entities

Every test class must have a KDoc with exactly this structure:

```kotlin
/**
 * Actors:
 * - <name used in the scenario>: <role in this scenario>.
 *
 * Domain entities:
 * - <name used in the scenario>: <meaning in this scenario>.
 */
```

Use one short sentence per entry. No introduction, additional sections or nested lists. Include only actors and domain
concepts used in the scenarios, not implementation classes or DTOs. Use the same names in the step descriptions.
Definitions clarify the context without redefining product terminology.

An actor initiates a product action. A person or agent can be an actor; an external service can also be one when it
independently initiates an action. Delivering someone else’s action or answering a request does not make a component an
actor. Asynchronous delivery alone does not create a new actor.

## Make the test boundary visible

Keep all scenario-specific configuration, fake implementations and fixtures at the beginning of the test class, before
test methods. Make it explicit there which external systems are replaced.

Replace only external systems. Keep backend business logic and storage real. Use `AbstractIntegrationTest`, `MockMvc`
and the existing PostgreSQL and Redis test containers.

## Write the story in the test method

Name tests by the product action and expected outcome, adding context where needed. No fixed naming template is required.

Introduce every product step with a multiline block comment describing the actor’s action. One product step may
contain several technical calls.

Use a `//` comment where necessary to identify the component or client making a technical call. Do not repeat what the
call already shows.

Keep backend API calls and assertions directly in the test method. The endpoint, authorization and relevant inputs must
be visible at the call site.

Helpers may prepare prerequisites, assemble request DTOs, configure external fakes or read event streams. Give DTO
builders names that describe the data they prepare rather than merely restating its type. Do not hide the actions under
test or their assertions behind helpers.

## Check observable results

Assert what actors can observe: API responses, delivered events and messages accepted by external systems. Do not
inspect repositories, stored credentials or internal registries to prove the result.

If a later action uses an earlier result, take those data from the actual response or event. A step does not need a
subsequent action to prove its success.

External fakes must distinguish accepted operations from rejected ones where that distinction matters to the scenario.
Capturing an outgoing request alone does not prove successful delivery.

## Keep tests isolated

Use unique test data and scenario-local stubs. Do not mutate global state or mock global objects. Bound asynchronous
waits and clean up stubs, streams and other resources opened by the test.
