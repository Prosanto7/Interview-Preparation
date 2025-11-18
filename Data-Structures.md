# 📊 Data Structures

> Essential data structures every software engineer must know.

---

## 📚 Table of Contents

1. [Why Data Structures Matter](#-why-data-structures-matter)
2. [Arrays](#-arrays)
3. [Linked Lists](#-linked-lists)
4. [Stacks](#-stacks)
5. [Queues](#-queues)
6. [Hash Tables](#-hash-tables)
7. [Trees](#-trees)
8. [Graphs](#-graphs)
9. [Heaps](#-heaps)
10. [Tries](#-tries)
11. [Time & Space Complexity](#-time--space-complexity)

---

## 🎯 Why Data Structures Matter

| Benefit | Description |
|---------|-------------|
| **Efficiency** | Right structure = better performance |
| **Organization** | Logical data arrangement |
| **Problem Solving** | Foundation for algorithms |
| **Interviews** | Core topic in technical interviews |

---

## 📋 Arrays

### Characteristics

- **Fixed size** (in most languages)
- **Contiguous memory**
- **O(1)** access by index
- **O(n)** insertion/deletion (except at end)

### Operations

| Operation | Time Complexity |
|-----------|-----------------|
| Access | O(1) |
| Search | O(n) |
| Insert (end) | O(1) |
| Insert (middle) | O(n) |
| Delete | O(n) |

### Implementation

```php
// PHP
$array = [1, 2, 3, 4, 5];
$array[] = 6;                // Append
echo $array[0];              // Access
unset($array[2]);            // Delete
```

```javascript
// JavaScript
let arr = [1, 2, 3, 4, 5];
arr.push(6);                 // Append
console.log(arr[0]);         // Access
arr.splice(2, 1);            // Delete at index 2
```

### Use Cases

- Storing sequential data
- Implementing other data structures
- Lookup tables
- Dynamic programming

---

## 🔗 Linked Lists

### Types

1. **Singly Linked List**: Each node → next
2. **Doubly Linked List**: Each node ↔ prev & next
3. **Circular Linked List**: Last node → first node

### Structure

```
[Data|Next] → [Data|Next] → [Data|Next] → null
```

### Time Complexity

| Operation | Time |
|-----------|------|
| Access | O(n) |
| Search | O(n) |
| Insert (head) | O(1) |
| Insert (tail) | O(1) with tail pointer |
| Delete (head) | O(1) |
| Delete (middle) | O(n) |

### Implementation

```php
class Node {
    public $data;
    public $next;
    
    public function __construct($data) {
        $this->data = $data;
        $this->next = null;
    }
}

class LinkedList {
    private $head;
    
    public function __construct() {
        $this->head = null;
    }
    
    public function insert($data) {
        $newNode = new Node($data);
        
        if ($this->head === null) {
            $this->head = $newNode;
        } else {
            $current = $this->head;
            while ($current->next !== null) {
                $current = $current->next;
            }
            $current->next = $newNode;
        }
    }
    
    public function display() {
        $current = $this->head;
        while ($current !== null) {
            echo $current->data . " → ";
            $current = $current->next;
        }
        echo "null\n";
    }
}

// Usage
$list = new LinkedList();
$list->insert(1);
$list->insert(2);
$list->insert(3);
$list->display();  // 1 → 2 → 3 → null
```

### Use Cases

- Dynamic size needed
- Frequent insertions/deletions
- Implementing stacks, queues
- Undo functionality

---

## 📚 Stacks

### Concept

**LIFO** (Last In, First Out) - like a stack of plates.

```
   Push →  [3]
           [2]
           [1]  ← Pop
```

### Operations

| Operation | Time |
|-----------|------|
| Push | O(1) |
| Pop | O(1) |
| Peek/Top | O(1) |
| isEmpty | O(1) |

### Implementation

```php
class Stack {
    private $items = [];
    
    public function push($item) {
        array_push($this->items, $item);
    }
    
    public function pop() {
        if ($this->isEmpty()) {
            throw new Exception("Stack is empty");
        }
        return array_pop($this->items);
    }
    
    public function peek() {
        if ($this->isEmpty()) {
            throw new Exception("Stack is empty");
        }
        return end($this->items);
    }
    
    public function isEmpty() {
        return empty($this->items);
    }
}

// Usage
$stack = new Stack();
$stack->push(1);
$stack->push(2);
$stack->push(3);
echo $stack->pop();  // 3
echo $stack->peek(); // 2
```

### Use Cases

- Function call stack
- Undo/Redo operations
- Expression evaluation
- Backtracking algorithms
- Browser history

---

## 🎫 Queues

### Concept

**FIFO** (First In, First Out) - like a line at a store.

```
Enqueue → [1][2][3] → Dequeue
```

### Types

1. **Simple Queue**: Basic FIFO
2. **Circular Queue**: Last position connects to first
3. **Priority Queue**: Elements have priority
4. **Double-Ended Queue (Deque)**: Insert/delete from both ends

### Time Complexity

| Operation | Time |
|-----------|------|
| Enqueue | O(1) |
| Dequeue | O(1) |
| Front | O(1) |
| isEmpty | O(1) |

### Implementation

```php
class Queue {
    private $items = [];
    
    public function enqueue($item) {
        array_push($this->items, $item);
    }
    
    public function dequeue() {
        if ($this->isEmpty()) {
            throw new Exception("Queue is empty");
        }
        return array_shift($this->items);
    }
    
    public function front() {
        if ($this->isEmpty()) {
            throw new Exception("Queue is empty");
        }
        return $this->items[0];
    }
    
    public function isEmpty() {
        return empty($this->items);
    }
}

// Usage
$queue = new Queue();
$queue->enqueue(1);
$queue->enqueue(2);
$queue->enqueue(3);
echo $queue->dequeue();  // 1
echo $queue->front();    // 2
```

### Use Cases

- Task scheduling
- BFS algorithm
- Print queue
- Request handling
- Buffering

---

## 🗃️ Hash Tables

### Concept

**Key-Value pairs** with O(1) average lookup.

```
Hash Function
Key → Hash Code → Index → Value
```

### Time Complexity

| Operation | Average | Worst |
|-----------|---------|-------|
| Search | O(1) | O(n) |
| Insert | O(1) | O(n) |
| Delete | O(1) | O(n) |

### Implementation

```php
// PHP has built-in associative arrays (hash tables)
$hashTable = [];
$hashTable['name'] = 'John';
$hashTable['age'] = 25;

echo $hashTable['name'];  // John

// Check if key exists
if (isset($hashTable['age'])) {
    echo $hashTable['age'];
}

// Remove
unset($hashTable['age']);
```

```javascript
// JavaScript Map
const map = new Map();
map.set('name', 'John');
map.set('age', 25);

console.log(map.get('name'));  // John
map.delete('age');
console.log(map.has('age'));   // false
```

### Collision Resolution

1. **Chaining**: Store collisions in linked list
2. **Open Addressing**: Find next empty slot
   - Linear probing
   - Quadratic probing
   - Double hashing

### Use Cases

- Caching
- Database indexing
- Counting frequencies
- Removing duplicates
- Symbol tables

---

## 🌳 Trees

### Binary Tree

Each node has at most 2 children.

```
       1
      / \
     2   3
    / \
   4   5
```

### Binary Search Tree (BST)

**Property**: Left child < Parent < Right child

```
       8
      / \
     3   10
    / \    \
   1   6   14
      / \   /
     4   7 13
```

### Time Complexity (BST)

| Operation | Average | Worst |
|-----------|---------|-------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

### Tree Traversals

#### In-Order (Left, Root, Right)
```
4, 2, 5, 1, 3  → Sorted for BST
```

#### Pre-Order (Root, Left, Right)
```
1, 2, 4, 5, 3  → Used for copying tree
```

#### Post-Order (Left, Right, Root)
```
4, 5, 2, 3, 1  → Used for deleting tree
```

#### Level-Order (BFS)
```
1, 2, 3, 4, 5  → Level by level
```

### Implementation

```php
class TreeNode {
    public $value;
    public $left;
    public $right;
    
    public function __construct($value) {
        $this->value = $value;
        $this->left = null;
        $this->right = null;
    }
}

class BST {
    private $root;
    
    public function insert($value) {
        $this->root = $this->insertNode($this->root, $value);
    }
    
    private function insertNode($node, $value) {
        if ($node === null) {
            return new TreeNode($value);
        }
        
        if ($value < $node->value) {
            $node->left = $this->insertNode($node->left, $value);
        } else if ($value > $node->value) {
            $node->right = $this->insertNode($node->right, $value);
        }
        
        return $node;
    }
    
    public function inOrder($node) {
        if ($node !== null) {
            $this->inOrder($node->left);
            echo $node->value . " ";
            $this->inOrder($node->right);
        }
    }
}
```

### Special Trees

| Type | Description |
|------|-------------|
| **AVL Tree** | Self-balancing BST |
| **Red-Black Tree** | Self-balancing BST (less strict) |
| **B-Tree** | Used in databases |
| **Segment Tree** | Range queries |
| **Fenwick Tree** | Cumulative frequency |

### Use Cases

- File systems
- DOM structure
- Database indexes (B-Trees)
- Expression parsing
- Autocomplete (Tries)

---

## 🕸️ Graphs

### Representation

#### Adjacency Matrix
```
  A B C
A 0 1 1
B 1 0 1
C 1 1 0
```

#### Adjacency List
```
A → [B, C]
B → [A, C]
C → [A, B]
```

### Types

| Type | Description |
|------|-------------|
| **Directed** | Edges have direction |
| **Undirected** | Edges are bidirectional |
| **Weighted** | Edges have weights |
| **Cyclic** | Contains cycles |
| **Acyclic** | No cycles (DAG) |

### Graph Traversal

#### BFS (Breadth-First Search)
- Uses **queue**
- Level by level
- Shortest path in unweighted graph

#### DFS (Depth-First Search)
- Uses **stack** (or recursion)
- Explores deep first
- Used for topological sort

### Implementation

```php
class Graph {
    private $adjacencyList = [];
    
    public function addVertex($vertex) {
        if (!isset($this->adjacencyList[$vertex])) {
            $this->adjacencyList[$vertex] = [];
        }
    }
    
    public function addEdge($v1, $v2) {
        $this->adjacencyList[$v1][] = $v2;
        $this->adjacencyList[$v2][] = $v1;  // Undirected
    }
    
    public function bfs($start) {
        $visited = [];
        $queue = [$start];
        $visited[$start] = true;
        $result = [];
        
        while (!empty($queue)) {
            $vertex = array_shift($queue);
            $result[] = $vertex;
            
            foreach ($this->adjacencyList[$vertex] as $neighbor) {
                if (!isset($visited[$neighbor])) {
                    $visited[$neighbor] = true;
                    $queue[] = $neighbor;
                }
            }
        }
        
        return $result;
    }
}

// Usage
$graph = new Graph();
$graph->addVertex('A');
$graph->addVertex('B');
$graph->addVertex('C');
$graph->addEdge('A', 'B');
$graph->addEdge('A', 'C');
$graph->addEdge('B', 'C');
```

### Use Cases

- Social networks
- Maps and navigation
- Network routing
- Recommendation systems
- Dependency resolution

---

## ⛰️ Heaps

### Concept

**Complete binary tree** with heap property:
- **Max Heap**: Parent ≥ Children
- **Min Heap**: Parent ≤ Children

```
Max Heap:
      100
     /   \
    19    36
   /  \   /
  17  3  25
```

### Operations

| Operation | Time |
|-----------|------|
| Insert | O(log n) |
| Delete Max/Min | O(log n) |
| Get Max/Min | O(1) |
| Heapify | O(n) |

### Use Cases

- Priority queue
- Heap sort
- Finding k largest/smallest elements
- Median maintenance
- Graph algorithms (Dijkstra, Prim)

---

## 🔤 Tries (Prefix Trees)

### Concept

Tree for storing strings where common prefixes share nodes.

```
       root
      / | \
     c  d  t
    /   |   \
   a    o    h
  / \   |    |
 r   t  g    e
```

### Time Complexity

| Operation | Time |
|-----------|------|
| Insert | O(m) where m = length |
| Search | O(m) |
| Delete | O(m) |

### Use Cases

- Autocomplete
- Spell checker
- IP routing
- Dictionary implementation

---

## ⏱️ Time & Space Complexity

### Big O Notation

| Notation | Name | Example |
|----------|------|---------|
| O(1) | Constant | Array access |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Linear search |
| O(n log n) | Linearithmic | Merge sort |
| O(n²) | Quadratic | Bubble sort |
| O(2ⁿ) | Exponential | Fibonacci (naive) |
| O(n!) | Factorial | Permutations |

### Complexity Comparison

```
O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!)
```

### Space Complexity

- **In-place**: O(1) extra space
- **Recursion**: O(n) call stack
- **Hash table**: O(n) space

---

## 📊 Data Structure Comparison

| Data Structure | Access | Search | Insert | Delete | Use Case |
|----------------|--------|--------|--------|--------|----------|
| Array | O(1) | O(n) | O(n) | O(n) | Random access |
| Linked List | O(n) | O(n) | O(1) | O(1) | Frequent insert/delete |
| Stack | O(n) | O(n) | O(1) | O(1) | LIFO operations |
| Queue | O(n) | O(n) | O(1) | O(1) | FIFO operations |
| Hash Table | N/A | O(1) | O(1) | O(1) | Fast lookup |
| BST | O(log n) | O(log n) | O(log n) | O(log n) | Sorted data |
| Heap | O(1) | O(n) | O(log n) | O(log n) | Priority queue |
| Trie | O(m) | O(m) | O(m) | O(m) | String prefix operations |

---

## 📚 Resources

### Books
- **"Introduction to Algorithms"** (CLRS)
- **"Data Structures and Algorithms in Java"** by Robert Lafore
- **"Grokking Algorithms"** by Aditya Bhargava

### Online Platforms
- [LeetCode](https://leetcode.com/)
- [HackerRank](https://www.hackerrank.com/)
- [VisuAlgo](https://visualgo.net/) - Visualizations
- [Algorithm Visualizer](https://algorithm-visualizer.org/)

### Courses
- [Data Structures - Coursera](https://www.coursera.org/specializations/data-structures-algorithms)
- [MIT OpenCourseWare - Intro to Algorithms](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-fall-2011/)

---

**Remember:** Choose the right data structure for the problem. Understanding trade-offs is key!
