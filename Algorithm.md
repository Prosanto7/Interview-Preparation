
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

---

## 🎯 Algorithm Interview Questions — Critical Scenarios

---

### ⏱️ Complexity & Trade-offs

**Q1. When would you choose Merge Sort over Quick Sort in production?**

**Answer:**

| Scenario | Choice | Reason |
|---------|--------|--------|
| Sorting linked lists | Merge Sort | Quick Sort requires random access; Merge Sort works with sequential access |
| Guaranteed O(n log n) needed | Merge Sort | Quick Sort degrades to O(n²) on already-sorted data with bad pivot |
| External sorting (disk) | Merge Sort | Sequential I/O pattern matches disk access patterns |
| In-memory with cache performance | Quick Sort | Better cache locality due to in-place partitioning |
| Stable sort required | Merge Sort | Preserves relative order of equal elements |

**Quick Sort advantage:** O(log n) extra space vs Merge Sort's O(n). In practice, Quick Sort with random pivot or median-of-three pivot selection rarely hits worst case. This is why most standard libraries (`std::sort`, PHP's `usort`) use Introsort — Quick Sort that falls back to Heap Sort when recursion depth exceeds log(n).

---

**Q2. What is the space complexity trade-off between DFS and BFS?**

**Answer:**

| Algorithm | Space Complexity | When Worse |
|-----------|-----------------|-----------|
| BFS | O(w) — width of widest level | Wide trees/graphs (e.g., 1M neighbors at level 1) |
| DFS (recursive) | O(h) — height of tree | Deep trees (stack overflow risk) |
| DFS (iterative) | O(h) — explicit stack | Same as recursive but no stack overflow |

**Interview insight:** For very wide graphs (social networks where one node has millions of connections), BFS can exhaust memory before finding the target. DFS uses much less memory but may not find the shortest path.

**Practical rule:** BFS for shortest path in unweighted graphs. DFS for cycle detection, topological sort, connectivity.

---

**Q3. How would you implement an LRU (Least Recently Used) Cache with O(1) get and put?**

**Answer:**
Combine a **HashMap** (O(1) lookup) with a **Doubly Linked List** (O(1) insert/delete):

```python
class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}  # key -> node
        # Sentinel nodes to avoid null checks
        self.head = Node(0, 0)  # Most recently used
        self.tail = Node(0, 0)  # Least recently used
        self.head.next = self.tail
        self.tail.prev = self.head

    def get(self, key):
        if key in self.cache:
            self._move_to_front(self.cache[key])
            return self.cache[key].val
        return -1

    def put(self, key, value):
        if key in self.cache:
            self.cache[key].val = value
            self._move_to_front(self.cache[key])
        else:
            node = Node(key, value)
            self.cache[key] = node
            self._add_to_front(node)
            if len(self.cache) > self.capacity:
                lru = self.tail.prev
                self._remove(lru)
                del self.cache[lru.key]
```

**Time:** O(1) for both. **Space:** O(capacity).

PHP uses `SplDoublyLinkedList` for the list part. This exact problem appears in real-world caching systems (Redis LRU approximation, browser cache).

---

### 🔢 Common Patterns

**Q4. Explain the Sliding Window technique and when to use it.**

**Answer:**
Sliding Window maintains a window of elements and slides it across the array, avoiding recomputation.

**When to use:** Subarray/substring problems involving contiguous elements with a constraint (sum, length, unique characters).

```python
# Maximum sum subarray of size k — O(n) vs brute force O(n*k)
def max_subarray_sum(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum

    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]  # Add new, remove old
        max_sum = max(max_sum, window_sum)

    return max_sum

# Variable-size window: Longest substring without repeating characters
def length_of_longest_substring(s):
    char_set = set()
    left = 0
    max_len = 0

    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)

    return max_len
```

**Pattern recognition:** If you see "contiguous subarray/substring" + "maximum/minimum" + some constraint → think Sliding Window.

---

**Q5. What is Dynamic Programming and how do you identify a DP problem?**

**Answer:**
DP solves problems by breaking them into overlapping subproblems and storing results (memoization/tabulation).

**DP indicators:**
1. "Optimal" — maximum/minimum/longest/shortest
2. Overlapping subproblems — same subproblem computed multiple times
3. Optimal substructure — optimal solution uses optimal solutions to subproblems

**Two approaches:**
```python
# Top-down (Memoization) — recursive + cache
def fib_memo(n, memo={}):
    if n in memo: return memo[n]
    if n <= 1: return n
    memo[n] = fib_memo(n-1) + fib_memo(n-2)
    return memo[n]

# Bottom-up (Tabulation) — iterative, better space
def fib_tab(n):
    if n <= 1: return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# Space-optimized bottom-up — O(1) space
def fib_opt(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a
```

**Classic DP problems:** Fibonacci, 0/1 Knapsack, Longest Common Subsequence, Coin Change, Edit Distance.

---

**Q6. What is Two Pointers technique and when does it apply?**

**Answer:**
Two Pointers uses two indices that move toward each other (or in same direction) to reduce O(n²) brute force to O(n).

```python
# Two Sum in sorted array — O(n) vs O(n²) brute force
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        current = arr[left] + arr[right]
        if current == target:
            return [left, right]
        elif current < target:
            left += 1
        else:
            right -= 1
    return []

# Same direction — Remove duplicates from sorted array
def remove_duplicates(arr):
    if not arr: return 0
    slow = 0
    for fast in range(1, len(arr)):
        if arr[fast] != arr[slow]:
            slow += 1
            arr[slow] = arr[fast]
    return slow + 1
```

**When to use:** Sorted array problems, pair/triplet sum problems, palindrome checking, merging sorted arrays.

---

### 🌐 Real-World Algorithm Applications

**Q7. How does a search engine use algorithms to rank results?**

**Answer:**

| Algorithm/Technique | Application |
|--------------------|-----------| 
| **PageRank (graph)** | Measures importance of pages via link analysis — BFS/DFS on web graph |
| **TF-IDF (scoring)** | Term Frequency × Inverse Document Frequency — weights relevance of keywords |
| **BM25 (ranking)** | Improved TF-IDF — used by Elasticsearch and Solr |
| **Inverted Index (hash)** | Maps word → document list for O(1) term lookup |
| **Binary Search** | Quickly locate terms in sorted inverted index |
| **Heap (Top-K)** | Efficiently get top-N results without full sort (O(n log k)) |
| **Trie** | Autocomplete and prefix search suggestions |

**Key insight:** Google's initial breakthrough was PageRank — treating the web as a directed graph and running a variant of random walk/eigenvector calculation to rank nodes by importance.

---

**Q8. What algorithm would you use to detect a cycle in a linked list, and why?**

**Answer:**
**Floyd's Cycle Detection (Tortoise and Hare)** — O(n) time, O(1) space:

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next        # 1 step
        fast = fast.next.next   # 2 steps
        if slow is fast:
            return True         # Cycle detected
    return False

# Find cycle start
def find_cycle_start(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            # Move one pointer to head
            slow = head
            while slow is not fast:
                slow = slow.next
                fast = fast.next
            return slow  # Meeting point = cycle start
    return None
```

**Why not HashSet?** HashSet approach uses O(n) space. Floyd's uses O(1) space — crucial for memory-constrained systems (embedded, streaming data).

**Real-world use:** Detecting circular dependencies in build systems, infinite loops in state machines.

---

**Q9. What is the difference between greedy algorithms and dynamic programming?**

**Answer:**

| Aspect | Greedy | Dynamic Programming |
|--------|--------|---------------------|
| **Decision** | Locally optimal at each step | Considers all subproblems |
| **Optimality** | Not always globally optimal | Guarantees global optimum |
| **Speed** | Generally faster | Slower (stores subproblem results) |
| **Space** | O(1) or O(log n) | O(n) to O(n²) |
| **When correct** | When greedy choice property holds | Overlapping subproblems + optimal substructure |

**Examples:**
- **Greedy works:** Activity Selection, Huffman Coding, Dijkstra's (with non-negative weights), Fractional Knapsack
- **DP required:** 0/1 Knapsack, Coin Change (minimum coins), Edit Distance, Longest Increasing Subsequence

**Interview gotcha:** Coin Change with arbitrary denominations requires DP (greedy fails for denominations like {1, 3, 4} with target 6 — greedy picks 4+1+1=3 coins, DP finds 3+3=2 coins).

---

**Q10. How does binary search extend beyond simple array search?**

**Answer:**
Binary search applies whenever the search space is monotonic (answer is "yes" before X and "no" after X, or vice versa).

```python
# Classic: Search in sorted array
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target: return mid
        elif arr[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1

# Generalized: Binary search on answer space
# "Minimum maximum" problems, capacity problems

# Example: Minimum days to make m bouquets
# Binary search on "number of days" (1 to max_bloom_day)
def can_make_bouquets(bloom, m, k, day):
    bouquets = flowers = 0
    for b in bloom:
        flowers = flowers + 1 if b <= day else 0
        if flowers == k:
            bouquets += 1
            flowers = 0
    return bouquets >= m

def min_days(bloom_day, m, k):
    lo, hi = min(bloom_day), max(bloom_day)
    if m * k > len(bloom_day): return -1
    while lo < hi:
        mid = (lo + hi) // 2
        if can_make_bouquets(bloom_day, m, k, mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

**Real-world applications:** Database query optimization (binary search on B-tree nodes), network load balancing ("find minimum server capacity that handles all requests"), resource allocation problems.
