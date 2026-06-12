# Bipartite Graph Matching Challenge

This document is in five parts:
- Description of the challenge
  + Award
- Why solving this problem is important
- My attempts solving the problem (pre LLMs)
- LLM attempts at solving the problem
- Suggestions for the sailors

## Description of the challenge

Let $G = (U, V, E)$ be a bipartite graph with $\lvert U\rvert = \lvert V\rvert = N$, such that $G$ has at least one perfect matching. We label the vertices, both of $U$ and $V$ as $0, 1, \dots, N - 1$. This way, any perfect matching in $G$ can be represented by a permutation of $(0, 1, \dots, N - 1)$.

Let $\pi_1, \pi_2, \dots, \pi_k$ denote all the perfect matchings in $G$. Given an integer $n > 0$, we define a bivariate polynomial $g$ as following

$$g(n, x, y) = \sum_{i=1}^{k} (-1)^{\text{parity}(\pi_i)} [f(\pi_i, x, y)]^n$$

where

$$f(\pi, x, y) = \sum_{j=0}^{N-1}x^jy^{\pi(j)}$$

We define _rank_ of the graph $G$, as the smallest $n$ for which $g(n, x, y)$ is not identically $0$.

Prove that $rank(G) \le \frac{N(N-1)}{2}$, or provide a counterexample.

The formal problem description in Lean4 is [here](./BipartiteMatching.lean). You need to complete the theorem `rank_le_choose_two`, whose body currently consists of a single `sorry`.

Send your proof as a pull request. Or else send a description of the counterexample graph.

### Award
The first person to send a correct proof or counterexample before Dec 31, 2026 would be awarded USD 1500.

## Why solving this problem is important

The above conjecture is closely tied to proving bipartite matching in the complexity class [NC](https://en.wikipedia.org/wiki/NC_(complexity)).

Bipartite matching problem is the problem of determining whether a
[bipartite graph](https://en.wikipedia.org/wiki/Bipartite_graph)
has a 
[perfect matching](https://en.wikipedia.org/wiki/Perfect_matching). It is an open problem if bipartite matching is in NC.

Several related results have been obtained. Specifically, 

- Bipartite matching is known to be in RNC (https://www.math.ias.edu/~avi/PUBLICATIONS/MYPAPERS/KUW86/KarpUW86.pdf)
- Bipartite matching is known to be in quasi NC (https://arxiv.org/pdf/1601.06319)
- Bipartite matching on planar graphs is known to be in NC (https://arxiv.org/html/2405.18833v1)

However, this result (Bipartite matching being in NC) has not been proven or disproven.

It can be shown (outside the scope of this article) that if we can prove that the rank of any graph with $n$ vertices on either side and having at least one bipartite matching is at most $n(n-1)/2$ (or for that matter, bounded by any polynomial in $n$), then bipartite matching would be proven to be in NC. Alternatively, if we can disprove this conjecture, then this line of enquiry comes to a close, and there is no decision on bipartite matching being in NC.

## My attempts to solve this problem

### Initial encounter
I first encountered this problem of proving bipartite matching in NC during my senior thesis, in 2002. My partner, Ajay Verma and I tried to solve this under the guidance of professors Manindra Agarwal and Somenath Biswas. In short 2 months or so we reached a stage where we "just" needed to prove the conjecture about the rank. We spent the remaining 8 months trying to prove the conjecture. It always appeared to us that we are missing something simple, but we could never figure out what. 

We did make some progress though, proving the conjecture for some categories of graphs.

### Rank of complete bipartite graph is $\binom{n}{2}$
We proved that for complete bipartite graphs, the rank is precisely $N(N-1)/2$. This proof is relevant and instructive, so let's go through it right here.

Consider 

$$g(n, x, y) = \sum_{i=1}^{k} (-1)^{\text{parity}(\pi_i)} [f(\pi_i, x, y)]^n$$

for the complete graph for any $n < N(N-1)/2$.

Focus on the inner term for a given matching $\pi$,

$$f(\pi, x, y)^n = {\left( \sum_{i=0}^{N-1} x^i y^{\pi(i)} \right)}^n = \sum_{k} \frac{n!}{k_0! k_1! \dots k_{N-1}!} \prod_{i=0}^{N-1} \left( x^i y^{\pi(i)} \right)^{k_i}$$ 

where the sum is over all partitions $k = (k_0, \dots, k_{N-1})$ such that $\sum k_i = n$.

We will show that each of these $k$ terms inside summation the sign cancel out.

Since $n < N(N-1)/2$ and $\sum k_i = n$, for any given $k = (k_0, \dots, k_{N-1})$, not all $k_i$ will be unique. Take indices $i$ and $j$, such that $k_i = k_j$. If there are multiple possibilities, take the pair with the least $i$.

Now consider a permutation $\pi'$ such that

$$
\begin{equation}
\pi'(r) = 
\begin{cases} 
& \pi(j) & \text{if } r=i \\
& \pi(i) & \text{if } r=j \\
& \pi(r) & \text{otherwise}
\end{cases}
\end{equation}
$$

Note that the parity of $\pi'$ will be opposite of the parity of $\pi$ and thus corresponding term in the expansion of $f(\pi', x, y)^n$ will cancel out.

It is easy to see that all the terms will cancel out in this way.

However, when $n=N(N-1)/2$, then the term

$$\frac{n!}{1! 2! \dots (N-1)!} \prod_{i=0}^{N-1} \left( x^i y^i \right)^i$$

will not cancel (This term will arise only via the identity permutation) and thus $g(n, x, y)$ will not be identically 0.

### Rank of a graph decomposable into complete graphs
Let's consider a bipartite graph $G$ = $(U, V, E)$ such that it can be decomposed into $k$ _complete_ subgraphs 
$(U_1, V_1, E_1), (U_2, V_2, E_2), \dots, (U_k, V_k, E_k)$  
such that 
$U_1 \cup U_2 \cup \dots \cup U_k = U$, 
$V_1 \cup V_2 \cup \dots \cup V_k = V$,
$E_1 \cup E_2 \cup \dots \cup E_k = E$ 

then $rank(G) = \binom{N_1}{2} + \binom{N_2}{2} + \dots + \binom{N_k}{2}$

where $N_i = \lvert U_i\rvert = \lvert V_i\rvert$ for $i = 1, \dots, k$

I am omitting the proof, it is not too difficult with the same ideas as in the previous proof.

## Attempts by AI models
Once the LLMs arrived on the scene, and we started receiving reports of them solving 
previously unsolved problems, I decided to give them a try. As of June 10, 2026, no model was able to solve the problem. Here I describe my attempts with various models.

First I tried the models to get to prove the simpler problem that for complete graph $rank(G) = N(N-1)/2$, both in English and then in Lean4. This is quite easy, and as discussed above, I know its proof. Here is the performance

### Performance on rank for complete bipartite graph

| Model | English Proof| Lean4 Proof |
| --- | --- | --- |
| Claude Opus | Done | Not successful |
| Gemini Pro | Done| Not successful |
| OpenAI codex| Done | [Successful](./CodexBipartite.lean) |
| Claude Fable | Done | [Successful](./FableBipartite.lean) |
| Aristotle | - | Failed |

So, for this simpler problem, English proof was provided by all the models, but only codex and claude fable were able to successfully translate the proof to lean4. So, claude fable is indeed better than claude opus here. But codex is competitive.

I also tried [Aristotle](https://aristotle.harmonic.fun/) which is a tool specifically to generate Lean proofs. Aristotle worked overnight, but by the morning it gave [partial proof](aristotle_attempt.lean) and gave up. For a tool specifically for formal math, I was mildly disappointed.

### Performance on proving the general theorem
Then I tried the models to prove the general theorem, both in English and in Lean4. This is the real deal.

| Model | English Proof| Lean4 Proof |
| --- | --- | --- |
| Claude Opus | Failed | Failed |
| Gemini Pro | Failed| Failed |
| OpenAI codex| Failed | Failed |
| Claude Fable | Failed | Failed |

As you can see, all the models have failed here (and that's why I have created this challenge).
Here are a few details about my attempts.

Claude and OpenAI models _did not hallucinate_. They tried hard to solve the problem and then admitted that they have not been able to solve the problem. In some cases, they reported partial progress that they could make, and pointed to future directions that could be taken. See the screenshots below.

Claude (Fable here) tells the progress, the gap and future line of attack:

<img src="fable.png" width="600">

Codex says that evidence is strong, but it does not have proof:

<img src="codex.png" width="600">

However, Gemini repeatedly proposed incorrect solutions. I needed to expend effort in finding holes in its argument (which were not deep really - an amateur mathematician like me could spot the errors). Every time I found a mistake in the argument, it would accept its mistake and commend me for my sharp observation, and then go on to give another proof with another mistake.

It feels good to be flattered by Gemini:

<img src="gemini_1.png" width="600">

But repeated flattery becomes banal:

<img src="gemini_2.png" width="600">

As a final note, it is comforting to see that a basic prompting does not yield the solution to the problem. Hence, it is not the case that
we missed something elementary. However, given the elegance of the result, I continue to believe that the proof should not be too hard 
to construct, and hence I hope that with suitable prompting, or else combining the state of the art models with specialized provers should 
ultimately work out. Hence, this challenge.

## Suggestions for the sailors
If you are interested in attempting to solve the problem, I encourage you first solve the similar problem of proving
that the rank of complete bipartite graph is $\binom{n}{2}$. A standalone file with this theorem stated is [here](./CompleteGraph.lean). If a model cannot give lean proof for this theorem then there is no hope for proving the general statement.

Good luck!
