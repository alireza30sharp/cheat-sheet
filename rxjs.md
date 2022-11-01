### ☑ RxJS Operators 

 ### Introduction
 > RxJS is a library for reactive programming using Observables, to make it easier to compose asynchronous or callback-based code.
 It provides a lot of operators to manipulate the data emitted from an Observable and its flow.
  
   ### Basic Operators
   ### from
   Turn an array, promise, or iterable into an observable.
  ```
   import { from } from 'rxjs';

//emit array as a sequence of values
// The output will be: 1,2,3,4,5
from([1, 2, 3, 4, 5])
    .subscribe(data => ...);
 ```
  ### of
  Emit variable amount of values in a sequence and then emits a complete notification.
  ```
  import { of } from 'rxjs';

// Emits any number of provided values in sequence
// The output will be: 1,2,3,4,5
of(1, 2, 3, 4, 5)
    .subscribe(data => ...);
```
     
### pipe
Allows executing operators on emitted values in the order they were defined.
```
import { of, pipe } from 'rxjs';

of(1,2,3,4)
    pipe(
        op1(),
        op2(),
        op3()
    )
    .subscribe(data => ...)

// The emitted values will be the result of op3(op2(op1(value)))
```
