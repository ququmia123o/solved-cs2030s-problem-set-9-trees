Download Link: https://assignmentchef.com/product/solved-cs2030s-problem-set-9-trees
<br>



So far, we have seen only <em>linear </em>data structures, eg. lists, maps, streams, arrays. Today, we will learn some basics of a <em>hierarchical </em>data structure called <em>trees</em>, which are useful for modeling many problems and solving them. <a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>

A tree is a non-linear structure made up of <em>nodes </em>(aka <em>vertices</em>) linked to one another via <em>edges </em>(aka <em>arcs</em>). Figure 1 shows a tree (ignore color for now) in which each node is drawn as a circle containing a value (in this case, a number, but it could be any datatype T), and each edge is drawn as a line. Edges can contain values too, but for now, we will ignore this. Trees must be <em>acyclic</em>, ie. there must not be a cycle, or loop. For instance, in the figure, if there is an edge between nodes 23 and 54, then, a cycle is created. You could walk the cycle as follows: 23 → 54 → 76 → 50 → 17 → 23.

Figure 1: A binary tree.

Node 50 at the top is typically called the <em>root</em>, whose <em>left child </em>is node 17 and <em>right child </em>is node 76. In turn, node 17 has two children 9 and 23, while node 76 has only 1 child, 54, and so on. Trees are usually drawn top down, or left to right. A <em>binary tree </em>is a tree in which every node has at most 2 children. Nodes which have no children are called <em>leaves </em>or <em>terminal nodes</em>, while the others are called <em>internal </em>or <em>non-terminal </em>nodes. In this example, nodes 12<em>,</em>19<em>,</em>67, are the leaves, while the rest are internal nodes.

Notice that the green nodes and their edges also form a tree; we call this the <em>subtree rooted at node 17</em>, or alternatively, the left subtree of node 50. Likewise, the blue nodes and their edges form a subtree rooted at the right child of node 50, and is thus called its right subtree. For any non-empty tree, whether binary or not, the following are true:

<ul>

 <li>There is only one root node.</li>

 <li>Every node, except the root, has exactly one parent node.</li>

 <li>If the tree has <em>n </em>nodes, then it has exactly <em>n </em>− 1 edges, and vice versa.</li>

 <li>The tree is <em>connected</em>, ie. you can “walk” from any node to any other node.</li>

 <li>The tree is acyclic.</li>

</ul>

We may easily implement binary trees in Java as follows. By now, we prefer lazy evaluation where possible.

public class BinaryTree&lt;T extends Comparable&lt;T&gt;&gt; {

private final T value; private final Supplier&lt;BinaryTree&lt;T&gt;&gt; leftTree; private final Supplier&lt;BinaryTree&lt;T&gt;&gt; rightTree; private final boolean amIEmpty;

//Use a Supplier to freeze the left and right subtrees public BinaryTree(T value, Supplier&lt;BinaryTree&lt;T&gt;&gt; left,

Supplier&lt;BinaryTree&lt;T&gt;&gt; right) {

this.value = value; this.leftTree = left; this.rightTree = right; this.amIEmpty = false;

}

public T value() { if (this.isEmpty()) throw new IllegalArgumentException(“value: empty tree!”);

return this.value;

}

public BinaryTree&lt;T&gt; leftTree() { if (this.isEmpty()) throw new IllegalArgumentException(“leftTree: empty tree!”);

return this.leftTree.get();

}

public BinaryTree&lt;T&gt; rightTree() { if (this.isEmpty()) throw new IllegalArgumentException(“rightTree: empty tree!”);

return this.rightTree.get();

} //..

}

Read the rest of the code in BinaryTree.java. Since, we want the ability to compare node values, we declare T extends Comparable&lt;T&gt;. Notice also that the definition is recursive: a BinaryTree is either empty, or holds a value and has right and left children of type BinaryTree (albeit frozen).

We may use a binary tree for efficient search. In Figure 1, notice that all numbers on the left subtree of the root (node 50) are smaller than the root, while all numbers on the right subtree are larger. Furthermore, this property is also true <em>at every node! </em>This property makes the tree a <em>binary search tree</em>.

Let’s see how we may exploit this property to search for 23 in the tree. Begin at the root, and compare its value with our search item (23). The root value (50) is larger, which means that our search item must lie in the left subtree (if it is there at all). We now go to the left child and continue our search there. Its value is 17, which is less than 23. This means we must next go to the right subtree of 17. We do so, and arrive at node 23, which equals our search item. We now stop the search and declare success.

Suppose instead we wish to search for 30. We follow the procedure as before, and at node 23, we conclude that we must next visit its right subtree (since 23 <em>&lt; </em>30). But node 23 has no right child. This means our search item cannot be in the tree at all. We now stop and declare failure.

You now see how this may be generalized: to search for <em>x</em>, start at the root and check to see if its value is equal to, or less than, or greater than <em>x</em>. Depending on the check result, we next do one of three things, respectively: stop and declare success, or recursively search the right subtree, or recursively search the left subtree. If there are no more children to visit, we stop and declare failure. This search procedure is easily coded as follows:

public boolean eagerContains(T thing) {

// Eager evaluation

// Return true if this tree contains item, false otherwise if (this.isEmpty()) return false; //nothing to search

int compareResult = this.value().compareTo(thing); if (compareResult == 0) return true; //found item

else if (compareResult &gt; 0)

//current node is larger than item, so search left subtree return this.leftTree().eagerContains(thing);

else //current node is smaller, so search right subtree return this.rightTree().eagerContains(thing);

}

In our search for 23 or 30 above, we compared our search item at three nodes: 50<em>,</em>17 and 23. This was sufficient to declare success or failure. We didn’t have to check every node because of the tree’s property. It may be proven that <em>on average</em>, searching for any item in an <em>n</em>-node binary search tree takes about log<sub>2</sub>(<em>n</em>) comparisons. Our tree above has 11 nodes, and log<sub>2</sub>(11) ≈ 3<em>.</em>46 which is about the number of comparisons we used, as predicted by the theory. If we plot log<sub>2</sub>(<em>n</em>) vs <em>n</em>, we will see that the curve increases very slowly. For large <em>n</em>, say, 22. This means that to search for an

NRIC number in Singapore (population ≈ 5.64 million), only 22 comparisons are needed on average. This is efficient indeed!

Q1.     Searching the tree.

<ul>

 <li>If eagerContains(61) is called using the tree in Figure 1, how many times is compareTo invoked?</li>

 <li>The analysis above was for the average case. Unfortunately, in the worst case,searching for an item may require checking every node in the tree. When will this happen? Illustrate with a tree.</li>

 <li>Complete the code below to implement a lazy version of eagerContains. This instance method in java returns a Lazy&lt;Boolean&gt; object that captures the result of checking if the search item is in this tree. The result is computed only when get is called.</li>

</ul>

//Instance method in BinaryTree.java public Lazy&lt;Boolean&gt; contains(T searchItem) { // Insert code here.

}

public class Lazy&lt;T&gt; { private final Supplier&lt;T&gt; value;

public Lazy(Supplier&lt;T&gt; v) { this.value = v;

}

public T get() { return this.value.get();

}

}

(d) Write an instance method max that lazily returns (via the Lazy object) the largest value in the binary search tree.

Figure 2: A BinaryTree&lt;String&gt;.

Q2. To eagerly get values out of the tree, we need to <em>traverse </em>it. One type of traversal is called <em>pre-order depth first traversal</em>. Starting at the root, the root value is “consumed” by an action, then the left subtree is recursively traversed, followed by the right subtree. The instance method to do so is straightforward:

public void preOrder(Consumer&lt;T&gt; action) { if (!this.isEmpty()) { action.accept(this.value()); this.leftTree().preOrder(action); this.rightTree().preOrder(action);

}

}

<ul>

 <li>Using the tree in Figure 2, determine the output of: preorder(x -&gt; System.out.println(x + “, “));</li>

 <li>Is there a binary search tree that will produce the following preorder output? If so, draw it; otherwise explain why not. H, C, A, E, F, L, K, J, N, M</li>

</ul>

Q3.     Adding to, and mapping over, a tree.

(a) Read the code for the fromList and add methods. Notice that add lazily adds each item to the tree.

public static &lt;T extends Comparable&lt;T&gt;&gt; BinaryTree&lt;T&gt; fromList(List&lt;T&gt; list) {

BinaryTree&lt;T&gt; result = BinaryTree.makeEmpty(); for (T item : list) result = result.add(item);

return result;

}

public BinaryTree&lt;T&gt; add(T thing) { if (this.isEmpty()) return new BinaryTree&lt;&gt;(thing,

()-&gt; makeEmpty(), ()-&gt; makeEmpty());

int compareResult = this.value().compareTo(thing); if (compareResult == 0) //already in tree return this;

else if (compareResult &gt; 0) //add to left tree return new BinaryTree&lt;&gt;(this.value(),

()-&gt; this.leftTree().add(thing), ()-&gt; this.rightTree());

else //add to right tree return new BinaryTree&lt;&gt;(this.value(),

()-&gt; this.leftTree(),

()-&gt; this.rightTree().add(thing));

}

Draw the resulting tree from running:

bt = BinaryTree.fromList(Arrays.asList(30, 8, 25, 6, 45, 0, 60, 80)); Draw the tree fully, as if it was constructed eagerly.

<ul>

 <li>We may lazily map over trees. The code is easy enough:</li>

</ul>

public &lt;U extends Comparable&lt;U&gt;&gt; BinaryTree&lt;U&gt; map(Function&lt;T,U&gt; f) { if (this.isEmpty()) return BinaryTree.makeEmpty();

else return new BinaryTree&lt;&gt;(f.apply(this.value),

()-&gt; this.leftTree().map(f),

()-&gt; this.rightTree().map(f));

}

Eagerly draw the resulting tree from running: bt.map(x -&gt; 100 – x);

<ul>

 <li>Does this tree still have the Binary Search Tree property? How should you recode map to correct this problem?</li>

</ul>

<a href="#_ftnref1" name="_ftn1">[1]</a> You will dive into more details in CS2040 Data Structures and Algorithms.