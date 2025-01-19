# PAL
> Standardní grafové algoritmy s polynomiální složitostí. Kombinatorické a číselně teoretické algoritmy, izomorfismus, prvočíselnost a pseudonáhodná čísla. Vyhledávací stromy a haldy, jejich využití. Vyhledávání ve vícedimenzionálním prostoru. Přesné a přibližné vyhledávání v textu založené na konečných automatech.

## Standardní grafove algoritmy s polynomiální složitostí
- A graph $G(V,E)$ is a set of verticies $V$ and edges $E$
- Incidence
	- If two nodes $u,v$ are linked by edge $e$, nodes $u,v$ are said to be incident to edge $e$ or edge $e$ is incident to nodes $u,v$
- handshaking lemma
	- $\sum_{v\in V} deg(v)=2|E|$

### BFS
```python
def bfs(graph, start):
    visited = set([start])
    queue = Queue([start])

    while not queue.is_empty():
        vertex = queue.pop()

        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.push(neighbor)
```

### DFS
```python
def dfs(graph, start):
    visited = set([start])
    stack = Stack([start])

    while not stack.is_empty():
        vertex = stack.pop()

        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                stack.push(neighbor)
```
or recursive
```python
def dfs(graph, vertex, visited=None):
  if visited is None:
      visited = set()
  visited.add(vertex)

  for neighbor in graph[vertex]:
      if neighbor not in visited:
        visited.add(vertex)
        dfs(graph, neighbor, visited)
```
### Minimum Spanning Tree (MST)
- MSP $H$ is a subgraph of $G$, such that $H$ is connected, has all the vertices and the sum of weights in $H$ is minimal

#### Jarník / Prim algorithm
- initialize $H$ by starting in any vertex
- in each step, add to $H$ the edge with the lowest value that neighbours $H$, update the min queue with new edges that are now reachable from the newly added vertex
- runs in almost linear time for dense graphs
- time complexity is:
  - $O(V^2)$ with adjacency matrix
  - $O(E \cdot \log V)$ with adjacency list + binary heap
  - $O(E + \log V)$ with Fibonacci heap + adjacency list

#### Kruskal algorithm
- Orders edges based on their value
- In each step adds the cheapest edge to MST, unless it creates a cycle, this can be confirmed by checking if the two vertices connected by the edge we are adding to the graph aren't already in a connected component
  - An efficient data structure for keeping track of which elements are in which set is Union-Find, this structure has two methods
    - **<span style="color:#FFB3B3">union</span>**: merge two trees into one by assigning the parent of one subset under the parent of the other set
      - assigning the parent of the deeper tree under the parent of the shallower tree results in better runtime complexity
    - **<span style="color:#B3D9FF">find</span>**: return the parent of the tree that x is in, works by traversing the tree upwards until root is reached

#### Boruvka algorithm
- Each vertex begins as its own component
- In each step, each component looks which other component is closest to it, it joins with that component forming a new one, this repeats until only one component remains, that is the MST
- A tie-breaking rule is needed if the graph contains edges with identical weights, an example is to give each vertex an id and add edges which connect vertices with lower ids (i.e. prefer edge (1,4) over edge (2,3) because min(1,4) < min(2,3)), there are also other rules, in principle we need a mechanism that uniquely orders edges so that different components make the same choices
- for keeping track of the components Union-Find is again a suitable data structure
- time complexity is $O(E \log V)$
- Can be run in linear time for planar graphs

### Strongly connected components (SCC)
- SCC is a maximal subgraph in which a walker can freely move and can reach each node in SCC from every other node
#### [Tarjan](https://youtu.be/wUgWX0nc4NY?si=-9GhUq0EXey_cpCp)
- start dfs from some random vertex
- as each node is discovered assign it an id, and a low-link value that is equal to the id, we also push it to a stack (the stack signals that the node is explored but not yet in a component)
- once the dfs ends the backtracking starts
  - during the backtracking (return from the recursion) if the dfs stopped at a node that is on the stack we update the low-link values for all nodes we are returning trough with
  ```python
  if (on_stack[to])
    low_link[at] = min(low_link[at], low_link[to])
  ```
- Then after the dfs if the backtracking ended up in a node where the node_id is equal to its low_link value, and we explored all the neighbours of that node, we found a SCC. Now we need to pop all the nodes with the same low_link value from the stack, the low_link value marks their component.

### Isomorphism
- Two graphs are isomorphic if we can get a 1:1 mapping (bijection) that preserves the adjacency relationships between vertices
- Checking isomorphism exactly in a general case is expensive (though it is not know if it is polynomial or np-complete) because of the combinatorial nature of the problem where we have to try each possible mapping
- It can be sped up with invariants $\phi()$
  - for invariants it holds that if $G_1$ and $G_2$ are isomorphic then $\phi(G_1)=\phi(G_2)$
  - invariants can for example be the number of vertices, number of edges, degrees of vertices, ...
  - invariants are necessary though not sufficient for isomorphism

### Euler trail
- An Euler trail visits every edge exactly once
- A graph G has an Euler trail if and only if it is connected and has 0 or 2 vertices of odd degree

### Hamiltonian path
- visits every vertex exactly once
- finding such path is np-complete

## Kombinatorické a číselně teoretické algoritmy
- n - number of objects
- r - sample size
- combination: order doesnt matter
	- [0,1] == [1,0]
- permutation: order does matter
	- [0,1] != [1,0]
- repetitions $\rightarrow$ each object can be present multiple times in the sequence
- combinations nCr without repetition
  $$\binom{n}{r}=\frac{n!}{r!\cdot(n-r)!}$$
- combinations with repetition
  $$\frac{(n+r-1)!}{r!\cdot(n-1)!}$$
- permutations nPr without repetition 
  $$\frac{n!}{(n-r)!}$$
- permutations with repetition
  $$
  n^r
  $$
### Ranking & Unranking
- it is useful to order combinations/permutations in lexicographical order and assign id to each
**Rank**
- then we can generate rank given a perm/comb
**Unrank**
- generate a perm/comb given a rank

### Gray code
- binary numeral system where following values only differ in one bit
- rotacni enkodery
- coverting binary to gray
	- rewrite MSB
	- compare neighbouring numbers
	- if same => write 0
	- if different => write 1
- gray to binary
	- rewrite MSB
	- compare last grey bit and next binary
	- if same => write 0
	- if different => write 1

## Prvočíselnost a pseudonáhodná čísla

### Prime numbers

#### The Sieve of Eratosthenes
- given $n$ numbers
- for each number in $[2,\ldots,\sqrt{n}]$
  - mark number as prime
  - mark all multiples of the number < $n$ as not prime
  - complexity $O(n\cdot\log\:\log\:n)$

### Prime decomposition

### Random number generators
- true random numbers are not possible, what we have are formulas that start with a number (seed) and generate a sequence of pseudorandom numbers
- a good generator should be
  - uniform
  - fast
  - have simple formula

#### Lehmer
- $x_{n+1}=A\cdot x_n \: mod\:M$
- Conditions for maximal period
	- $M$ is prime
	- $A$ is primitive root modulo $M$
		-   $A$ is primitive root modulo $M$ if
			- $A^x\:mod\:M$ can generate all numbers $<0, M-1>$
			-   For some $x \in \mathbb{Z}$

#### Linear Congruental Generator
- $x_{n+1} = (Ax_n + C)\:mod\:M$
- maximum period lenght
- Hull-Dobell theorem
	- length is max, e.g. equal to M if
	1. $C$ and $M$ are coprimes (nemaji spolecneho delitele krom 1)
	2. $A-1$ is divisible by each prime factor of $M$ 
	3. If $4$ divides $M$, $4$ also divides $A-1$ 

#### Blum Blum Shub Generator
- $x_{n+1}=x_n^2\:mod\:M$

## Vyhledávací stromy a haldy, jejich využití

### Binary search tree

### Skip list

### B-trees

### Red-black tree

### Binary heap

#### D-ary heap

### Binomial heap

### Fibonacci heap

## Vyhledávání ve vícedimenzionálním prostoru

### K-d tree
- trees used for searching in point clouds, useful for algorithms that need to calculate distances between points often such as k-nearest neighbours, k-means, ICP (iterative closest point), ...
- the tree contains alternating levels that divide the search space in half based on each dimension, so in the first level all left children will have the $x$ coordinate smaller and all right children will have the $x$ coordinate bigger
- the splits alternate in each level so for 3D first level splits by $x$ second splits by $y$, third by $z$, fourth again by $x$, ...
- bulding the tree:
  - we have to order the points to decide in which order we insert them to the tree, this sorting has to happen for each coordinate so this will be $O(k\cdot n\log n)$, building the tree itself is then $O(n)$

## Presné a přibližné vyhledávání v textu založené na konečných automatech

### Basics
- **Abeceda**
	- Konecny, neprazdny set symbolu
- **Slovo (nad abecedou)**
	- Konecna (i prazdna) sekvence znaku z abecedy
- **Jazyk (nad abecedou)**
	- Set slov (stringu)
	- $|L|$ - kardinalita jazyka (pocet slov), muze byt i $\infty$
- **Automaty**
	- Konecne
		- **A** - alphabet
		- **Q** - states
		- **$\sigma$** - transitions $\sigma : Q \times A \rightarrow A$
		- $S_0$ - start state, $Q_f$ - final state(s)
		- Complexity $O(n)$ - linear with length of text
		- Zapis pomoci transition a final tabulek
		- Funguji pouze pro regularni jazyky
		- Code for simple FA


### Text metrics
- **Hamming distance**
  - defined for two strings of the same length
  - measures the number of different characters (missmatches) between the two strings
  - $O(n)$
- **Levenshtein distance**
  - also called editing distance
  - it measures the distance between strings with the number of edits needed to make them identical, where edits are insert, delete, replace
  - it is done with a dynamic programming approach, so the complexity is $O(m\cdot n)$

### Automata
- both DFA and NFA can recognize only regular langauge, a language is regular if it can be expressed with a regular expression
## DFA (deterministic finite automaton)
- always in one state only
## NFA (non-deterministic finite automaton)
- can be in multiple states at one time
- a word is accepted if at least one of the states the automaton is in is a final state
