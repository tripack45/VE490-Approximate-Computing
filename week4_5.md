# VE490 Weekly Report 4&5

[TOC]

## Work Done:
* Read through almost all code base. 
* Focused on the approximation core part.
* Refactored some files to move all the code out of the VM. 
* Wrote script for automatically invoking VCS to perform comparison and calculate error rate.

## A detailed analysis on the current approximation core

### Detailed implementation of Wang
1. Build a whole "possible tree". For example, six inputs 123456 are given, under the root(node) noted with` [123456]`, different branches are listed, such as `[ 1 + 23456]`, `[2 + 13456]`,` ...` ,`[12 + 3456]`,  `[13 + 2456]`,.. ,`[134 + 256]`.. All possible divides are all listed out. Each branch is attached with two nodes describing the inputs of this part and attaching the deeper divides. Basic structure for it is:
``` 
   node  --> branch describing the divide method (many branches)
   branch--> two nodes describing the inputs of two parts
```
2. Build truth table for root node.
3. Starting from root node. For every node, set `Error bound = inf` and enumate every possible divide (all branches).
4. In every branch, build the exact-kmap with the divide method and the truth table attached in node. 
5. Using exact-kmap to get approx-kmap in four differnt ways. 
6. If the least error is larger than error bound, terminate this divide, else calculate the exact least error of this divide by go to step.4 and calculte the two nodes this branch attahced. 
7. Mark the least error divide method and return the error bound under this branch.
8. From the root node, build the divide tree and make .blif file.

Basic data used by Wang:

1. Input size=6.
   Input size can be calculated by the given truth table which is inputs/f_vec.txt. It seems can be modified by changing truth table, but since this program consume great time and space, it is unable to deal with the situation of input size more than 6. Meanwhile a smaller input size is meanless, we could consider the input size always as 6.
2. dont_care_bound=1 and enable_fast_mode=1. 
   It seemed Wang never change the dont care bound parameter. The `enable_fast_mode` is a switch of the dont care case.  1 means leave the dont care bound work. With these two parameters, more testing could be done.
3. Wang leave the access to modify the testing round in a for loop..

Severl points should be mention:

1. **After carefully checking, no greedy algorithm is used in implemtation**. Once a divide method gives an least error less than error bound, the program would calculate the exact least error and compared withe error bound. Thus once the greedy algorithm is added, the time consumption would be greatly reduced. Considering there is no strong proof supporting that the local best solution is a part of the global best solution, we could leave several divide methods based on the least errors and use these divide to calculte a further exact least error. 
2. This program spent great memory in the initial tree building, and since a lot of functions and if are included, this program works not well with very large input size. (As I mentioned above, input size=6 is best for it and when the size becomes 8, the program seems working not so well.) I think, reconstruction is needed and in urgent. Since the introduction of greedy algorithms, maybe a new data structure should be applied  which gather the characters and functions of node and branch in Wang's programs.
3. Here is some small details. 1. Many symmetrical cases should be considered only once.  For example, [123456] could be divide as [14+2356] and [2356+14]. Of course these two cases should and will lead to the same result, the time is spent on the calculting. These should be avoided. 2. While doing approcimation checking, row and column should were to decide. Consider we divide [123456] into [14+2356], which one should we select as row and which one as column. I think it will lead to different time complexity. (Maybe the one with less number should be considered as row? Brief mathematical proof is required and i would try to do that.)
4. Some improvements (many be can't be executed but I think it has meaning and maybe useful): 1. Maybe stochastic algorithms could be applied. Consider that if greedy algorithms is applied and we failed to prove local best solution is a part of global best solution, we would lost the best solution in every divide. Why not we just "try" some divides and select the best solution under these divides? With severl randomly testing, we could get a solution. Depth of searching, numbers and methods of divides seleciton and the times of retesting could be adjusted. 2. Use memory to record state. For example, [123456] could be divide into [35]+[1246], and it could alose be divided into [1]+[23456] and then become [1]+[35]+[246]. There is a same pattern [35], maybe with different kmap. But what is the relationship between these two kmap and since the difference would not be very large (orelse why we doing this project), or can we directly used the same kmap or just take a little modification? I haven't done a further digging but i think these ideas many be helpful.

### Suggestion
1. Wang' program needed reconstruction.
2. Improved algorithms is needed.
3. More tests are required.


## Experiment on the approximation core.

Contraints and assumptions:
* All simplified circuits has 6 inputs.
* All of them are part of an MFFC of an existing circuit
* Timed only the time spent on approxiamation core
* The characteristics of this "Approximation Core" is provided above
* This process begins with MFFC Identification and Ends with Approximation core.


| Source Circuit    | Error Rate | Time Expense |
| ----------------- | ---------- | ------------ |
| c880              | 0%         | 1.8600       |
| c1355             | 0%         | 1.01         |
| c1908             | 0%         | 10.52        |
| c2670             | 0%         | 10.81        |
| c499              | 0%         | 0.7500       |
| rfile9            | 9.38%      | 10.7         |
| rfile6            | 15.62%     | 14.24        |
| bignode (7 input) | ERROR      | ERROR        |

We have to say some tests give nonsense results. Clearly some implementation is buggy. We tried to debug some, but it takes really long time.