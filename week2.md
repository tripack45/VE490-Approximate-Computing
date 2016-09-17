# VE490 Weekly Report 2

[TOC]

## Concerns

The central question of the research is to find **Whether it is possible, given enoumous computational power,  to simplify an logical circuit using decompositon method**

The basic methodology would be, given a circuit and finite number of gate, iterate through all possible gate combinations, find the one with the minimum error rate.  We would like to prove feasibility by brute force searching all possible combinations.

Issues:
* How to iterate over all possible structures ?
  -  Is there a way to make sure nothing is lost?
  -  Performance: Is it possible to claim that some of the structure are "Equivalent", or some structure can cover all possible cases?
  -  For an LUT structure, is it possible that 2 different configurations ends up an identical truth table. Can we set some sort of "Equivalencies" between different configurations? Can we count the configurations?

A further disscussion can be performed to analyze how increasing t  he number of gates affect the accuracy.
* Is it possible to Assign some sort of "Complexity" to the circuit. Is it possible to claim that some circuits are "Inheritantly" more complex then others.
  - Can we claim that some structure is possible to cover all cases
* Can we have an practical "Upper or lower bound" for the given truth table.

## Chen's work revisited
### Terms:
Denote:
* **LUT** => 2 input, single output logical gate
### Problem Stating:
We begin with the following claim, given a function with $n$ variables:
$$
f(x_1, x_2, x_3, ...., x_n)
$$
It will at most have $2^n$ LUTs. 
We then wish to decompose $f$ into the following form:
$$
f_1(f_2(f_3(f_4(...), x_3), x_2), x_1)
$$
We claim this will have $n-1$ input gates (This is trivial).
We wish to introduce proper error to make this conversion possible.
#### Comments
Note that $n-1$ LUT is the minimum amout of gate that can be used to implement an $n$ input bolean function. This methodology basically challanges us that with the minimum amount of LUTs. how good is the approxiamation.

We also note that this decomposition is not the only way to connect $n$ inputs using $n-1$   to form a function. For a boolean function with 5 inputs, we give the following examples:

![fig1](week2\fig1.png)

There are a bunch of thing that I am not sure of:
* It's unclear whether these are the only 3 possible situations. I guess it is. But I need to prove this... I am also not sure if there exists a way of enumerating them one by one, or essentially counting them... I think it is related to the VE203 Catlan numbers stuff. 
* It's also not clear what "type" of equations can they take care of. I think the so-called "decomposable" theorem can actually help.

I think we can be fairly sure that limiting our self to this particular gate arrangement. is the source of the error. More gates would allow much more complex designs, however much more difficult to handle....
### Searching
It seems other then the obviouse brute force searching algorithm, Chen seems to have invented an accelarated version of the algorithm. This algorithm some how accelerated the entire process by 3x in some cases.
#### Comments
Didn't quite understand the alogorithm, too much jargons unexplained.
### "Approxiamate Core"
Definition
> This is the main process in the approximation approach to approximate a truth table into the one which is decomposible and returns an approximate cost [Chen]

Algorithm:
```python
# input: A truth table
# output: The approxiamated truth table
def approxiamateTable(table: TruthTable):
	row = []
	for column in Table.columns:
		bit = MajorityBit(column) 
		# A majority bit is the bit with most occurance in that row
		# In the case of even number. We leave it as don't care
		column.append(bit)
	# The column formed by the majority bits are called "Majority row"
	# Now for each row, we try to flip some bits and turn them into 
	# one of the following 2 cases:
	# 1) Constant, all 1 or 0
	# 2) The majority row, or its complement
	newTable = TruthTable()
	totalCost = 0
	for row in Table.rows:
		costs, choice = findCostOfCases(row)
		# cost refers to the number of bits needed to change to become the 2 cases
		# compute cost for all cases
		minCost, newRow = getMin(costs, choice)
		# chose the one minimum cost, generate new row
		newTable.AppendRow(newRow)
		totalCost += minCost
```
This completes the analysis on one single partition. Note we still have "Don't cares" in the final results. We decide which literal to choose, by:

> For each “dont-care” bit choice, we can calculate the cost for each row is a list (A,B,C,D). We will find the final cost and choose the minimum final cost with corresponding majority row to be the final approximation result.

I didn't quite understand the "List (A, B, C, D)" part. But he seems to delaying the choice of "Don't care bit" to the end of all approxiamation. He argues that the number of possible choices arrised by "Don't Care" situation will explode exponentially, thus results in an uncomputable situation. *I name this the "Don't-care Disaster"*

*I suppose I will for now ignore this problem, since we are doing feasibility research.*

* The Don't care bound
  To deal with the "Don't care disaster" stated in the above problem. We cut down complexity by limiting the number of Don't Cares.
#### Comments
This method assumes a certain patition on the original function. It wirte the truth table in to the K-Map using that decomposition. 

The algorithm makes deicision for one single LUT. It seems this algorithm works on more general cases other than just the proposed simple structure. 

We expect the algorithm to behave at least as follows:
1. For an already satisfied combination, this algorithm should do nothing.
2. It is expected to do the optimal choice, the choice should minimize the final error rate

1st point is easy to verify. The problem lies in the second one. The algorithm uses a **Greedy Strategy**.  It assumes that the final, optimal circuit, is composed by optimizing the error rate in each step.

I suspect this sub-optimal structure may not hold. This method might very likely miss the real optimal solution by far. **To verify our main concern, we perhaps need to rewrite the searching algorithm in the brutal way**

### Pushing the solution into larger circuits.
Chen proposes this **MFFC**

**THIS PART IS LEFT UNSOLVED**

### Main Theorems

### Conclusion 
This algorithm may not able to get our work here done. This algorithm might miss major pieces, because of 2 things:
1. This algorithm missed many structures.
2. For one single structure, this algorithm cannot yield the optimal solution.
   We discuss how to tackle these 2 problems in the following section

## Investigation of brute force solution
We now would like to go other way around. For each $n$ variable function. Using $n-1$ LUTs, we go through all structures it can form. For each structure, we iterate all possible combination of inputs. We thus find all possible truth table that can be represented using these $n-1$ LUTs. 

Then for every node we wish to simplify, we compare the result with all the truth tables. Find the one with the minimum difference. This is the optimal approxiamate of the given function.

*In practice we can precalculate the data.*

For an $n$ input function:
* In total $2^n$ possible input patterns
* In total $2^{2^n}$ possible functions

### Counting number structures

In order to count the number of all possible structures:
* Observe that each possible structure can be uniquely represented by a binary tree.
* We require that each literal appears only once
* We require literals appears only on leaves
* **The output of an LUT must be connected to the input of one LUT, and 1 only**
* **The input of an LUT must be connected either to another LUT output, or to a literal**

Due to the last 2 requirements, we claim that our binary tree is a full binary tree. We denote each binary tree is a "Structure". 

We must make one further requirement:
* We require when we traverse the tree using prefix order, the literal must appear in the order of $(x_1, x_2, x_3, ... , x_n)$. This further implies we consider the left child and right child of a single tree different.

So we are essentially counting the number of full binary trees. Referencing VE203 course material (P674, Dr. Hohberger), we find the number of full binary trees,  are:
$$
C_{n} = \dfrac{1}{n}\left(\begin{matrix}2(n-1)\\ n-1\end{matrix}\right)
$$
This number is simply the Catlan number.

Example: for $n=4$, we have the following 5 possibilities:

![fig2](week2\fig2.png)

* Some comments:
  We see there exists some "redundancies". Some structures are symmetric to another (1-5, 2-4). Some are effectively the same if we switch the order of literals (1-2).  **I believe what only matters is the number of input.** e.g. for this 4 input example, the only 2 partitioning is ((A,B), (C,D)) or (((A,(B,C)), D). Others all can be derived from these basics. Maybe take a look at this "NPN Equivalance" article may help? *This idea would need further discussion*

### Counting number of cases to be considered.
Note that:
* There are 16 kinds of 2 input LUT
* If the circuit need $n-1$ LUT, then there are $16^{n-1}$ possible functions. 
  - Note many of them are overlaping
* Each possible function would generate a truth table with $2^n$ terms.
* Thus in total the computational expense is
$$
E=C_n * 16^{n-1} * 2^n = \dfrac{2^{5n-4}}{n}\left(\begin{matrix}2(n-1)\\ n-1\end{matrix}\right)
$$
With spatial complexity less then $2^{2^2}$

The following table gives common values:

| $n$  |     Time     |    Sapce     |
| :--: | :----------: | :----------: |
|  3   |     4096     |     256      |
|  4   |    327608    |    65536     |
|  5   |   29360128   |  4294967296  |
|  6   |  2818572288  | Uncomputable |
|  7   | 283467841536 | Uncomputable |

Note when $n=5$, both numbers are around $2^32$. This is perhaps the laregest testing set we can use. It is possible to run an algorithm to verify this idea on 5 input functions.

Note the spacial requirement is verly likely heavily overestimated.
