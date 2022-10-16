# Variance
The subtyping is when you can assign a value of a subtype to a variable of a supertype

## Set subtyping
A subtype has less or equeal number of members of a supertype. In Typescript the word `extends` intuitively more suitable for row or inclusive polymprphism (when an object/class of a subtype has more properties than a supertype, more on it in the next section).

```typescript
type T_2345 = 2 | 3 | 4 | 5
type T__345 =     3 | 4 | 5
type T__34_ =     3 | 4

// T2 extends T1 --> 3 | 4 extends 3 | 4 | 5
// T1 extends T0
// T0 extends number

const v1: T__34_ = 3
const v2: T__345 = v1  // OK

const v3: T_2345 = 2
const v4: T__345 = v3 // NOT OK, cause T__345 cannot be 2

```
functions' arguments are always contravariant and return type is always covariant
```typescript
const fn1 = (a: T__345): T__345 => a

const v5: T_2345 = fn1(3) // OK
const v6: T__34_ = fn1(3) // NOT OK
```
`v5` is OK because `fn1` returns `T__345` and `T_2345` is wider than these members, nothing wrong can happen.
But `v6` could get `5` as the value which is not the member of the type `T__34_`.

```typescript
const fn1 = (a: T__345): T__345 => a

const v7: T__34_ = 3
fn1(v7) // OK
const v8: T_2345 = 2
fn1(v8) // NOT OK
```
`v7` is a subtype of argument `a`, `v7` is narrower than the type the function excpects as an input. It means, the function knows how to deal with all members of the input. Otherwise, if we suplay a super type of the function's arguement type, the function won't be able to process all members of a wider type.

By translating this reasoning to function subtyping we get to `Fn2` is subtype of `Fn1`, (`fn2` can be assigned to `fn1`)
```typescript
type Fn1 = (x: T__345) => T__345
type Fn2 = (x: T_2345) => T__34_

const fn2: Fn2 = x => x < 4? 3: 4
const fn1: Fn1 = fn2
//         fn1 type:     3 | 4 | 5 => 3 | 4 | 5
// fn1 value's type: 2 | 3 | 4 | 5 => 3 | 4

```
`fn1` expects `T__345`, but actual function is `fn2` which is capable of handling the wider type of the argument `T_2345`.
From the other side, `fn1` must return `T1__345` which will be used somewhere futher as a variable, but actual function returning the result is `fn2` which produces only `T__34_`.

## Row/Inclusive subtyping
The example is based on row polymorphism (objects), the same logic can be applied to inclusive polymorphism (classes)
```typescript
type Creature = {age: number}
type Animal   = {age: number, kind: string}
type Pet      = {age: number, kind: string, name: string}

//    Pet extends Animal
type Fn1 = (x: Creature) => Pet
type Fn  = (x: Animal  ) => Animal
type Fn3 = (x: Pet     ) => Creature

// Fn1 extends Fn, Fn1 is subtype of Fn
// Fn  extends Fn3

const fn  : Fn  = x => x
const fn1 : Fn1 = x => ({age: x.age, kind: '', name: ''})
const fn3 : Fn3 = x => ({age: x.age})
const fn_a: Fn  = fn1 // OK
const fn_b: Fn  = fn3 // NOT OK
const fn_c: Fn3 = fn
```
```
fn_a type is Fn
           Animal => Animal
      {age, kind} => {age, kind}

fn_a's actual value is fn1
         Creature => Pet
            {age} => {age, kind, name}
```
We supply `fn_a` with `Animal {age, kind}`, and `fn1` needs only `Creature {age}`.
Also `fn1` returns `Pet {age, kind, name}`, but `fn_a` types the return value as `Animal {age, kind}` (dropping `name`) and the caller is satisfied with the result.
```
fn_b type is Fn
           Animal => Animal
      {age, kind} => {age, kind}

fn_b's actual value is fn3
              Pet => Creature
{age, kind, name} => {age}
```
We supply `fn_b` with `Animal {age, kind}`, and `fn3` needs more `Pet {age, kind, name}`. Bang!
Also `fn3` returns `Creature {age}`, but `fn_b` types the return value as `Animal {age, kind}` and the caller may get in a trouble trying to access `kind` property.
```
fn_c type is Fn3
              Pet => Creature
{age, kind, name} => {age}

fn_c's actual value is fn
           Animal => Animal
      {age, kind} => {age, kind}
```
We supply `fn_c` with `Pet {age, kind, name}`, and `fn` needs more `Animal {age, kind}`. All good.
`fn` returns `Animal {age, kind}`, but `fn_c` types the return value as `Creature {age}` (dropping `kind`) and the caller is satisfied with the result.
