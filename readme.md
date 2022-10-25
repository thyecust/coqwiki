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
- assumption: extend the global environment with axioms, parameters, hypotheses or variables. bind ident with type.

## definitions

- definition: extend the global environment by associating names to terms.

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

- assertion: states a proposition (or a type). enter proof mode.

## conversion

- cbv, call by value?
- reduction
  * beta-reduction, eliminates fun
  * zeta-reduction, eliminates let
  * match-reduction, eliminates match
  * delta-reduction, eliminates variable/constant
  * fix-reduction, replace fix
  * cofix-reduction, replace cofix
  * iota-reduction, do match-, fix- and cofix-reduction.
- expansion
  * eta-expansion, replace forall with fun

```coq
Coq < Goal 1 + 1 = 2.
1 goal
  
  ============================
  1 + 1 = 2

Unnamed_thm0 < cbv delta.
1 goal
  
  ============================
  (fix add (n m : nat) {struct n} : nat :=
     match n with
     | 0 => m
     | S p => S (add p m)
     end) 1 1 = 2

Unnamed_thm0 < cbv fix.
1 goal
  
  ============================
  (fun n m : nat =>
   match n with
   | 0 => m
   | S p =>
       S
         ((fix add (n0 m0 : nat) {struct n0} : nat :=
             match n0 with
             | 0 => m0
             | S p0 => S (add p0 m0)
             end) p m)
   end) 1 1 = 2

Unnamed_thm0 < cbv beta.
1 goal
  
  ============================
  match 1 with
  | 0 => 1
  | S p =>
      S
        ((fix add (n m : nat) {struct n} : nat :=
            match n with
            | 0 => m
            | S p0 => S (add p0 m)
            end) p 1)
  end = 2

Unnamed_thm0 < cbv match.
1 goal
  
  ============================
  S
    ((fix add (n m : nat) {struct n} : nat :=
        match n with
        | 0 => m
        | S p => S (add p m)
        end) 0 1) = 2

Unnamed_thm0 < cbv fix.
1 goal
  
  ============================
  S
    ((fun n m : nat =>
      match n with
      | 0 => m
      | S p =>
          S
            ((fix add (n0 m0 : nat) {struct n0} : nat :=
                match n0 with
                | 0 => m0
                | S p0 => S (add p0 m0)
                end) p m)
      end) 0 1) = 2

Unnamed_thm0 < cbv beta.
1 goal
  
  ============================
  S
    match 0 with
    | 0 => 1
    | S p =>
        S
          ((fix add (n m : nat) {struct n} : nat :=
              match n with
              | 0 => m
              | S p0 => S (add p0 m)
              end) p 1)
    end = 2

Unnamed_thm0 < cbv match.
1 goal
  
  ============================
  2 = 2
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
