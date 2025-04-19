# Five Algorithmic Building Blocks, A Refresher

Scott Bruce, 2025-2-10

>*There are three levels of software engineering understanding: understanding pointers, understanding recursion, and understanding threads. Nobody truly understands threads.* [^1]

## Prelude
This short paper is a review of the basics of algorithms and a specific review of five simple, recurring structures. 

Algorithmic analysis is an incredibly deep field. Software engineers, computer scientists, and programmers learn the basics of it from the first time we write code[^2]. We practice it our entire careers, and we will never exhaust the field's possibilities. The computational model for analysis of algorithmic complexity stretches from 'a compare is a proxy for all computational cost' through 'cachelines, branch predict misses, and streaming memory speed constrain compute time' to the extreme of 'this custom silicon has unusual performance characteristics that require application specific analysis'.  Algorithmic analysis has many different specializations, but no accomplishd practitioner fails to be able to fluently apply five basic concepts:
* Trees, as exemplified by Binary Search Trees
* Hash Tables
* Amortized Analysis
* Breadth First Search On Graphs
* Depth First Search On Graphs

These five algorithmic concepts recur so frequently we cannot trust anyone who isn't familiar with them to do good algorithmic work.

## The Very Basics, As Review

As we all know, computing hardware accepts some machine instructions/operations that take operands and produce a result. At the high level language level (C and above, roughly), the programmer model is arrays of bytes, pointers, assignments, function calls, if statements, for/while loops, reads and writes. Historically, the number of compare operations executed were found to be a good proxy for 'how long will a given algorithm run, scaled by input size'. So basic algorithmic analysis tend to just count total compares made[^3]. We use "Big-O" notation in which only the largest term of a given cost equation is stated.
We won't review the most basic structures (linked lists, stack, queues, arrays); we will skip to the least complex of the complex algorithmic structures.

## Some basic operations
* get, lookup, find, search: Execute a procedure that finds some element. Ex: `get(key)->value` from a linked list takes O(n) time, and retrieves the value in the structure that matches the given key.
* put, store, stash, insert, add: Execute a procedure to insert some element into a structure.  Ex: `put(key)->value` in a linked list takes constant, O(1), time and makes the given object the head of the list.[^4]
* push, enqueue: adds an object to some structure that has controlled insertion (ex: stack, queue).
* pop, dequeue: removes an object from some structure that has controlled insertion (ex: stack, queue).
* delete: removes an object from some structure.

## Trees, as exemplified by Binary Search Trees
A binary search tree (BST) is a graph (a structure of nodes and edges) that is similar to a linked list, except each node has two edges pointing away from it.  We call the two nodes pointed to the 'children' of this 'parent' node. The binary search tree invariant is that the left child is smaller[^5] than the parent and the right child is larger than the parent.  BSTs that are balanced perform get, put and delete in O(log n) time.  Repeated operations on a balanced search tree will usually leave it unbalanced, and the worst case times (which occur if they are not guarded against) are O(n) for get, put and delete.  

The three operations implemented naively are easy: for insert, you walk down the tree, checking if the inserted value is less than or greater than the current node, until you find an empty child pointer, and add the element there. Searching is similarly simple. Delete requires finding the node, swapping its position with its largest left child or smallest right child, then unlinking and cleaning up the leaf node the value to delete ends up in.  Deleting via using a rotation can result in better balance characteristics, and many self-balancing binary search tree algorithms exist. Also splay trees exist, which are cooler and better[^6] than any AVL or red-black tree variant.

## Amortized Analysis
Amortized analysis is for determining algorithm efficiency by 'dividing out' total cost of a sequence of operations to find what each operations cost on average.  There are many variations, but the overall idea is very simple.  Given a sequence of n operations, accumulate the total cost of those operations, then divide to get the average cost.  We use this technique to decide how much to resize a dynamic array by when we want to grow it past its current bounds, so that we can say each access is O(1) _on average_.  

There are many pitfalls when thinking about actual performance costs vs computational complexity style costs when we're using 'amortized cost of O(<some function>)' algorithms.  As a specific example: A sequence of n inserts into a hash table costs O(n), but might do _many_ resizes, resulting in code that consumes several times more memory allocation, memory bandwidth, and time than it needs to.  For the general case of dynamic array sizing (which hash table resizing is a case of), 'rightsizing' your arrays when you _know_ exactly or approximately what your final collection size is results in material execution cost savings in all circumstances.  Optimizing compilers are _not_ capable of doing this optimization for you[^7][^8].

## Hash Tables

A hash table consists of an array, a hash function, and a collision resolution strategy. At least `get`,`put`,`delete` are supported as operations. Each takes amortized constant time, O(1), to complete.
The table uses a specified hashing[^9] function `f(key) => hash_value` to get a key's "hash", then transforms that hash value into an address in the array the hash table keeps (often by modulo against the table's size, but Fibonacci hashing is better).  With no collisions, insert, get and delete are simple: check the indicated address, and get|put|delete based on if the element is already present or not. 

As we add elements, we compute the table load factor as number of slots filled divided by number of slots available. If we have the potential of collisions (which we always do), we must choose a collision resolution strategy.  Chaining and probing[^10] are the usual strategies. Chaining hangs a linked list off of each bucket, and probing uses a function applied to the key, hash, or computed table address to look elsewhere in the table: often simply incrementing by one. 

Algorithmically, chaining looks better. However, in our first case of 'the costs of real hardware', chasing pointers is very expensive, so the world record fastest hash tables all use some variant of linear probing, to fetch as few cachelines as possible.  Deleting from a chained table is simple: find the bucket a key should be, and walk the linked list in that bucket, removing the key if found.  Deleting from a probed table is harder: any place you might delete from could be in the collision resolution chain of any other key, so you have to leave a tombstone indicating a dead space in the table. This can be written over on a later insert.  It will also be removed on a table resize.

A table resize happens (with the normal 'double or halve' semantics due to amortizing cost) when a we attempt to insert a key and there is no space, or when we delete a key and fall below some load factor.  First we allocate a new array, then we reinsert all live keys into that new array. Remember that amortized does not mean free! If you are inserting a large number of keys into a table, reserving a size of table that fits them all can save you a large amount of real execution time (even while leaving the algorithmic complexity identical).

## Breadth First Search (BFS)

Breadth first search walks through a graph, applying a function to some node and then applying the function to nodes on the other side of edges out of the graph.  Efficient implementations add nodes to a queue, and pre-check if a node has been added to the queue or processed before adding it to the queue.  

## Depth First Search (DFS)
Depth first search pushes nodes onto a stack, and then processes the node it is looking at.  The next node to be processed comes off the stack, and it repeats. Efficient implementations check before pushing onto the stack of the node is already discovered or processed.  Many standard graph algorithims are just depth first search with a particular function applied at each point in the exploration, or applied in some order to the processed nodes produced by the search. Topological sort of a graph, in particular, is an extremely common graph processing application of DFS.

## Beyond the Compare
cachelines, branch predict, out of order execution, superscalar arch, bitwise ops, better cost model, multiple execution threads, hyperthreads

| Ops | Cost|
|-----|-----|
|Bitwise Ops, Shifts |~0 |
|Bitwise Ops, Add, Subtract |~1 |
|Multiply, Branch Hit, Predicted Indirect |1-3 |
|Division, Modulo |~40 |
|Branch Miss, Indirect Jump |10-60 |
|L2 Cache Miss, Bad Indirect Jump |50-150 |

## Abstraction Hides Costs
Below the high level languages the machine languages lie.  The basic operations are loading data from memory to a register, storing data from a register to memory, executing arithmetic/binary operations, comparing operands, or jumping to another place in the program.  The operands to these instructions might be in a register or they might be a pointer to memory.  Virtual machines and interpreters also behave in this fashion. Reading machine code, byte code, intermediate representation (IR), Single Static Assignment (SSA) formatted code, and other very 'immediate' styles of code makes seeing execution costs easy.  Higher level languages makes seeing execution costs harder.  It is fairly easy to accidentally copy an entire array in a tight loop using javascript or python code. It is necessary to pay even more attention to execution costs in those environments, as it is far too easy to impose a catastrophic cost on your small program or large software system without meaning to.

[^1]: I first heard this joke at Google, I have no idea of its origin.  It is almost entirely true.
[^2]: Whether we want to or not: The first time you write a double for loop and try to run against something 'large' you get a Direct Lesson In Algorithms.
[^3]: A 'computational model' is the specific costs relevant to doing a particular algorithmic analysis.  They range from very simple to very complex. Naturally, the more complex ones are much more difficult to analyze.
[^4]: Obviously we could keep a tail pointer on a linked list and make the inserts on the tail and not change any time bounds.
[^5]: Usually we use a strict [weak comparator](https://en.wikipedia.org/wiki/Weak_ordering) in this sort of situation, which provides a total ordering on sets.
[^6]: Source: The Vibes, Scott Bruce, when I first read about splay trees around 2008.
[^7]: C++ attempts to circumvent this with design: `std::unordered_map` will allow you to insert any number of times without resizing, only resizing when you eventually execute a lookup.  This is probably a good tradeoff in practice, but requires extreme care! A common multithreaded pattern is 'initialize a structure, pass a time barrier, then only issue reads against that structure'. Without knowing that `std::unordered_map` reads aren't _really_ const, the pattern will fail at at least the first read (which causes a long running resize).
[^8]: The optimizing compiler cannot know the final size of any dynamic structure you are manipulating, and in fact cannot even cache the length of a dynamic array you are iterating over that you know you do not change the size of.
[^9]: Knuth's 'The Art Of Computer Programming' has extensive analysis of hash functions, universal hash functions, good entropy mixing, Fibonnaci hashing (use this), and more.
[^10]: I actually mean 'only strategies' here. Other variants of collision resolution all come down to one of these two if you tilt your head and squint.  "put it in an alternate structure" or "do another computation to find another slot in this array somewhere".  See cuckoo hashing, robin hood hashing, others.  We call also call them (open,closed)x(addressing, hashing), but that only shows up in academic papers.
