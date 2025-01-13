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

## Kombinatorické a číselně teoretické algoritmy

## Izomorfismus

## Prvočíselnost a pseudonáhodná čísla

## Vyhledávací stromy a haldy, jejich využití

## Vyhledávání ve vícedimenzionálním prostoru

### K-d tree
- trees used for searching in point clouds, useful for algorithms that need to calculate distances between points
- bulding the tree:
  - the tree contains alternating levels that split the space based on each dimension

## Presné a přibližné vyhledávání v textu založené na konečných automatech
