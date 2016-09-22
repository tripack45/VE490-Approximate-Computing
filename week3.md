# VE490 Approximate computing

## Weeekly Report (Week3)
[TOC]
## Mapping Equivalent Functions:

A funtion $ g(x_1, x_2, ..., x_n)$ is said to be equivalent to $ f(x_1, x_2, ..., x_n)$, if there exists a funtion $ M: \mathbb{B}^n \mapsto \mathbb{B}^n$, such that 
$$
f(x_1, x_2, ..., x_n)=g(M(x_1, x_2, ..., x_n))
$$

### Are all functions mapping equivalent?

  Almost.  Suppose $g(X_T) = 1$, $g(X_F) = 0$, an trivial map will be
$$
M(X) = \left\{ \begin{array}  \ X_T , f(X)= 1\\ X_F, f(X) = 0 \end{array}  \right.
$$
  This method fails only if functions contains 0s and 1s only.

## Bijective Equivalence and P Equivalence
Using our previous notation, two functions are called Bijiective-Equivalence, if only if the two functions are mapping equivalent, and **the map $M$ is a bijective map**. 

I believe we have the following lemma:

* Lemma:
  Two functions are bijective equivalent if only if they have the same amount of 1s and 0s.

The definition of P-Equivalence will be, if two functions are P equivalent, there exists a  permuation that results in the same truth table.

* Lemma:
  If two functions are P-Equivalent, they must be bijective equivalent. 
  - Proof: Every permuation corresponds to a bijective map.

