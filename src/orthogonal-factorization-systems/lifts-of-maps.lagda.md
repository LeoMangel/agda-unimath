# Lifts of maps

```agda
module orthogonal-factorization-systems.lifts-of-maps where

open import foundation.contractible-types
open import foundation.dependent-pair-types
open import foundation.equivalences
open import foundation.functions
open import foundation.fundamental-theorem-of-identity-types
open import foundation.homotopies
open import foundation.identity-types
open import foundation.propositions
open import foundation.sets
open import foundation.small-types
open import foundation.structure-identity-principle
open import foundation.truncated-types
open import foundation.truncation-levels
open import foundation.type-arithmetic-dependent-pair-types
open import foundation.universe-levels

open import orthogonal-factorization-systems.extensions-of-maps
```

## Idea

A _lift_ of a map `f : X → B` along a map `i : A → B`
is a map `g : X → A` such that the composition `i ∘ g` is `f`.

```md
           A
          ^|
        /  i
      g    |
    /      v
  X - f -> B
```

## Definition

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} {B : UU l2} (i : A → B)
  where

  is-lift : {X : UU l3} → (X → B) → (X → A) → UU (l2 ⊔ l3)
  is-lift f g = f ~ (i ∘ g)

  lift : {X : UU l3} → (X → B) → UU (l1 ⊔ l2 ⊔ l3)
  lift {X = X} f = Σ (X → A) (is-lift f)

  total-lift : (X : UU l3) → UU (l1 ⊔ l2 ⊔ l3)
  total-lift X = Σ (X → B) lift

module _
  {l1 l2 l3 : Level} {A : UU l1} {B : UU l2} (i : A → B)
   {X : UU l3} {f : X → B}
  where

  map-lift : lift i f → X → A
  map-lift = pr1

  is-lift-map-lift : (l : lift i f) → is-lift i f (map-lift l)
  is-lift-map-lift = pr2
```

## Operations

### Vertical composition of lifts of maps

```md
           A
          ^|
        /  i
      g    |
    /      v
  X - f -> B
    \      |
      h    j
       \   |
         v v
           C
```

```agda
module _
  {l1 l2 l3 l4 : Level} {X : UU l1} {A : UU l2} {B : UU l3} {C : UU l4}
  {i : A → B} {j : B → C} {f : X → B} {h : X → C} {g : X → A}
  where

  is-lift-vertical-comp : is-lift i f g → is-lift j h f → is-lift (j ∘ i) h g
  is-lift-vertical-comp F H x = H x ∙ ap j (F x)
```

### Horizontal composition of lifts of maps

```md
  A - f -> B - g -> C
    \      |      /
      h    i    j
        \  |  /
         v v v
           X
```

```agda
module _
  {l1 l2 l3 l4 : Level} {A : UU l1} {B : UU l2} {C : UU l3} {X : UU l4}
  {f : A → B} {g : B → C} {h : A → X} {i : B → X} {j : C → X}
  where

  is-lift-horizontal-comp :
    is-lift j i g → is-lift i h f → is-lift j h (g ∘ f)
  is-lift-horizontal-comp J I x = I x ∙ J (f x)
```

## Left whiskering of lifts

```md
           A
          ^|
        /  i
      g    |
    /      v
  X - f -> B - h -> S
```

```agda
module _
  {l1 l2 l3 l4 : Level} {A : UU l1} {B : UU l2} {X : UU l3} {S : UU l4}
  {i : A → B} {f : X → B} {g : X → A}
  where

  is-lift-left-whisker : (h : B → S) → is-lift i f g → is-lift (h ∘ i) (h ∘ f) g
  is-lift-left-whisker h H x = ap h (H x)
```

## Right whiskering of lifts of maps

```md
                    A
                   ^|
                 /  i
               g    |
             /      v
  S - h -> X - f -> B
```

```agda
module _
  {l1 l2 l3 l4 : Level} {A : UU l1} {B : UU l2} {X : UU l3} {S : UU l4}
  {i : A → B} {f : X → B} {g : X → A}
  where

  is-lift-right-whisker : is-lift i f g → (h : S → X) → is-lift i (f ∘ h) (g ∘ h)
  is-lift-right-whisker H h s = H (h s)
```

## Properties

### Identification of lifts of maps

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} {B : UU l2} (i : A → B)
  {X : UU l3} (f : X → B)
  where

  coherence-htpy-lift :
    (l l' : lift i f) → map-lift i l ~ map-lift i l' → UU (l2 ⊔ l3)
  coherence-htpy-lift l l' K = (is-lift-map-lift i l ∙h (i ·l K)) ~ is-lift-map-lift i l'
  
  htpy-lift : (l l' : lift i f) → UU (l1 ⊔ l2 ⊔ l3)
  htpy-lift l l' =
    Σ ( map-lift i l ~ map-lift i l')
      ( coherence-htpy-lift l l')
  
  refl-htpy-lift : (l : lift i f) → htpy-lift l l
  pr1 (refl-htpy-lift l) = refl-htpy
  pr2 (refl-htpy-lift l) = right-unit-htpy

  htpy-eq-lift : (l l' : lift i f) → l ＝ l' → htpy-lift l l'
  htpy-eq-lift l .l refl = refl-htpy-lift l

  is-contr-total-htpy-lift : 
    (l : lift i f) → is-contr (Σ (lift i f) (htpy-lift l))
  is-contr-total-htpy-lift l =
    is-contr-total-Eq-structure
      (λ g G → coherence-htpy-lift l (g , G))
      (is-contr-total-htpy (map-lift i l))
      (map-lift i l , refl-htpy)
      (is-contr-total-htpy (is-lift-map-lift i l ∙h refl-htpy))

  is-equiv-htpy-eq-lift :
    (l l' : lift i f) → is-equiv (htpy-eq-lift l l')
  is-equiv-htpy-eq-lift l =
    fundamental-theorem-id (is-contr-total-htpy-lift l) (htpy-eq-lift l)
  
  extensionality-lift :
    (l l' : lift i f) → (l ＝ l') ≃ (htpy-lift l l')
  pr1 (extensionality-lift l l') = htpy-eq-lift l l'
  pr2 (extensionality-lift l l') = is-equiv-htpy-eq-lift l l'

  eq-htpy-lift : (l l' : lift i f) → htpy-lift l l' → l ＝ l'
  eq-htpy-lift l l' = map-inv-equiv (extensionality-lift l l')
```

### The total type of lifts of maps is equivalent to `X → A`

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} {B : UU l2} (i : A → B) (X : UU l3)
  where

  inv-compute-total-lift : total-lift i X ≃ (X → A)
  inv-compute-total-lift =
    ( right-unit-law-Σ-is-contr ((λ f → is-contr-total-htpy' (i ∘ f)))) ∘e
    ( equiv-left-swap-Σ)

  compute-total-lift : (X → A) ≃ total-lift i X
  compute-total-lift = inv-equiv inv-compute-total-lift

  is-small-total-lift : is-small (l1 ⊔ l3) (total-lift i X)
  pr1 (is-small-total-lift) = X → A
  pr2 (is-small-total-lift) = inv-compute-total-lift
```

## Properties

### If `P` is `k`-truncated then the type of lifts of maps is `k`-truncated

```agda
module _
  {l1 l2 l3 : Level} (k : 𝕋) {A : UU l1} {B : UU l2} (i : A → B)
  where

  is-trunc-is-lift :
    {X : UU l3} (f : X → B) →
    is-trunc (succ-𝕋 k) B → (g : X → A) → is-trunc k (is-lift i f g)
  is-trunc-is-lift f is-trunc-B g =
    is-trunc-Π k (λ x → is-trunc-B (f x) (i (g x)))

  is-trunc-lift :
    {X : UU l3} (f : X → B) →
    is-trunc k A → is-trunc (succ-𝕋 k) B → is-trunc k (lift i f)
  is-trunc-lift f is-trunc-A is-trunc-B =
    is-trunc-Σ
      ( is-trunc-function-type k is-trunc-A)
      ( is-trunc-is-lift f is-trunc-B)
  
  is-trunc-total-lift :
    (X : UU l3) → is-trunc k A → is-trunc k (total-lift i X)
  is-trunc-total-lift X is-trunc-A =
    is-trunc-equiv' k
      ( X → A)
      ( compute-total-lift i X)
      ( is-trunc-function-type k is-trunc-A)

module _
  {l1 l2 l3 : Level} {A : UU l1} {B : UU l2} (i : A → B)
  where

  is-contr-is-lift :
    {X : UU l3} (f : X → B) →
    is-prop B → (g : X → A) → is-contr (is-lift i f g)
  is-contr-is-lift f is-prop-B g = is-contr-Π λ x → is-prop-B (f x) ((i ∘ g) x)

  is-prop-is-lift :
    {X : UU l3} (f : X → B) →
    is-set B → (g : X → A) → is-prop (is-lift i f g)
  is-prop-is-lift f is-set-B g = is-prop-Π λ x → is-set-B (f x) ((i ∘ g) x)
```

## See also

- [`orthogonal-factorization-systems.extensions-of-maps`](orthogonal-factorization-systems.extensions-of-maps.md) for the dual notion.