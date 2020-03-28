# Project: Learning simple math

### Problem formulation (adding two integer numbers)

Create a model that is able to learn to add two integer numbers, e.g given "97+53" return "150".

The model should learn the algorithm of breaking the problem down to first adding 3 + 7 = 0 (carry = 1) followed by 
5 + 9 + 1 (carry) = 5 (carry = 1) and finally 0 + 0 + 1 (carry) = 1. From here the output can be copied from the 
results (backwards): 150

The idea is to split the model up in an "associative" (System 1) part, and a "reasoning" (System 2) part. The job
of System 1 will be to perform simple one digit addition (plus carry), while System 2 will need to learn how to solve
the problem by decomposing it into smaller problems that can be solved by System 1.

We formulate the problem as a sequence prediction problem, with the following vocabulary:
 - "0" - "9" : Represent the 10 digits
 - "+" : Represents the addition operator
 - "Q" : Start of query to System 1
 - "E" : End of query to System 1
 - "F" : Start of final output
 - "!" : End of final output

The model will be given a string like `97+53`. System 2 should then decompose it into several "queries" to System 1. A
query is constructed like `Q0+7+3E`, which means System 1 is supposed to retrieve the answer to 0 + 7 + 3, where the 0
is a carry bit. System 1 will respond with two tokens `10` where the first digit is a carry bit, and the second is the
remainder. 

Let's take a concrete example. The model is given the following input:
```
97+53
```
Based on this, System 2 is expected to generate the following string of tokens constituting a query to System 1:
```
Q0+7+3E
```
The following string will trigger System 1, which is expected to generate the the string:
```
10
```
Note that the `0` is the first digit of the final answer, while the carry bit is needed for the next single digit 
addition. System 2 is now expected to generate the string
```
Q1+9+5E
```
where the `1` is taken from the previous System 1 output. System 1 should generate
```
15
```
From here, System 2 should realize that all necessary computations have now been performed, and that the final answer 
can now be copied from the partial results. System 2 should output
```
F150!
```
Concatenating all strings, the full example becomes:
```
97+53Q0+7+3E10Q1+9+5E15F150!
```

### Why is this interesting?

 1. How should System 2 be trained?
   - How does supervised loss vs reinforcement loss affect sample efficiency?
 2. How should System 1 be trained?
   - De-noising objective
   - Jointly with system 2. How does that affect sample efficiency?


### Execution plan

- [ ] Hard-coded (perfect) System 1. Train System 2 using supervised loss => Baseline for sample efficiency
- [ ] Train System 1 and System 2 jointly using supervised loss. Also train System 1 with de-noising loss
- [ ] Hard-coded (perfect) System 1. Train System 2 using reinforcement loss
- [ ] Train System 1 using de-noising + reinforcement loss. Train System 2 using reinforcement loss.


## Using the repo

Build docker:

```bash
./scripts/build.sh
```

Run docker:

```bash
./scripts/run.sh [gpu] [notebook] [tensorboard]
```
