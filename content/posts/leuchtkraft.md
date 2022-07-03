---
title: "Leuchtkraft"
date: 2022-04-13T22:18:09+01:00
---

# Leuchtkraft
## What is Leuchtkraft
Leuchtkraft is a declarative, logical hobby programming language i developed over the past 
few days. 
It is designed to be minimalistic, with only two operations (more on that later).
I find it to be quite satisfying how much can be expressed with such a limited number of
operations. 
Leuchtkraft's resolution algorithm is completely selfmade, i am pretty sure it's able to
resolve any resolvable questions but no gurantees (:

To clarify a few terms right from the beginning:

* *Declarative* means that you only tell the computer *what* you want it to do, not *how* to do it. (Unlike most modern languages, which are *imperative*).
* *Logical* means that Leuchtkraft operates purely on Statements which are either `True` or `False`


You can find the project on [GitHub](https://github.com/Wuelle/Leuchtkraft).

## Syntactical Elements
Leuchtkraft only supports a two basic operations, namely **And** and **Implication**.

| A     | B     | A and B | A => B |
|-------|-------|---------|--------|
| `False` | `False` | `False`   | `True`   | 
| `False` | `True`  | `False`   | `True`   | 
| `True`  | `False` | `False`   | `False`  | 
| `True`  | `True`  | `True`    | `True`   | 

Note how `False` implies both `True` and `False`. That might seem counterintuitive at first, but
it is absolutely possible to derive true statements from false ones.
An example is the (false) equation `-1 = 1` which, if squared, produces `1 = 1`, a 
true statement. 

## Getting Started
Leuchtkraft scripts are mostly made up of three parts: *Rules*, *Facts* and *Questions*.
If you worked with Prolog before, that concept should be familiar.

### Facts
A fact consists of a *predicate* applied to an *object* with an *inferred value*.
Facts are statements considered to hold true.
They describe how specific things are related to each other.
Lets go over an example:
```
true => Month(June)
```
Here, `Month` is a predicate. `true` is an expression that always
resolves to `True` and it implies `Month(June)`. 
Therefore, this rule states that "June is a Month".
A slightly more complicated rule is 
```
true => Month(June) and Month(March) => Month(July)
```
Fundamentally, this is the same logic as the first rule we looked at: *The left side
of an implication is true, therefore the right side must be too*.
Notice how we are able to chain implications together.


### Rules
Rules are generalized facts. While you use facts to express properties of specific
objects, rules are used to generalize that knowledge over generic objects.
So while a fact could be `John likes apples`, a rule would be something like
`John likes everything that Mary likes` or `John likes everything that is green and edible`.
To mark a variable as *free*, i.e replaceable by any specific value,
the `forall` keyword is used.

```python
// Rules
forall X
    // John likes everything that mary likes
    Likes(Mary, X) => Likes(John, X)
```
Leuchtkrafts indentation rules are pretty simple: Since you cannot nest `forall`
statements, any number of spaces or tabs at the beginning of a line counts as a
single level of indentation. 

### Questions
With facts and rules, the logical environment can be described. However, if we cannot
interact with the environment, its hardly of any use. This is where questions come into play.
Like free variables, they contain identifiers that are not mapped to a specific value.
These identifiers are followed by a question mark (`X?`) and when encountering them,
the interpreter will list all possible values for that variable such that the overall
statement still holds true.
The following question outputs `true` if Apples are red and `false` otherwise
```
X? => Red(Apple) => X?
```
Notice how we cannot just write `Red(Apple) => X?`, since the value of `X` would be 
ambiguous if `Red(Apple)` is false. In some cases, that ambiguity might be desirable.
In this case, it is not.

#### Dealing with Contradictions
If we try add contradictions to the knowledge base, Leuchtkraft won't find any configuration
of variables that make the statements true. (Note that Leuchtkraft is capable
of ignoring very simple contradictions, like `true => false`)
Therefore, the output will be `Neither`.

## A word of advice
Leuchtkraft is very much in beta (version `0.1.0` at the time of writing).
Expect bugs, a lot of them.
Contradictions aren't explained and using multiple unknown variables in the same
question is "experimental" (which is fancy speech for "i haven't tested it").

## Lore
Leuchtkraft is the german word for "Luminosity", but that's not what the language
is named after. It's named after the Spaceship "LEUCHTKRAFT" from 
[Perry Rhodan](https://en.wikipedia.org/wiki/Perry_Rhodan).
The Commander of the LEUCHTKRAFT is called "Alaska Saedelaere". Guess where 
my nick comes from (:

