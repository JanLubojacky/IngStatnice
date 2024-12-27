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

#### Jarník / Prim algorithm

#### Boruvka algorithm

#### Kruskal algorithm

## Kombinatorické a číselně teoretické algoritmy

## Izomorfismus

## Prvočíselnost a pseudonáhodná čísla

## Vyhledávací stromy a haldy, jejich využití

## Vyhledávání ve vícedimenzionálním prostoru

## Presné a přibližné vyhledávání v textu založené na konečných automatech
