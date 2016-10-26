# VE490 Weekly Report 7

[TOC]

## Work Done:

* Trying to design a good structure for the CPP implementation of the code.
* Target of the design involves the following:
  * Dis-entangle the dependence of the classes. We would like to remove the dependence of the searching order and the actual shape of the search tree.
  * Keeping the memory usage down while maintaining flexibility. Normally preserving more information means better flexibility (flexibility on search order, etc.). We would like to maintain that while keep as less information as possible.
  * Design in a way that allows us to easily replace previous component of algorithm with new ones. This will allow us to experiment other ideas in the future, including quick implementation of algorithms like A*, applying new approximation core etc.

We have come up a design. For the detail we will present some header files. 

Sorry this week I have midterms going on, so there aren't much work.