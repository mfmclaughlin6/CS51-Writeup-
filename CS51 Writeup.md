# CS51 Final Project WriteUp 

For my extension, I implemented a *lexically* scoped evaluator (as per the recommendation)
for my MiniML interpreter. In this writeup, I will explain how I implemented this 
evaluator as well as how this evaluator specifically differs from the *dynamically* 
scoped evaluator with regards to functions and function applications. Thank you for 
taking the time to read and evaluate my final project! 

### Functions 

When evaluating functions *lexically*, the current environment in which the function 
is evaluated is saved in a `closure:` 

``` 
let rec eval_l exp env = 
... 
| Fun (v, e1) -> Env.close e1 env  
```
The closure essentially links the function with the current environment in an akin 
fashion to the *substitution model* (but without actually substituting). 

In a *dynamic* environment, functions simply evaluate to themselves and thus their 
free variables can be altered up until the point in which they are applied: 

``` 
let rec eval_d exp env = 
... 
| Fun (v, e1) -> exp 
``` 
### Function Application

We will examine the differences between these two evaluators by looking at a 
paradigmatic example provided in the final project specifications: 

``` 
let x = 1 in 
let f = fun y -> x + y in 
let x = 2 in 
f 3 ;; 
``` 
Specifically, we will see how this expression evaluates differently based on 
how functions are applied in these different environments.  

The differences in the *lexical* and *dynamic* evlauators lies in this idea of 
`closures.` 

#### Dynamically Scoped Evaluator

Without closures, the environment that corresponds to the above expression 
dynamically changes without ever saving previous conditions. Specifically, 
even though `x -> 1` (i.e. x is mapped to 1) before the function defintion 
`let f = fun y -> x + y`, x becomes mapped to 2 before the function is applied to 
`Num(3)`. So, when the function `f` is applied to `Num(3)`, `x -> 2`, resulting 
in the evaluation of `2 + 3` which equals `Num(5)`. 

#### Lexically Scoped Evaluator 

When functions evaluate to `Closures`, as mentioned above, they are evaluated
and applied in the context of the environment in which they are defined. This
can be seen in the code for the application of a function in eval_l: 

``` 
let rec eval_l 
... 
| App (e1, e2) -> 
... 
  | Env.Closure ((Fun(v,e3), close) -> eval_l' e3 Env.extend close v... 
```
Here we are extracting the *closure* (i.e. the environment in which the function was
defined) and applying the function's defintion in this former environment, not the 
current environment. Because the function `f` is bound by the environment in which 
`x -> 1,` `f` becomes `fun y -> 1 + y` when it is being applied, no matter how many
times `Var(x)` is shadowed by other defintions. In this evaluation then, `f 3` evaluates
to `1 + 3` which equals `Num(4)`, a different result than for the dynamically 
scoped evaluator! 

Thank you again for evaluating my **CS51 Final Project!** 
