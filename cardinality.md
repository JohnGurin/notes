# Cardinality
## set cardinality
```typescript
type T_bool  = false | true    // |T_bool| = 2
type T_abc   = 'a' | 'b' | 'c' // |T_abc|  = 3
type T_tuple = [T_bool, T_abc] // |T_bool| x |T_abc| = 2 x 3 = 6

// (approximation of discriminated union type)
type T_union = T_bool | T_abc  // |T_bool| + |T_abc| = 2 + 3 = 6
type T_option<T> = T | null    // |T| + 1
```
## function cardinality (exponential)
```typescript
type Fn1 = (x: false | true) => 1 | 2 | 3 // |Fn1| = 3² = 9
/*
 Combination of all possible functions for Fn1 type
 true -> 1, false -> 1        true -> 2, false -> 1       true -> 3, false -> 1
 true -> 1, false -> 2        true -> 2, false -> 2       true -> 3, false -> 2
 true -> 1, false -> 3        true -> 2, false -> 3       true -> 3, false -> 3
*/

type Fn2 = (x: 1 | 2 | 3) => true | false // |Fn1| = 2³ = 8
/*
 Combination of all possible functions for Fn2 type
 1 -> true , 2 -> true , 3 -> true         1 -> false, 2 -> false, 3 -> true
 1 -> false, 2 -> true , 3 -> true         1 -> false, 2 -> true , 3 -> false
 1 -> true , 2 -> false, 3 -> true         1 -> true , 2 -> false, 3 -> false 
 1 -> true , 2 -> true , 3 -> false        1 -> false, 2 -> false, 3 -> false
*/
```
