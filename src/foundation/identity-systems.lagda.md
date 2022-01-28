---
title: Univalent Mathematics in Agda
---

```agda
{-# OPTIONS --without-K --exact-split --safe #-}

module foundation.identity-systems where

open import foundation.contractible-types using
  ( is-contr; eq-is-contr; eq-is-contr')
open import foundation.dependent-pair-types using (Σ; pair; pr1; pr2)
open import foundation.equivalences using (sec; is-equiv)
open import foundation.fundamental-theorem-of-identity-types using
  ( fundamental-theorem-id)
open import foundation.identity-types using (tr; ap; refl; Id)
open import foundation.propositions using (is-prop-is-contr)
open import foundation.universe-levels using (Level; UU; lsuc; _⊔_)
```

# Identity systems

```agda
module _
  {l1 l2 : Level} (l : Level) {A : UU l1} (B : A → UU l2) (a : A) (b : B a)
  where

  IND-identity-system : UU (l1 ⊔ l2 ⊔ lsuc l)
  IND-identity-system =
    ( P : (x : A) (y : B x) → UU l) →
      sec (λ (h : (x : A) (y : B x) → P x y) → h a b)

module _
  {l1 l2 l3 : Level} {A : UU l1} {B : A → UU l2}
  where

  fam-Σ : ((x : A) → B x → UU l3) → Σ A B → UU l3
  fam-Σ C (pair x y) = C x y

module _
  {l1 l2 : Level} {A : UU l1} {B : A → UU l2} (a : A) (b : B a)
  where

  abstract
    Ind-identity-system :
      (is-contr-AB : is-contr (Σ A B)) →
      {l : Level} → IND-identity-system l B a b
    pr1 (Ind-identity-system is-contr-AB P) p x y =
      tr ( fam-Σ P)
         ( eq-is-contr is-contr-AB)
         ( p)
    pr2 (Ind-identity-system is-contr-AB P) p =
      ap ( λ t → tr (fam-Σ P) t p)
         ( eq-is-contr'
           ( is-prop-is-contr is-contr-AB (pair a b) (pair a b))
           ( eq-is-contr is-contr-AB)
           ( refl))

  abstract
    is-contr-total-space-IND-identity-system :
      ({l : Level} → IND-identity-system l B a b) → is-contr (Σ A B)
    pr1 (pr1 (is-contr-total-space-IND-identity-system ind)) = a
    pr2 (pr1 (is-contr-total-space-IND-identity-system ind)) = b
    pr2 (is-contr-total-space-IND-identity-system ind) (pair x y) =
      pr1 (ind (λ x' y' → Id (pair a b) (pair x' y'))) refl x y

  abstract
    fundamental-theorem-id-IND-identity-system :
      ({l : Level} → IND-identity-system l B a b) →
      (f : (x : A) → Id a x → B x) → (x : A) → is-equiv (f x)
    fundamental-theorem-id-IND-identity-system ind f =
      fundamental-theorem-id a b
        ( is-contr-total-space-IND-identity-system ind)
        ( f)
```