# Skip List Data Structure - Explained!

Analyzing randomness is hard. So why would you want to choose a probabilistic data structure in your system implementation? Allow me to explain!

<!--more-->

## **Why am I writing about Skip Lists?**

Skip List is one of those data structures that I have seen briefly mentioned in academic books; but never seen it being used in academic or industrial projects (or in LeetCode questions 🙂 ). However I was pleasantly surprised to see Skip List being one of the major contributors behind Twitter's recent success in reducing their search indexing latency by more than 90% (absolute). This encouraged me to brush up my understanding of Skip List and also learn from Twitter's solution.

## **Why do we need Skip Lists?**

I believe it's easier to understand the concept of Skip Lists by seeing their application. So let's first build a case for Skip List.

### **The Problem: Search**

Let's say you have an array of sorted values as shown below, and your task is to find whether a given value exists in the array or not.

Given an array:

![simple array](/img/posts/skip-list/1simplearray.png)

 find whether value `57` exists in this array or not



## Option 1: Linearly Searching an Array

As the name suggests, in linear search you can simply traverse the list from one end to the other and compare each item you see with the target value you're looking for. You can stop the linear traversal if you find a matching value.

![Linear Search - Success](/img/posts/skip-list/2linsearchfound.png)



You can stop the search if at any point of time you see an item whose value is bigger than the one you're looking for.

![Linear Search - Fail](/img/posts/skip-list/3linsearchnotfound.png)


{{< admonition question "Is this a good solution?" false >}}
This solution is alright. But as you would notice, it doesn't take advantage of the fact that the given sequence of values are in sorted order. The worst case time complexity for this algorithm would be order of the number of values in the sequence, i.e. **O(n)**. We will be comparing our target value with each member of sequence if the element is at the very end of the sequence or does not exist in the sequence. 
{{< /admonition >}}

{{< admonition question "Can we do better?" false >}}
Yes, with Binary Search!
{{< /admonition >}}

## Option 2: Binary Search on an Array

The binary search concept is simple. Think of how you search for a given word in a dictionary. Say if you are trying to find the word "quirky" in the dictionary, you do not need to linearly read each word in the dictionary one-by-one to know if the word exists in the dictionary. Because you have established that the dictionary has the all of the words in alphabetical sorted order. You can simply flip through the book, notice which word you are at, then either go left or right depending on whether the word starts with a character that comes before or after 'q'.

Going back to our example, let's first check the value at the center of the sequence, if the value matches our target. we can successfully end the search.

![Binary Search - Success](/img/posts/skip-list/25binsearchoneshot.png)



But if it is not the value we are looking for, we will compare the value of the middle position with our target value. If our target value is smaller, we need to look into smaller numbers, so we go towards left and ignore the values on the right.



![Binary Search - 1](/img/posts/skip-list/3binsearchstart.png)



![Binary Search - 2](/img/posts/skip-list/4binsearch.png)

> By following this binary search approach have reduced our search space and the required number of comparisons by half. Also, we repeat the same process on the "valid" half, i.e. we directly go the middle value of the left sub-array and see if we have a match, if not, we again decide whether to go left or right, and hence reduce our candidate search space by half again.

![Binary Search - 3](/img/posts/skip-list/5binsearch.png)



![Binary Search - 4](/img/posts/skip-list/6binsearch.png)



![Binary Search - 5](/img/posts/skip-list/7binsearchfound.png)


{{< admonition question "What is the time complexity now?" false >}}
The time complexity of this algorithm is again defined by the number of comparisons. If you recognize the pattern in this algorithm, you would know that the algorithm belongs to the category for which the size of a variable is continuously divided by a constant (2 in this case). And hence our algorithmic time complexity is **O(\\(log_2n\\))**. [[Read this for an intuitive explanation](https://stackoverflow.com/questions/9152890/what-would-cause-an-algorithm-to-have-olog-n-complexity/9153420#9153420)]
{{< /admonition >}}


This is excellent. Logarithmic time complexity means that our algorithm will scale really well with large number of values being stored in the sequential array.


{{< admonition warning "Is this a perfect solution?" >}}
One big issue, here, is that while sequential storage (for example, arrays) makes in easy and cheap to design binary search algorithms, it makes it costlier for us to implement an insertion and deletion strategy. For example, if you insert an item at a specific index in the array, you will have to shift the values on its right by one place to the right. Similarly if you delete a value at an arbitrary index inside the array, you will have to left shift all the values to the right of that index by one place. (see examples below)
{{< /admonition >}}

![array insertion](/img/posts/skip-list/8arrayinsertion.png)

![array deletion](/img/posts/skip-list/9arraydeletion.png)

As you can imagine, having extra space on the right of the array would be convenient else you would have to call for dynamic allocation of memory at every insertion and copy over the whole array to newly allocated array memory. List data structure in Python, allocates list in a similar way that leaves capacity at the end of the sequence for future expansion. If we run out of space in the list, Python allocates a new and much larger (double the size, for example) sized list, copies over each value from the old list to new list, and then deletes the old list. (This is why the insertion in a Python list is [Amortized](https://en.wikipedia.org/wiki/Amortized_analysis) **O(1)** complexity)

{{< admonition question "Could there be a way to have a data structure with better insertion and deletion costs?" false >}}
Yes, we can use Linked Lists!
{{< /admonition >}}

## Option 3: Linear search with Linked Lists

Linked Lists are dynamic data structures that store values in non-contiguous memory blocks (called nodes). Each block refers to the other using memory pointers. These pointers are sort of the links in a chain that define the ordering among the non-contiguous nodes. A Singly Linked list represents a sequential structure where each node maintains a pointer to the element that follows it in the sequence. In linked lists we also maintain additional reference to the list nodes. Head is one such reference which is variable that stores the address for the first element in the list (stores null pointer if the list is empty).

![Linked List - simple](/img/posts/skip-list/10singlyll.png)

Insertion and deletion at any arbitrary location is straightforward, as you would simply reconnect the links of the nodes previous and next positions from the target position.

![Linked List - Insertion](/img/posts/skip-list/12singlyllinsertion.png)

![Linked List - Deletion](/img/posts/skip-list/13singlylldeletion.png)



We can also have a Doubly Linked List where each node stores the pointer to the previous as well as the next node in the sequence.  A tail reference may also be used to store pointer to the last node in the linked list.



![Doubly Linked List - simple](/img/posts/skip-list/11doublyll.png)

As you would notice, random access in linked lists are not possible. And to find a value in the list, we would simply have to traverse the list from one end, say from the node referred by Head, towards the other end till we find what we are looking for. So, even though you can insert and delete in **O(1),** you still need the reference to the target location. Finding the target location is again **O(n)**.

> With Linked Lists, while we solved the issue of costly insertions and deletions, we haven't quite found a good solution to quickly access a target value in the list (or find if the value exists).

Wouldn't it be great if we could have the random access (or something close to it) in the list to simply our find, insert and delete operations? Well this is where Skip List come handy!

## Option 4: Pseudo-binary search with Linked Lists [Skip List]

Technical advances have made low latency highly desirable for software applications. How well the underlying algorithm scales to demand can make or break a product. On the other hand, the memory/storage costs is getting cheaper by day. This propels many system designers to make decisions that balance compromises that spend extra money on storage costs, while opting for algorithms with low time complexities. Skip Lists is a good example of one such compromise.

Skip Lists were proposed by WIlliam Pugh in 1989 ([read the original paper :(fa fa-book): here](https://15721.courses.cs.cmu.edu/spring2018/papers/08-oltpindexes1/pugh-skiplists-cacm1990.pdf)) as a probabilistic alternative to balanced trees. Skip lists are relatively easy to implement, specially when compared to balanced trees like AVL Trees or Red Black Trees. If you have used the Balanced Trees before, you may remember how they are great data structures to maintain relative order of elements in a sequence, while making search operations easy. However, insertions and deletions in Balanced Trees are quite complex as we have the additional task to strictly ensure the tree is balanced at all times.

> Skip Lists introduce randomness in by making balancing of the data structure to be probabilistic. While it definitely makes evaluating complexity of the algorithm to be much harder, the insertion, deletion operations become much faster and simpler to implement. The complexities are calculated based on expectation, but they have a high probability, such that we can say with high confidence (but not absolute guarantee) that the run time complexity would be O(log(n)) apart from some specific worst-case scenarios as we would see soon.

### Skip Lists - Intuition

Following is the map of New York Subway's "7" train.

![New York Metro Map 7](/img/posts/skip-list/nyc-metro-route-7-flushing-local-map.png)

Each point on the horizontal line represents on train stop on a specific street. The line at the top is the local train that has much more number of stops compared to the express line on the below it. Government decides where to put the stops based on factors like nearby facilities, neighborhood, business centers etc. Let's simplify the above route map and display stations by the street numbers.

![New York Map simplified](/img/posts/skip-list/14nycmap.png)

In the simplified map above, the express line is shown at the top and local line is below it. Now if you had this map and you were supposed to travel from stop `14` to stop `59`. How would you plan your trip route? For simplicity let's assume we can't overshoot, meaning that we can't go some station to the right and then travel back towards left. You would probably want to take the express line from stop `14` and get down on stop `42`. Then take the local train from stop 42 and reach the destination 59 from there, as shown below.

![New York Map traverse](/img/posts/skip-list/15nycpath.png)



As you would notice, by taking the express route you would be able to skip some stations along the way and maybe even reach the destination much faster compared to taking the local train from a original stop and traveling each station one by one to reach a destination stop. This is exactly the core idea behind Skip List. With Skip Lists, we introduce redundant travel paths at the cost of additional space, but the additional travel paths have lesser and lesser number of "stops" such that the linear traversal is much faster if we travel in those additional lanes.

### Skip Lists - Basics

Let's now build the concept of Skips Lists from bottom to top.

Assume that we have a Doubly Linked List which is unsorted. What do you think would be the search complexity for this list?

![Doubly Linked List - Search](/img/posts/skip-list/16doublyllunsearch.png)

Search would be done in **O(n)**, because you would have to traverse the list linearly to find your target node.

Let's assume that the list is now sorted as shown below. What do you think is the search complexity now?

![Doubly Linked List - Sorted Search](/img/posts/skip-list/17doublyllsearch.png)

Well, it's still **O(n)**. Even if it sorted, the linked list structure doesn't provide us random access, hence we can't simply calculate the next middle index and access that middle node in **O(1)**.

Now, let's put our "express line" on top of the existing structure. We are going to create a second Doubly Linked List and place the second list above of the first list such that each node has four reference to neighboring nodes: left, right, above, below.

which can be visualized as:



![Doubly Linked Lists - Joined](/img/posts/skip-list/175doublyllsearch.png)



Now given this Skip Lists structure, you can find the number 73 with lesser number of comparison operations. You can start traversing from the top list first, and at every node see if the next node overshoots the value that you are looking for. If it doesn't simply go the next node in the list on the top, else go to the list on the bottom and continue your linear search in the bottom list.

![Doubly Linked List - Search](/img/posts/skip-list/18doublyllsearch.png)



We can summarize the searching approach in a simple algorithm.

{{< admonition abstract "Algorithm" >}}
- Walk right in the Linked List on top (L1) until going right is going too far
- Walk down to the bottom list (L0)
- Walk right in L0 until element is found or we overshoot.
{{< /admonition >}}



So what is the kind of search cost we are looking at?

We would want to travel the length of L1 as much as possible because that gives us skips over unnecessary comparisons. And eventually we may go down to L0 and traverse a portion of it. The idea is to uniformly disperse the nodes in the top lane.
{{< style "text-align:center; strong{color:#00b1ff;}" >}}
Search Cost = \\(|L1| + \frac{|L0|}{|L1|}\\)
{{< /style >}}
**How many "stops" (nodes) do you need in the top lane?**

It can be <cite>mathematically shown[^1]</cite> that if the cardinality of the bottom list is n (i.e. it has n nodes), we will get an optimal solution if we have \\(\sqrt{n}\\) nodes in the top layer.
{{< style "text-align:center; strong{color:#00b1ff;}" >}}
So, Search Cost = \\(\sqrt{n} + \frac{n}{\sqrt{n}}\\) = 2\\(\sqrt{n}\\) = O(\\(\sqrt{n})\\))
{{< /style >}}
However, our Skip List don't have to limited to 2 lists. We can have more lists ("lanes"), meaning our search costs would scale as:
{{< style "text-align:center; strong{color:#00b1ff;}" >}}
‎‏‏‎2 sorted lists → \\(2 * \sqrt[2]{n}\\)

3 sorted lists → \\(3 * \sqrt[3]{n}\\)

‎‏‏‎...

k sorted lists → \\(k * \sqrt[k]{n}\\)

(if we have log(n) number of sorted lists:)‎‎‎‎‎‎‎‎‎‎‎‏‏‎

‎‎‎‎‎‎‎‎‎‎‎log(n) sorted lists → \\(log(n) * \sqrt[log(n)]{n}\\) = 2 \\(\log{n}\\)
‎‎‎‎‎‏‎‎‏‏{{< /style >}}

So, if the nodes are optimally dispersed, theoretically the structure gives us our favorite logarithmic complexity 🙂  (Although in implementation when we use randomization to insert the nodes, things get a little tricky).

Now think of the structure we have created. At the bottom we have n nodes, the layer on top has interspersed \\(\frac{n}{2}\\) nodes and the layer above it has \\(\frac{n}{4}\\) nodes. Do you how this structure looks quite similar to a tree? We just need to figure out how "balancing" works in this tree (*hint: it's probabilistic*).

### Skip List - Definition

Skip List S consists of a series of lists {\\(S_0,S_1 .. S_h\\)} such that each list \\(S_i\\) stores a subset of the items sorted by increasing keys.

{{< admonition example "Also note:" >}}
- h represents the height of the Skip List S
- each list also has two sentinel nodes -\\(\infty\\)  and +\\(\infty\\) . -\\(\infty\\) is smaller than any possible key in the list and +\\(\infty\\) is greater than any possible key in the list. Hence the node with -\\(\infty\\) key is always on the leftmost position and node with +\\(\infty\\) key is always the rightmost node in the list
- For visualization, it is customary to have the \\(S_0\\) list on the bottom and lists \\(S_1, S_2, ..  S_h\\) above it
- Each node in a list has to be "sitting" on another node with same key below it. Meaning that if \\(L_{i+1}\\) has a node with key k, then \\(L_i, L_{i-1} ..\\) all valid lists below it will have the same key in them
- We may chose to opt for an implementation of Skip List that only uses Single Linked Lists. However it may only improve the asymptotic complexity by a constant factor
- Skip List also needs to maintain the "head" pointer, i.e. the reference to the first member (sentinel node -\\(\infty\\)) in the topmost list
- On average the Skip List will have O(n) space complexity
{{< /admonition >}}

Let's first simplify the visualization for our nodes and connection in Skip List.

![Skip List - Simplify](/img/posts/skip-list/19simplify.png)



A standard, simplified Skip List would look like this:

![Skip List - example](/img/posts/skip-list/20simpleskiplist.png)



Now, let's see how some of the basic operations are performed on a Skip List.

### Searching in Skip List

Searching in a Skip List in straightforward. We have already seen an example above with two lists.  We can extend the same approach to a Skip List with `h` lists. We will simply start with the leftmost node in the list on top. Go down if the next value in the same list is greater than the one we are looking for, go right otherwise.

![Skip List - Search](/img/posts/skip-list/21skiplistsearch.png)

The main idea is again to skip comparisons with as many keys as possible, while compromising a little on the extra storage required in the additional lists containing subset of all keys. On average,  "expected" Search complexity for Skip List is **O(log(n))**



### Inserting in Skip List

To insert a new node, we would first find the location where this new key should be inserted in the list at the very bottom. This can be simply done be re-using the search logic, we start traversing to the right from the list on top and we go one step down if the next item is bigger than the key that we want to insert, else go right. Once we reach at a position in bottom list where we can't go more to the right, we insert the new value on the right side of that position.

![Skip List - Insert 1](/img/posts/skip-list/22skiplistinsert1.png)

Skip List after inserting 67:

![Skip List - Insert 2](/img/posts/skip-list/23skiplistinsert2.png)



> Now comes the probabilistic part, after inserting the new node in the list \\(L_i\\) we have to also insert the node in list above it, i.e. list \\(L_{i+1}\\) with probability \\(\frac{1}{2}\\). We do this with a simple coin toss, such that if we get a head we insert the node in the list \\(L_{i+1}\\) and toss the coin again, we stop when we get a tail. We may repeat the coin toss till we keep getting heads, even if we have to insert a new layer (list) at the top.

![Skip List - Insert 3](/img/posts/skip-list/24skiplistinsert3.png)



Note that we also need to "re-hook" the links for the newly inserted node. The new node needs to have it's 'below' reference pointing to the node below, and the node below would have 'above' reference pointer to the new node. To find the 'left' neighbor for the new node, we simply traverse towards left from the node below it and return 'above' pointer from a node for which 'above' pointer is not null. The 'right' reference of the 'left' neighbor is used to update the 'right' reference of the new node. Also, the 'left' reference for the new node's right neighbor is also updated to the new node. This re-hooking operation is actually pretty easy to implement with Linked Lists.

![Skip List - Insert 4](/img/posts/skip-list/25skiplistinsert4.png)



Let's toss the coin again.

![Skip List - Insert 5](/img/posts/skip-list/26skiplistinsert5.png)



Last toss gave us another Head, so let's toss the coin again.

![Skip List - Insert 6](/img/posts/skip-list/27skiplistinsert6.png)



We got a Tail this time, so no more insertions are required.

As you see the probability of a node also getting inserted in the layer above it get reduced by half after every layer. Still, there is a worst case possibility that you would keep getting Heads indefinitely, although the probability of that happening is extremely small. To avoid such cases when you may get a large number of heads sequentially, you could also use a termination condition where you stop inserting if you reach a predefined threshold for the number of layers (height) or a predefined threshold for the number of nodes in a specific layer. Although the expected time complexity would still be **O(log(n))** for all operations.



### Deletion in Skip List

Deletion operation in Skip List is pretty straightforward. We first perform search operation to find the location of the node. If we find the node, we simply delete it at all levels. Before deleting a node, we simply ensure that no other node is pointing to it.

![Skip List - Delete Search](/img/posts/skip-list/28skiplistdelete1.png)



Release all references to this node. And move to the node below it.

![Skip List - Delete 1](/img/posts/skip-list/29skiplistdelete2.png)



Remove all references to this node and release it. Move to the node below in the bottom list.



![Skip List - Delete 2](/img/posts/skip-list/30skiplistdelete3.png)



Release this node as well.

![Skip List - Delete 3](/img/posts/skip-list/31skiplistdelete4.png)

This is how our Skip List looks like after deletion.

![Skip List - Delete 4](/img/posts/skip-list/32skiplistdelete5.png)



## Conclusion

In this article we saw how Skip List is a probabilistic data structure that loosely resembles a balanced binary tree. It is also easy to implement and very fast as the search, insert, delete operations have an expected time complexity of **O(log(n))**, along with **O(n)** expected space complexity. in next article, I will introduce a practical application of Skip List and explain how Twitter used Skip List to get a huge improvement in their search indexing latency.

## Reference and Further Reading

[^1]: MIT OCW 6.046 Design and Analysis of Algorithms  [Lecture 7: Randomization: Skip Lists](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-046j-design-and-analysis-of-algorithms-spring-2015/lecture-videos/lecture-7-randomization-skip-lists/)




