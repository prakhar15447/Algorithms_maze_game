# Dark Maze

An interactive, zero-dependency browser sandbox built for **Analysis and Design of Algorithms (ADA)**. This project visualizes procedural graph generation, heuristic shortest-path routing, raycasted line-of-sight, and real-time adversarial AI inside a partially observable dark labyrinth.

## Key Features

* **Three Procedural Maze Generators:** Switch dynamically between **Randomized DFS**, **Randomized Prim’s**, and **Randomized Kruskal’s** algorithms and benchmark their real-time generation latency in milliseconds (`performance.now()`).

* **Cyclic Graph Topology (8% Loop Injection):** After generating a spanning tree, the engine removes $8\%$ of eligible interior walls to introduce cycles, creating tactical flanking routes and preventing single-corridor softlocks.

* **Intelligent A\* Pursuit AI:** Two autonomous monsters spawn at least $12$ tiles away from the player and switch between **Patrolling** (random valid neighbor walk) and **Searching** (optimal A\* pursuit) states.

* **Bresenham Line-of-Sight & Persistent Chase:** Monsters detect the player within a $14$-tile Manhattan radius using integer raycasting and lock onto the player for a persistent $10$**-second (`26-tick`) chase timer** even after corners are turned.

* **Hard Mode & Deterministic Lightning Cycle:** Restricts player visibility to a $4.3$-tile radius flashlight, punctuated by a storm cycle:

  * **Initial Delay:** $4.0\text{ s}$ of darkness at round start.

  * **Illumination Phase:** $0.4\text{ s}$ bright lightning flash followed by $3.0\text{ s}$ of full-map visibility.

  * **Darkness Phase:** $7.0\text{ s}$ of fog-of-war darkness, repeating in a $3\text{ s} / 7\text{ s}$ cycle.

* **One-Time A\* Hint System:** Highlights the exact shortest path from the player's current cell to the exit for $10\text{ seconds}$, dynamically updating as the player moves.

## Algorithmic Architecture & Complexity

Let $V = R \times C$ represent the total grid vertices ($41 \times 29 = 1,189$ cells) and $E$ represent the walkable edges.

| Module | Algorithm | Data Structures | Time Complexity | Space Complexity | 
| ----- | ----- | ----- | ----- | ----- | 
| **Maze Generation** | Randomized DFS | 2D Grid Array, Explicit Coordinate Stack | $\Theta(V)$ | $O(V)$ | 
| **Maze Generation** | Randomized Prim’s | 2D Grid Array, Shuffled Frontier Wall List | $\Theta(V \log V)$ | $O(V)$ | 
| **Maze Generation** | Randomized Kruskal’s | Disjoint Set Union (Union-Find w/ Path Compression) | $\Theta(V \log V)$ | $O(V)$ | 
| **AI & Hint Routing** | A\* Search | `openSet` Frontier, `gScore`, `fScore`, & `cameFrom` Maps | $O(E \log V)$ avg / $O(V \log V)$ worst | $O(V)$ | 
| **Visibility Check** | Bresenham’s Line Algorithm | In-place Integer Error Accumulators | $O(\max(\Delta x, \Delta y))$ | $O(1)$ | 

### Pathfinding Cost Function

The A\* solver evaluates nodes using the total estimated cost function:

$$
f(n) = g(n) + h(n)
$$

Where $g(n)$ is the exact step count from the start node, and $h(n)$ is the **Manhattan Distance** heuristic:

$$
h(n) = \vert{}x_n - x_{\text{goal}}\vert{} + \vert{}y_n - y_{\text{goal}}\vert{}
$$

Because movement is restricted to 4 cardinal directions with uniform edge weight $w = 1$, $h(n)$ is strictly **admissible** and **consistent**, guaranteeing the mathematically shortest path.

## Tech Stack

* **Core Languages:** HTML5, CSS3, Vanilla JavaScript (ES6+)

* **Graphics & Game Loop:** HTML5 Canvas 2D Context (`CanvasRenderingContext2D`) & `requestAnimationFrame` delta-time loop

* **Benchmarking:** High-Resolution Time API (`performance.now()`)

* **Dependencies:** None (100% client-side, single-file architecture)

## Controls & How to Run

1. **Launch:** Download `ada_maze_game.html` and open it directly in any modern web browser (Chrome, Edge, Firefox, Safari). No local server or build step is required.

2. **Move:** Use **W / A / S / D** or the **Arrow Keys** to navigate from the top-left start `(1, 1)` to the green exit `(39, 27)`.

3. **Controls Panel:**

   * **Mode Toggle:** Switch between **Easy** (full visibility) and **Hard** (flashlight + lightning storm).

   * **Algorithm Dropdown:** Select `DFS`, `PRIMS`, or `KRUSKAL` to generate a fresh graph.

   * **Regenerate Maze:** Builds a new maze and resets entity positions.

   * **Hint (`HINT 💡`):** Reveals the optimal A\* escape route for $10\text{ seconds}$ (usable once per maze).
