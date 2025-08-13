# Juegos Ordinales de una jugada

## Game frames and Games

Game theory deals with interactive situations where two or more individuals, called players, make decisions that jointly determine the final outcome. In this [video](https://www.youtube.com/watch?v=tBtr8-VMj0E) each of two players, Sarah and Steve, has to pick one of two balls: inside one ball appears the word "split" and inside the other the word "steal" (each player is first asked to secretly check which of the two balls in front of him/her is the split ball and which is the steal ball). **They make their decisions simultaneously**.

![[Pasted image 20250402160100.png]]

What should a rational player do in such situation? It is tempting to reason as follows. Let us focus on Sarah's decision problem. She realizes that her decision alone is not sufficient to determine the outcome; she has no control over what Steven will choose to do. However, she can envision two scenarios: one where Steven chooses **Steal** and the other where he chooses **Split**.

- if Steven decides to *Steal*, then it does not matter what Sarah does, because she ends up with nothing, no matter what she chooses.
- If Steven picks *Split*, then Sarah will get either $50,000 (if she also picks *Splits*) or $100,000 (if she picks *Steal*).

Thus Sarah should choose *Steal*.

The above argument, however, is not valid because it is based on an implicit and unwarranted assumption about how Sarah ranks the outcomes; namely, it assumes that Sarah is selfish and greedy, which may or may not be true. Let us denote the outcomes as follows:

- $o_1:$ Sarah gets $50,000 and Steven gets $50,000
- $o_2:$ Sarah gets nothing and Steven gets $100,000
- $o_3:$ Sarah gets $100,000 and Steven gets nothing.
- $o_4:$ Sarah gets nothing and Steven gets nothings.


If, indeed, Sarah is *selfish and greedy* - in the sense that, in evaluating the outcomes, she focuses exclusively on what she herself gets and prefers more money to less- then her **ranking** of the outcomes is as follows: $o_3 \succ o_1 \succ o_2 \sim o_4$ (which reads $o_3$ is better than $o_1$, $o_1$ is better than $o_2$ and $o_2$ is just as good as $o_4$). 

We cannot presume to know the answer to the question "What is the rational choice for Sarah?" if we **don't know what her preferences are**.



---

A game-frame can be represented - as the image above- by means of a table, with as many rows as the number of possible strategies of Player 1 and as many columns as the number of strategies of Player 2; each row is labeled with one strategy of Player 1 and each column with one strategy of Player 2; inside each cell of the table we write the corresponding outcome. 

### Definition of Game Frame

A *game-frame* in *strategic form* is a list of four items $(I, (S_1, S_2, ,\dots, S_n), O, f)$ where

- $I = \{1,2,\dots,n\}$ is a set of players (n $\geq$ 2)
- $(S_1, S_2, ,\dots, S_n)$ is a list of sets, one for each player. For every Player $i \in I$, $S_i$ is the set of *strategies* (or possible choices) of Player $i$. We denote by $S$ the Cartesian product of sets: $S= S_1 \times S_2 \times \dots \times S_n$ thus and element of $S$ is a list $s = (s_1, s_2,\dots,s_n)$ consisting of one strategy for each player. 
- $O$ is a set of *outcomes*
- $f:S\rightarrow O$: is a function that associates with every strategy profile $s$ an outcome $f(s) \in O$ 

From a game-frame one obtains a *game* by adding, for each player, their **preferences** over (or ranking of) the possible outcomes. 


### Utility function 
There are (at least) four ways of representing, or expressing, a complete and transitive **preference** relation over a set of outcomes. One of them is assigning a number to each outcome, with the convention that ***if the number assigned to $\large o$ is greater than the number assigned to $\large o'$ then  $\large o$  is preferred to $\large o'$***, and if two outcomes are assigned the same number then they are considered to be just as good. 

Such an assignment of numbers is called a *utility function*. A useful way of thinking of utility is as and "index of satisfaction": the higher the index the better the outcome; however, this suggestion is just to aid memory and should be taken with a grain of salt because a utility function *does not measure anything* and, furthermore, as explained below, the actual numbers used as utility indices are completely arbitrary. 


### Definition of Utility function 

Given a complete and transitive ranking of a finite set of outcomes $O$, a function $U:O \rightarrow \mathbb{R}$  (where $\mathbb{R}$ denotes the set of real numbers) is said to be an *ordinal utility function that represents the ranking* if, for every two outcomes $\large o$ and $\large o'$, $U(o) \gt U(o')$ if and only if $\large o \succ \large o'$ and  $U(o) = U(o')$ if and only if  $\large o \sim \large o'$. The number $U(o)$ is called the *utility of outcome* $\large o$. 


