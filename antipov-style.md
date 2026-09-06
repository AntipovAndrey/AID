## Designing an Interface
#### Use-cases / services / ports

1. If the class is the Type's deliberately the only producer/consumer: define the Type nested
2. If there is more than 1 shape of the returned value: use sealed class
3. Do not throw: return sealed result
4. Report what happened, render at the caller
5. Do not report broader error type than the producer actually produces. (Exception: multiple impls of the same interface)

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
