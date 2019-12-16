# Optimization II: Integer-valued Optimization


In this chapter we will continue our discussion of linear optimization, but now with the added complexity of considering problems with *integer*-valued decision variables.

The learning objectives for this chapter are:

- Readers should understand how and when integer constraints apply to optimisation problems.

- Readers should know how to use Linear-Program Relaxation (LP Relaxation) to gain insight into integer-constrained problems.

- Readers should understand how to solve integer-constrained optimisation problems.



```r
# Load the libraries we'll use in this chapter
library(ggplot2) # for plotting
library(lpSolve) # for solving linear optimization problems ('linear programming')
```



## Integer-valued decision variables


There are many other examples of integer-valued optimisation problems (i.e., with integer variables). For example,

- how many days to operate a factory; 
- how many cars / products to produce;
- how many shifts to assign an employee

We can also consider the special case of binary-valued decision variables (i.e., when your decision variable $X$ can only take values of 1 or 0).

Binary (Yes/No) Decisions:

- Should I invest or not?    $X_i$ = 1 or 0
- Is worker Bob assigned to shift $j$?    $X_j$ = 1 or 0


And finally, we will also see how we can represent logical constraints using binary decision variables. This allows us to encode constraints like, "if A, then B". 


In an optimisation problem, if some (but not all) of the decision variables are integer-valued, this is called a **mixed-integer** optimisation problem.



## [NOT DONE] From real-valued to integer solutions


```
    [ need to rework examples from lecture to illustrate this more ]
```


Feasible solutions are now integer-valued solutions that lie within the feasible region bounded by the other constraints

- Can we just round off the real-valued answer?
- No. In this case, rounded-answer is not feasible

- Can we just round down the answer or round off to the nearest feasible answer?
- Maybe. But the nearest feasible answer is not guaranteed to be optimal!



We've seen that the optimal integer solution may not necessarily be near the optimal real-valued solution.

But that doesn't mean the real-valued solution is useless. In fact, the most common way to solve the integer optimisation problem is to FIRST solve the problem without the integer constraints.

Solving the optimisation without the integer constraints is called Linear Program [LP] Relaxation, because you "relax" the integer constraints.


If you solve the LP relaxation, and the solution happens to be integer valued, then you are lucky, and that solution is also the optimal solution to the integer problem.

- think back to the example of Farmer Jean. In that case the solution was an integer number of plots of crops, (80, 120)


But this will not often be the case that the solution to the LP relaxation will be integer-valued. Then, programs use the real-valued solution as a starting point to systematically search for the optimal integer solution (e.g. "branch and bound" algorithm used by `lpSolve`).







## Logical Constraints

Some logical constraints can also be formalised as integer constraints. For example, if I choose $X_1$, then I must choose $X_2$ as well. This can be formalised as the following constraint:
$$X_2 - X_1 \geq 0$$
If $X_1$ is 1, then this forces $X_2$ to be 1 too.

In general, the following table summaries how some logical constraints can be represented using binary decision variables:

Logical Condition | Linear Constraint
--- | ---
if $A$ then $B$ | $B - A \geq 0$
if not $A$ then $B$ | $B - (1 - A) \geq 0$, or $A + B \geq 1$
At most one of $A$, $B$ | $A + B \leq 1$
At least one of $A$, $B$ | $A + B \geq 1$
if ($A$ or $C$) then $B$ | $B - (A+C) \geq 0$
if ($A$ and $C$) then $B$ | $B - (A+C) \geq -1$, or $(A+C) - B \leq 1$
if $A$ then ($B$ or $C$) | $(B+C) - A \geq 0$
if $A$ then ($B$ and $C$) | $(B+C) - 2A \geq 0$


### Logical Constraints Example: Planning university courses

Let's see logical constraints in action in the following example.

Natalie has decided to switch her career to data science, as she feels it has more prospects than her previous industry. She is eyeing a "Pico-masters (TM)" program at her local university, where she has to complete 40 units of courses to satisfy the pico-degree. ("Pico-masters" and "nano-masters" are fictional---at least, for now---but some online education platforms and universities like edX are offering "MicroMasters<sup>&reg;</sup>" and other similar products.)

The program offers the following courses, in **ST**atistics, **P**ro**G**ramming, and **D**ata **M**anagement, along with their associated costs and pre-requisites. The pre-requisites for each course must be fulfilled before students are allowed to take that course. In order to finish the PicoMasters, she needs to finish a specialization in one of the three tracks, which is fulfilled by completing the "Level 3" version of that course. Natalie has also indicated her personal interest in each course, in the following table.


Course | Units | Pre-requisites | Interest
--- | --- | --- | --- 
ST1 | 10 | - | 8 
ST2 | 10 | ST1 | 4 
ST3 | 10 | ST2 | 6 
PG1 | 10 | - | 7 
PG2a | 10 | PG1 | 5 
PG2b | 10 | PG1 | 6
PG3 | 10 | PG2a *or* PG2b | 3 
DM1 | 10 | - | 4
DM2 | 10 | DM1 | 6
DM3 | 10 | DM2 | 7



Imagine that her goal is to maximize her interest and satisfy the requirements of the degree, while also taking exactly 40 units (in order to keep costs low). Which classes should she take?


First, let's define our decision variables, by just using $X_1$ to $X_{10}$ to correspond to taking each of the ten courses, in the order in the table above. That is,

$X_1$ = Take ST1, $X_2$ = Take ST2, $X_3$ = Take ST3, <br>
$X_4$ = Take PG1, $X_5$ = Take PG2a, $X_6$ = Take PG2b, $X_7$ = Take PG3, <br>
$X_8$ = Take DM1, $X_9$ = Take DM2, $X_{10}$ = Take DM3

Now let's tackle the constraints one-by-one.

Consider the following pre-requisite: 
```
Natalie needs to take ST1 before being allowed to take ST2. 
```

We can represent this using the following constraint:

$$ X_1 - X_2 \geq 0 $$

You can easily check if this is correct by 'testing' it by subtituting values. If Natalie takes ST2 (i.e., $X_2 = 1$), then we have the following inequality: $X_1 - 1 \geq 0$. In order for this also to be true, we need $X_1$ to also be 1. This means that Natalie taking ST2 ($X_2 = 1$) actually forces her to also have taken ST1 ($X_1 = 1$).

Similarly, we can write out the pre-requisite constraints for the rest. Now, let's consider one more case:

```
Natalie needs to take either PG2a OR PG2b before being allowed to take PG3. 
```

One easy thing to try is to replace ST2 ($X_2$) in the previous inequality with PG3 ($X_7$), and now we have to replace ST1 ($X_1$) with (PG2a or PG2b), or ($(X_5+X_6)$). So let's try:

$$ (X_5 + X_6) - X_7 \geq 0 $$

And actually this is the correct inequality! Let's test this out. If Natalie takes PG3, then $X_7=1$, and so we are left with:
$(X_5 + X_6) - 1 \geq 0$.
This inequality will be satisfied if $X_5$ is 1, or if $X_6$ is 1, that is, if she takes either PG2a or PG2b. (Note that this will also be satisifed if BOTH $X_5$ and $X_6$ is 1 -- there is nothing stopping her from taking both PG2a and PG2b!)


And finally let's consider the constraint that she needs to finish a specialization. That is, she needs to take either ST3 ($X_3$) or PG3 ($X_7$) or DM3 ($X_{10}$). This means that at least one of them needs to be 1. So we can just write:

$$ X_3 + X_7 + X_{10} \geq 0 $$

Please make sure you understand how we got (or how we tested/verified) the above inequalities!

Now, we can finally write out our optimization problem in a large table:



Decision variables <br> $X_1$ = Take ST1, $X_2$ = Take ST2, <br> $X_3$ = Take ST3, <br> $X_4$ = Take PG1, $X_5$ = Take PG2a, <br> $X_6$ = Take PG2b, $X_7$ = Take PG3, <br> $X_8$ = Take DM1, $X_9$ = Take DM2, <br> $X_{10}$ = Take DM3 | Maximize Interest = <br> 8$X_1$ + 4$X_2$ + 6$X_3$ + 7$X_4$ + 5$X_5$ + <br> 6$X_6$ + 3$X_7$ + 4$X_8$ + 6$X_9$ + 7$X_{10}$
--- | --- 
Subject to |  
Minimum Course Requirements (= for cost savings) | 10 $X_1$ + 10 $X_2$ + 10 $X_3$ + 10 $X_4$ + 10 $X_5$ + <br> 10 $X_6$ + 10 $X_7$ + 10 $X_8$ + 10 $X_9$ + 10 $X_{10}$ $=$ 40
Pre-Requisite for ST2 | $X_1 - X_2 \geq 0$ <br> or <br> 1 $X_1$ + (-1) $X_2$ + 0 $X_3$ + 0 $X_4$ + 0 $X_5$ + <br> 0 $X_6$ + 0 $X_7$ + 0 $X_8$ + 0 $X_9$ + 0 $X_{10}$ $\geq$ 0
Pre-Requisite for ST3 | $X_2 - X_3 \geq 0$ <br> or <br> 0 $X_1$ + 1 $X_2$ + -1 $X_3$ + 0 $X_4$ + 0 $X_5$ + <br> 0 $X_6$ + 0 $X_7$ + 0 $X_8$ + 0 $X_9$ + 0 $X_{10}$ $\geq$ 0
Pre-Requisite for PG2a | $X_4 - X_5 \geq 0$ <br> or <br> 0 $X_1$ + 0 $X_2$ + 0 $X_3$ + 1 $X_4$ + -1 $X_5$ + <br> 0 $X_6$ + 0 $X_7$ + 0 $X_8$ + 0 $X_9$ + 0 $X_{10}$ $\geq$ 0
Pre-Requisite for PG2b | $X_4 - X_6 \geq 0$ <br> or <br> 0 $X_1$ + 0 $X_2$ + 0 $X_3$ + 1 $X_4$ + 0 $X_5$ + <br> -1 $X_6$ + 0 $X_7$ + 0 $X_8$ + 0 $X_9$ + 0 $X_{10}$ $\geq$ 0
Pre-Requisite for PG3 <br> (PG2a OR PG2b) | $(X_5 + X_6) - X_7 \geq 0$ <br> or <br> 0 $X_1$ + 0 $X_2$ + 0 $X_3$ + 0 $X_4$ + 1 $X_5$ + <br> 1 $X_6$ + -1 $X_7$ + 0 $X_8$ + 0 $X_9$ + 0 $X_{10}$ $\geq$ 0
Pre-Requisite for DM2 | $X_8 - X_9 \geq 0$ <br> or <br> 0 $X_1$ + 0 $X_2$ + 0 $X_3$ + 0 $X_4$ + 0 $X_5$ + <br> 0 $X_6$ + 0 $X_7$ + 1 $X_8$ + -1 $X_9$ + 0 $X_{10}$ $\geq$ 0
Pre-Requisite for DM3 | $X_9 - X_{10} \geq 0$ <br> or <br> 0 $X_1$ + 0 $X_2$ + 0 $X_3$ + 0 $X_4$ + 0 $X_5$ + <br> 0 $X_6$ + 0 $X_7$ + 0 $X_8$ + 1 $X_9$ + -1 $X_{10}$ $\geq$ 0
Complete Specialization | $X_3 + X_7 + X_{10} \geq 1$ <br> or <br> 0 $X_1$ + 0 $X_2$ + 1 $X_3$ + 0 $X_4$ + 0 $X_5$ + <br> 0 $X_6$ + 1 $X_7$ + 0 $X_8$ + 0 $X_9$ + 1 $X_{10}$ $\geq$ 1
Binary, Integer, Non-Negativity Constraints | $X_1$, to $X_{10}$ all binary, integers and $\geq 0$.


Notice that I've written out the inequalities into longer forms with explicit "0"s, so

$$X_1 - X_2 \geq 0$$ becomes $$X_1 + (-1) X_2 + 0 X_3 + 0 X_4 + 0 X_5 + 0 X_6 + 0 X_7 + 0 X_8 + 0 X_9 + 0 X_{10} \geq 0$$

This makes it much easier to type this into code. This is the hardest part now, transfering this into code without making any mistakes!




```r
#defining parameters
objective.fn <- c(8, 4, 6, 7, 5, 6, 3, 4, 6, 7)
const.mat <- matrix(c(rep(10,10),
                      rep(0,0), 1, -1, rep(0, 8), # ST2
                      rep(0,1), 1, -1, rep(0, 7), # ST3
                      
                      rep(0,3), 1, -1, rep(0, 5), # PG2a
                      rep(0,3), 1, 0, -1, rep(0, 4), # PG2b
                      rep(0,4), 1, 1, -1, rep(0, 3), # PG3
                      
                      rep(0,7), 1, -1, rep(0, 1), # DM2
                      rep(0,8), 1, -1, rep(0, 0), # DM3
                      
                      rep(0,2), 1, rep(0,3), 1, rep(0,2), 1 # complete specialization
                      ) , ncol=10 , byrow=TRUE) 
const.dir <- c("=", rep(">=", 8))
const.rhs <- c(40, rep(0,7), 1)

#solving model
lp.solution <- lp("max", objective.fn, const.mat, const.dir, const.rhs, 
                  binary.vec = c(1:10))
lp.solution$solution #decision variables values 
```

```
##  [1] 1 1 1 1 0 0 0 0 0 0
```

```r
lp.solution
```

```
## Success: the objective function is 25
```


Thus, the final solution is $X_1$, $X_2$, $X_3$, $X_4$ = 1, with the rest = 0.

In order to maximize her interest while completing the degree, Natalie should choose to specialize in Statistics (taking ST1, ST2, ST3), and then also taking the first course in Programming (PG1).




## Integer Optimization Summary

In this lecture we've covered how to use linear optimisation to solved integer-valued optimisation problems, where some of the decision variables are constrained to be integers, or even constrained to be binary variables (which can be used to model yes/no decisions).

We've also seen how to transform logical constraints into linear constraints using binary variables. And we've also seen worked examples of problems with 20+ decision variables -- these problems scale up very fast!

The output of these problems can be very useful in helping managers make optimal decisions based on the data, and is the whole objective behind prescriptive analytics.


## [NOT DONE] Exercises

```
    to be completed
```

Q1)
John is interested in buying ads to market his new startup. He sees the following options:

Ad | Cost per ad | Reach | Limits
--- | --- | --- | ---
Radio Ad | \$100 | 500 | 40 
Newspaper Ad | \$250 | 2000 | 10
Social Media Ad | \$50 | 300 | 80

The "limits" in the table above are imposed by each advertiser, so the Newspaper will only run a maximum of 10 Newspaper Ads. Reach is an estimated number of people that the ad will reach, per ad that John buys (e.g. if he buys 1 Radio ad, it will reach 500 people. If he buys 2, it will reach 1000 people.)


Q1a) Identify the decision variables, objective function and constraints. Write out the optimization problem in a table.

Q1b) Write R code to solve this problem. What is the optimal solution, and what is the value of the objective function that this optimal solution?







```
    something similar to the distribution center example?
```















