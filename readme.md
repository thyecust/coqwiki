## coq concepts

0

- term: basic expression, x.
- type: a term needs a type to be valid, x:T. Types are terms.
- command: be used to modify the state of a Coq document. [Coq command index](https://coq.inria.fr/refman/coq-cmdindex.html#command-index).
- tactic: specifies how to transform the current proof state as a step in creating a proof. [Coq tactic index](https://coq.inria.fr/refman/coq-tacindex.html#tactic-index).

1

- sentence: Coq documents are made of a series of sentences that contain *commands* or *tactics*. Sentence is terminated with `.` and may be decorated with *attributes*.
- attribute: setting, modifies the behavior of a single sentence.
- flag: setting, modifies the behavior of multiple sentences, boolean value.
- option: like flag but numeric/string value.
- table: like flag but strings value.

2

- sort: Types are terms, so types have types (called sorts). Base sorts are SProp, Prop, Set.
- Prop: type of logical propositions. M:Prop is a logical proposition, m:M is a proof. M:Prop are called *form*, a subclass of term.
- SProp: like Prop, but have irrelevant proofs (all proofs are equal).
- Set: type of small sets.

Note: Prop:Type(1), SProp:Type(1), Set:Type(1), Type(i):Type(i+1). But users only see Prop:Type, SProp:Type, Set:Prop, Type:Type.

## functions

- function: syntax like `fun ident: type => term`, e.g. `fun x: A => x` is the id function on type A.
- function type: syntax like `forall ident: type1, type2`, denote the product type of variable `ident` of type `type1` over `type2`.
  1. if `ident` is used in `type2`, this is a dependent product
  2. if `ident` is not used in `type2`, this is a non-dependent product, alse written as `forall _: A, B` or `A -> B`, used to denote both propositional implication and function types.


## definitions

```coq
(* https://coq.inria.fr/library/Coq.Init.Datatypes.html *)
Definition ID := forall A:Type, A -> A.
Definition id : ID := fun A x => x.
```

This defines `ID` with type `forall A : Type, A -> A` and `id` with type `fun (A:Type) (x:A) => x`. The `A` in `id` is an implicit argument (a language extension, see [Implicit arguments](https://coq.inria.fr/refman/language/extensions/implicit-arguments.html#implicitarguments)). You can use command:

```
Check (id 2).
Check (@id nat 2).
```

----

```coq
(* https://coq.inria.fr/refman/language/core/inductive.html *)
Fail Inductive Lam := lam (_ : Lam -> Lam).
Fixpoint infinite_loop l : False :=
  match l with lam x => infinite_loop (x l) end.
Check infinite_loop (lam (@id Lam)) : False.
```

```coq
(* https://coq.inria.fr/refman/proofs/writing-proofs/equality.html *)
Opaque id.
Goal id 10 = 10.
```

```coq
(* https://coq.inria.fr/refman/language/extensions/implicit-arguments.html *)
Definition id {A : Type} (x : A) : A := x.
```

4

- inductive construction

5

- datatype: part of preclude of Coq library, defined as inductive constructions over the sort Set.
