# KO
> Problémy kombinatorické optimalizace zahrnující popis aplikací, formalizaci problému, rozbor složitosti a řešící algoritmy. Celočíselné lineární programování, toky a řezy, nejkratší cesty, úloha obchodního cestujícího, úloha batohu a rozvrhování.

# ILP

# Flows and cuts

# Shortest paths (STP)
- **Bellman's principle of optimality for STP**
  - bellman's principle is a fundemental principle in dynamic programming that states that if we reached state $s_{t+1}$ optimally than the path we took to the previous state $s_{t}$ must have been optimal as well
  - shortest path between two vertices is composed of shortest paths between the vertices on the path
  - **Bellman equation for STP**
    - optimal path to $v$ can be found by considering optimal path to $k$ + the cost from $k$ to $v$
$$l(s,v)=\min_{k\ne v}\left\{l(s,k)+c(k,v)\right\}$$

## Dijkstra's algorithm

- Complexity
  - with the naive implementation in each iteration we need to go to the vertex with the smallest distance, finding that is $O(|V|)$ and we visit all vertices which is $O(|V|^2)$ in total
  - with priority queue we are using a BFS to search the neighbors of each vertex which is $O(|V|+|E|)$ and in each iteration we insert an edge into a min heap which is $O(\log |V|)$ so we get
$$O((|E| + |V|)\times \log|V| = O(|E| \times \log|V|)$$

## Bellman-Ford

## Floyd-Warshall

# Traveling salesman

# Knapsack

# Scheduling
