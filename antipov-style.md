## Designing an Interface
#### Use-cases / services / ports

1. If the class is the Type's deliberately the only producer/consumer: define the Type nested
2. If there is more than 1 shape of the returned value: use sealed class
3. Do not throw: return sealed result
4. Report what happened, render at the caller
5. Do not report broader error type than the producer actually produces. (Exception: multiple impls of the same interface)

