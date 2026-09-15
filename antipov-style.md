## Common

1. Nest classes to their producers
2. Nest classes to their aggregates, e.g. 'User.Address.ZipCode'

## Designing an Interface
#### Use-cases / services / ports

1. If the class is the Type's deliberately the only producer/consumer: define the Type nested
2. If there is more than 1 shape of the returned value: use sealed class
3. Do not throw: return sealed result
4. Report what happened, render at the caller
5. Do not report broader error type than the producer actually produces. (Exception: multiple impls of the same interface)

## Designing a Sealed type
#### The goal is to forbid the impossible (when possible)
#### Action.Performed / Action.Failed.MissingData
#### Result.Data / Result.Fail.NetworkError

1. Always nest the hierarchy
2. Do not import the members on the call-sites: the name of the target type is the full name starting from the beginning of the hierarchy

## Designing a Use-case / Pipeline
#### Which might carry a different name in the project; might be spread across more than one file
#### Use-case is a pipeline that executes scenario (possibly alternating): `Request => Result`, 
#### with a series of mappings, 'Steps' in between.

1. For each `Step`'s type: have a single producer. (Exception: strategy-like patterns with alternative implementations).
2. For each kind of `A => B` mapping: have a single owner.
3. Make the pipeline read top-down. Keep branches, mappings and retries explicit.
4. Define the types explicitly when unless they are obvious.
5. The types should prohibit the impossible cases/scenarios, not logic in the pipeline.
6. If another consumer needs only part of the pipeline: expose that part (instead of faking the 'Request').
7. Give a named home to a `Step` that appears in multiple places.
8. Render at the end of the pipeline.
9. Do not construct the pipeline's `Result` in a `Step`, even for an early-exit.


# Code-style: functions

1. Add names for call arguments when they differ from the parameter name
2. Do not mix named and positional arguments in the same function call
3. Put arguments on separate lines when you have 4+ arguments
4. Put arguments on separate lines when they can not fit the accepted width 
5. Use named arguments when arguments are put on separate lines 
6. Move out subject from 'when' if it requires to call a function with parameters on separate lines
7. Do not accept a nullable argument only to return early if its null
8. Use verb in function names
9. Call mapping functions like 'to<Type>'

## Code-style: Interfaces

1. (?) (!) Lean to declare the nested Request/Response-like types directly above the method that owns them
2. (!) Exception - `(void)=>A`, when `A` is 'complex'/'long'; also when it is not `void`, but also a simple type
3. 

## Designing Hex

1. DI annotations can appear on any level

## Smells

1. Interface that defines an operation where `Request` type is a sealed hierarchy while that type is owned by that operation; use overrides instead 
2. Extension functions that map to the types of other layers; this is a hidden dependency; find it a home


