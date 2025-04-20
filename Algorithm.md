
| **Algorithm**              | **Best**     | **Average**   | **Worst**     | **Space**     | **Description**                                                                 | **Real-Life Example**                            |
|---------------------------|--------------|---------------|----------------|---------------|----------------------------------------------------------------------------------|--------------------------------------------------|
| **Bubble Sort**           | O(n)         | O(n²)         | O(n²)          | O(1)          | Swaps adjacent elements if out of order                                          | Teaching sorting basics                         |
| **Merge Sort**            | O(n log n)   | O(n log n)    | O(n log n)     | O(n)          | Divide-and-conquer based sorting                                                | Large file or dataset sorting                   |
| **Quick Sort**            | O(n log n)   | O(n log n)    | O(n²)          | O(log n)      | Partitions using pivot and sorts recursively                                    | Backend of `qsort()` in C                       |
| **Binary Search**         | O(1)         | O(log n)      | O(log n)       | O(1)          | Searches sorted list by halving the range                                       | Looking up dictionary entries                   |
| **Linear Search**         | O(1)         | O(n)          | O(n)           | O(1)          | Sequential search                                                               | Searching a name in a random list               |
| **Dijkstra’s Algorithm**  | O(V + E log V)| O(V + E log V)| O(V²)          | O(V)          | Shortest path algorithm for weighted graphs                                     | GPS Navigation                                  |
| **BFS**                   | O(V + E)     | O(V + E)      | O(V + E)       | O(V)          | Explores neighbor nodes level by level                                          | Social network friend suggestions               |
| **DFS**                   | O(V + E)     | O(V + E)      | O(V + E)       | O(V)          | Explores deep into a branch before backtracking                                 | Solving mazes                                    |
| **Heap Sort**             | O(n log n)   | O(n log n)    | O(n log n)     | O(1)          | Turns array into heap, then sorts                                               | Priority queues                                 |
| **Prim’s Algorithm**      | O(E + log V) | O(E + log V)  | O(E log V)     | O(V)          | Builds MST by expanding from the source                                         | Designing cable or internet networks            |
| **Kruskal’s Algorithm**   | O(E log E)   | O(E log E)    | O(E log E)     | O(V)          | Builds MST by choosing shortest edges                                           | Building roads between cities                   |
| **Insertion Sort**        | O(n)         | O(n²)         | O(n²)          | O(1)          | Inserts each item into its proper place                                         | Sorting small datasets                          |
| **Selection Sort**        | O(n²)        | O(n²)         | O(n²)          | O(1)          | Selects the smallest element and places it                                      | Sorting class roll manually                     |
| **Counting Sort**         | O(n + k)     | O(n + k)      | O(n + k)       | O(k)          | Count occurrences, suitable for small range                                     | Sorting student ages                            |
| **Radix Sort**            | O(nk)        | O(nk)         | O(nk)          | O(n + k)      | Sorts by digit position                                                         | Sorting IDs or ZIP codes                        |
| **Bellman-Ford**          | O(VE)        | O(VE)         | O(VE)          | O(V)          | Handles graphs with negative weights                                            | Computer networks with varied latencies         |
| **Floyd-Warshall**        | O(V³)        | O(V³)         | O(V³)          | O(V²)         | Finds all-pairs shortest paths                                                  | Optimal travel planning                         |


Sure! Let’s bring these graph algorithms to life with **real-life relatable stories** so they’re easier to understand and remember:

---

### **1. Breadth-First Search (BFS)**  
**Real-life Story:**  
Imagine you are **looking for your friend in a city**, and you only know their **neighborhood**.  
You start from your **house**, and first check **all the houses on your street** (your immediate neighbors).  
Then you go one level deeper — checking the friends of friends, and so on.  
This is exactly what BFS does: it explores the graph **level by level**, expanding from the source.

**Example:** Facebook’s friend suggestion system ("People you may know") uses BFS to explore friends of friends.

---

### **2. Depth-First Search (DFS)**  
**Real-life Story:**  
Suppose you're in a **maze** and want to find a way out. You choose a path and keep walking **until you hit a dead end**.  
Then you **backtrack** and try the next unexplored path.  
This mimics DFS — it explores **as deep as possible**, and then **backtracks**.

**Example:** Solving puzzles like Sudoku or exploring folders in your computer.

---

### **3. Prim’s Algorithm (Minimum Spanning Tree)**  
**Real-life Story:**  
You’re a city planner. You want to **connect all the houses in a new colony with the least cable length**.  
You start from one house and always add the **nearest unconnected house** until everyone is connected.  
This is Prim’s algorithm — it **grows the tree step by step**, always picking the **cheapest edge**.

**Example:** Designing electrical grid or water pipe layout for a new neighborhood.

---

### **4. Kruskal’s Algorithm (Minimum Spanning Tree)**  
**Real-life Story:**  
You are setting up **Wi-Fi routers in a university**, and you have several routers scattered around campus.  
You want to **connect them with the least wiring**, but you're allowed to **connect any two directly**.  
So, you **sort all possible connections by cost** and keep choosing the cheapest one that doesn't form a loop.  
That's Kruskal’s — sort edges and keep adding the smallest one that keeps the network **cycle-free**.

**Example:** Building a computer network in a campus or offices with minimal cable.

---

### **5. Dijkstra’s Algorithm (Single-source shortest path)**  
**Real-life Story:**  
You're using **Google Maps** to find the **shortest route from your home to work**.  
You explore roads and **update the best-known time** to reach each place.  
Once you finalize the shortest path to a location, you **don’t revisit it**.

**Example:** GPS navigation systems for shortest route planning.

---

### **6. Bellman-Ford Algorithm (Single-source shortest path, handles negative weights)**  
**Real-life Story:**  
Imagine you're tracking **delivery cost** from one warehouse to all stores, but there are **some promotional discounts** (negative costs).  
You try each path and keep **relaxing** the cost by checking if a cheaper path exists — even through negative ones.  
But if cost keeps decreasing endlessly (negative cycle), it means there's a problem.

**Example:** Currency arbitrage detection in financial systems or road networks with toll discounts.

---

### **7. Floyd-Warshall Algorithm (All-pairs shortest path)**  
**Real-life Story:**  
Suppose you're running a **transportation company**. You want to find the **shortest travel times between every pair of cities**.  
You compare **every city with every other**, using **every other city as a middle stop**, to see if there's a shorter path.

**Example:** Flight or train scheduling system between all stations/cities.

---

### Summary Table:

| Algorithm         | Real-life Analogy                                          |
|------------------|------------------------------------------------------------|
| **BFS**           | Finding a friend by exploring friends, then friends-of-friends |
| **DFS**           | Escaping a maze by diving deep into one path              |
| **Prim's**        | Connecting houses to electricity with minimal wiring      |
| **Kruskal's**     | Connecting Wi-Fi routers with least cable, without loops  |
| **Dijkstra's**    | Finding shortest driving route on Google Maps             |
| **Bellman-Ford**  | Cheapest delivery cost with some roads offering discounts |
| **Floyd-Warshall**| Calculating shortest paths between every pair of cities   |
