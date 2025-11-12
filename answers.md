# CMPS 6610 Problem Set 04
## Answers

**Name:** Leonardo Matone


Place all written answers from `problemset-04.md` here for easier grading.




- **1d.**
There is a general trend here: the content type (and how frequent the chars within it are) tend to affect the compression we are able to achieve with Huffman coding. Text like asyoulik.txt and alice29.txt are solidly compressible at around .65.-.69, whereas source code like fields.c is less compressible at .72. As our alphabet size goes up, the huffman vs fixed ratio increases and we are able to compress less. Notably, this is not well correlated with the actual size of the file, only the uniqueness of their contents.

```
+--------------+---------------------+----------------+--------------------------+
|     File     | Fixed-Length Coding | Huffman Coding | Huffman vs. Fixed-Length |
+--------------+---------------------+----------------+--------------------------+
|    f1.txt    |         1340        |      826       |    0.6164179104477612    |
| grammar.lsp  |        26047        |     17356      |    0.6663339348101509    |
|   fields.c   |        78050        |     56206      |    0.7201281229980782    |
| asyoulik.txt |        876253       |     606448     |    0.6920923523229022    |
| alice29.txt  |       1039367       |     676374     |    0.6507557003445367    |
+--------------+---------------------+----------------+--------------------------+
```



- **1e.** (changed from 1d to 1e)

This either renders the Huffman coding approach identical to the fixed length approach, or slightly more optimal depending on the size of the alphabet.

When the size of the alphabet results in a balanced tree, the Huffman coding matches the fixed length approach. But in all other cases, the Huffman coding performs slightly better, ascribing an n-length encoding to most leaves and an m-length encoding to a smaller fraction of the leaves (where m<n). 

At the worst case, this matches the fixed length approach. At the best case, we only slightly outperform it. We can define this explicitly:

Huffman (uniform frequencies)
\[
L_{\text{Huff}}(|\Sigma|)=\left\lceil \log_2 |\Sigma| \right\rceil-\frac{2^{\lceil \log_2 |\Sigma| \rceil}-|\Sigma|}{|\Sigma|}
\]

Fixed-length
\[
L_{\text{Fixed}}(|\Sigma|)=\left\lceil \log_2 |\Sigma| \right\rceil
\]

Special case
\[
|\Sigma|=2^k \;\Rightarrow\; L_{\text{Huff}}(|\Sigma|)=L_{\text{Fixed}}(|\Sigma|)=\log_2 |\Sigma|
\]


- **2a.**

Treat array $A$ as a nearly complete binary tree, then iterate down from $n/2$ to $1$ swaping with smaller childs until the min-heap property holds.

1. Suppose $A$ is a tree in array form with the form: **Node**$=i$, **Left**$=2i$, **Right**$=2i+1$
2. Iterate over $n/2$ to $1$:
    3. Compare $i$ to **Left** and **Right**
    4. If it's already less than both, do nothing
    5. If it's not, swap it with the smaller child and continue sifting down from the child
6. When we reach index 0, we match the min-heap property.


- **2b.**


This is inherently $O(n)$, but the span can be $O(\log n)$. From a bottom-up approach, we can build up from the nodes in parallel. This intuitively leaves us with the longest chain starting from the bottom-most leaf to the root, which is $\log n$.


- **3a.**

The greedy choice here is simple: choose the largest possible denomination of coin that fits within $N$. Then recurse on the $N-$ largest_denom remaining.

- **3b.**

Let $2^k$ be the largest coin less than or equal to N. We can prove the greedy choice property by counterexample:
1. Any set of coins strictly smaller than $2^k$ that sums to $2^k$ must contain at least two coins, since the next smallest coin is $2^{k-1}$. 
2. Intuitively swapping the largest coin out for at least two coins weakly increases the coin count and never reduces it.

Hence, there exists an optimal solution that uses the $2^k$ coin and the greedy choice property is maintained.

After taking $2^k$, the remainder $R$ is less than $2^k$, so only coins less than $2^k$ are relevant. If the greedy choice for $R$ were not optimal, replacing it with a better one would improve the total for $N$, contradicting optimality, an optimal solution to $N$ consists of the greedy choice $2^k$ plus an optimal solution to $R$.

- **3c.**

The work is $O(\log n)$, directly proportional to the number of bits. Supposing $N$ is given in binary, we effectively iterate over the bits of $N$ and pop them as we iterate, which requires one operation per bit, and $\log N$ bits.

The span (I think) could be $O(1)$. Because we deal in powers of 2 and holding our binary assumption, we can easily process our binary input in parallel to figure out what coins are needed. 101001 is just $2^0 + 2^3 + 2^5$ with a simple map application.

- **4a.**

Because we cannot be guranteed to make change, our greedy assumption does not hold. If we take the largest denomination less than $N$, there may not exist a combination of coins that can get us to $N$ total. Specific example:

Suppose we have denominations 4, 2, 5, and $N$ = 6. Our greedy choice is 5, but this prevents us from reaching the correct solution. In this case, we would need to cointer-intuitively (pun intended) choose 4 and 2 in order to get our solution.

- **4b.**

Optimal substructure: For $N > 0$ and $d \in D$, the optimal construction for $N$ ends with $\exists d \in D$. After taking that coin away, what remains is the best way to make $N - d$. This is the crux of the substructure.

**Proof:**

First we prove that this is greater than or equal to the optimal solution: Take any optimal solution $S$ for amount $n$. Let its last coin be some $d \in D$. If we remove that coin, the remaining multiset makes $n-d$ using $|S|-1$ coins. If this reimainder were not optimal for $n-d$, there would exist a solution with fewer than $|S|-1$ coins and adding back $d$ would result in a contradiction..

Now we prove that this is less than or equal to the optimal solution: Take any alternate $d \in D$ with the minimum on the right, excepting impossible combinations. Take an optimal solution for $n-d$ with $optimal(n-d)$ coins and append an additonal $d$ coin. This forms a feasible solution for $n$ with 1$+optimal(n-d)$ coins.

Both directions combined prove equality, demonstrating that an optimal solution for n decomposes into a single final coin plus an optimal solution for the amount $n-d$.


- **4c.**

Let $D$ be the set of coin values, and $k=|D|$.
1. Initialize an array: optimal[0 -> N] with optimal[0] = 0 and optimal[n] = infinity, initialize previous[0 -> N] = d
2. Iterate over all n $\in$ N:
    3. for each coin $d \in D$ where $d <=n$:
        4. If optimal[n - d] + 1 < optimal[n]:
            set optimal[n] = optimal[n - d] + 1
            set previous[n] = d
5. The minimum number of coins is optimal[N] (infinity if impossible)
6. While N > 0, output previous[N], set N = N - previous[N]


- **5a.**

**Proof:**

We first list all jobs in order of finish time from earliest to latest. For any position j in this list, we describe the problem that only allows to choose from the first j jobs the "size-j subproblem". An optimal solution is a contiguous subset which maximizes the total value from each job in the subset.

To start, we take an optimal solution to the size-j subproblem and look at the jth job. This job is known to finish latest among the first j according to our definition. There are only two possibilities for this j: The optimal solution includes j, or it does not include j. 

In the case that we do include j, then this solution uses jobs from the first j-1 positions. If tehre were a better feasible subset from those jobs, we could replace what we picked with that better subset, contradicting the assumption that we started with an optimal solution for size j.

In the case that we do include j, then every other job must finish no later than the start of job j- so aside from job j, everything we picked is constrained within the earlier prefix that ends right before job j's start time. But what if the remainder we picked on that earlier prefix were not optimal? Then we could swap it out for an earlier subset on that prefix, keep job j, and strictly increase our value, which contradicts optimality. 

These two cases demonstrate that the optimal solution to the size-j subproblem is composed of a smaller optimal solution, which meets the optimal substructure property. 


- **5b.**

No, this the greedy choice property does not hold for this problem. 

Counterexample 1: 
1. job_1 = [0,2], v=2
2. job_2 = [2,4], v=2
4. job_3 = [0,4], v=5

Greedy by earliest finish picks job 1 and job 2 for total of 4, but the optimal choice is job 3 with 5.

Counterexample 2:
1. job_1 = [0,5], v=120 
2. job_2 = [0,3], v=100 
3. job_3 = [3,4], v=50 
4. job_4 = [4,5], v=50

Greedy by earliest finish picks job 1 for at total of 120, but the optimal choice is job 2, 3, 4 with a total of 200.


- **5c.**

1. Sort the jobs by their finish times, earliest first
2. For each job j, compute predecessors:
    3. predecessors[j] = the index of the rightmost job that finishes no later than j
4. For each job j, initialize arrays best/take:
    5. best[j] will store the best total value corresponding to jobs 0-j
    6. take[j] will store true if we include job j in an optimal solution, otherwise false.
7. Populate the arrays iterating over all j:
    8. Decide whether to skip or take j:
        9. If we skip, the best value stays the same as for the previous job
        10. If we take it, the jobs value is the value plus the best value from its last non-overlapping predecessor
    11. Keep the larger of the two, and note whether we take or skip.
12. Recover chosen jobs from last job iterating backwards
    13. If a job was marked take, add it to the answer and jump to its recorded predecessor
    14. If it was skip, just step to the next (previous) job.


To dervive work and span, we can break into each sequential piece and analyze appropriately:
1. Sorting:
$Work(n) = O(n \log n)$
$Span(n) = O(\log n)$
2. Predecessors: (scan)
$Work(n) = O( n)$
$Span(n) = O(\log n)$
3. Dynamic programming pass: (constant comparison per job)
$Work(n) = O(n)$
$Span(n) = O(n)$
4. Reconstruction:
$Work(n) = O(n)$
$Span(n) = O(n)$

We do a lot of work here, but the overall work is dominated by sorting, while overall span is dominated by our dynamic programming pass, yielding:

$Work(n) = O(n \log n)$
$Span(n) = O(n)$