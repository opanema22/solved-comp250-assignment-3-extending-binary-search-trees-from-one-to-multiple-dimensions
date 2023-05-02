Download Link: https://assignmentchef.com/product/solved-comp250-assignment-3-extending-binary-search-trees-from-one-to-multiple-dimensions
<br>
In this assignment, we extend the powerful idea of binary search trees from one dimension to multiple dimensions. In particular, we will work with points in &lt;<em><sup>k </sup></em>where <em>k </em>can be any positive integer. Such points might represent image data, audio data, or text. For example, machine learning methods often map such common data to <em>feature vectors </em>in a <em>k </em>− <em>D </em><em>feature space</em>, and then use these features in various ways.<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>

In binary search, one is given a key and tries to find that key amongst many that are stored in a data structure. We will examine a related but different problem for <em>k </em>− <em>D </em>data. Our problem will be to take a given point, which we will call a <em>query point </em>rather than a “key”, and search for the <em>nearest point </em>in the data set. In the field of Machine Learning, the nearest point is typically referred to as the <em>nearest neighbor</em>.

One way to find the nearest point would be to iterate by brute force over all data points, compute the distance from the query point to each data point, and then return the data point that has the minimum distance.<a href="#_ftn2" name="_ftnref2"><sup>[2]</sup></a> However, this brute force search is inefficient, for the same reason that linear search through a list is inefficient relative to binary search, since in the worst case it requires examining each data point. One would instead like to have a “nearest point” method that is must faster, by restricting the search to a small subset of the data points.

<h2>1.1          KD-Trees</h2>

One way to restrict the search for a query point is to use a tree data structure called a <em>kd-tree</em>. This is a binary tree which is similar to a binary search tree, but there are important differences. One difference between a kd-tree and a binary search tree concerns the data itself. In a binary search tree, the data points (keys) are ordered as if on a line, or in Java terms they are Comparable. In a kd-tree, there is no such overall ordering. Instead, we only have an ordering within each of the <em>k </em>dimensions, and this ordering can differ between dimensions. For example, consider <em>k </em>= 2, and let points be denoted (<em>x</em><sub>1</sub><em>,x</em><sub>2</sub>). We can order two points by their <em>x</em><sub>1 </sub>value and we can order two points by their <em>x</em><sub>2 </sub>value, but we don’t want to order (<em>x</em><sub>1</sub><em>,x</em><sub>2</sub>) and  in general. For example, in the case but, we do not want to impose an ordering of these two points.

Another important difference is how binary search trees and kd-trees are typically <em>used</em>. With a binary search tree, one typically searches for an <em>exact </em>match for the search key. With a kd-tree, one typically searches for the <em>nearest </em>data point. We will use the Euclidean distance. Give a query point <em>x<sub>q </sub></em>∈Z<em><sup>k </sup></em>we want to find a data point <em>x</em>[ ] that minimizes the squared distance,

<em>.</em>

We used squared distance rather than distance because taking the square root buys us no extra information about which point is closest.

Note that there may be multiple data points that are the same (squared) distance from a query point <em>x<sub>q</sub></em>. One could define the problem slightly differently by returning all points in this non-unique case. Or, one could ask for the nearest <em>n </em>points, and indeed this is common. We will just keep it simple and ask for a single nearest point. If multiple points lie at that same nearest distance, then any of these points may be returned.

Kd-trees which are a particular type of binary tree. Each <em>internal node </em>implicitly defines a <em>k</em>−dimensional region in &lt;<em><sup>k</sup></em>, which we refer to as a <em>hyperrectangle</em>, and which will be defined below. An internal node has two (non-null) children which represent two spatially disjoint (nonintersecting) hyperrectangles that are contained within the parent hyperrectangle. We will refer to the two children as the low child and high child. <a href="#_ftn3" name="_ftnref3"><sup>[3]</sup></a>

Data points are stored at the leaf nodes, namely each leaf node has one Datum object. Internal nodes do not store references to Datum objects. Rather, for each internal node, we associate a set of data points, namely the Datum objects stored in the descendant leaf nodes.

Each internal node’s hyperrectangle is defined by data points that are associated with each node, as follows. For each of the <em>k </em>dimensions, the <em>range </em>of the hyperrectangle in that dimension is the difference of the maximum and minimum values of the data points. An example will be given later to illustrate. The range of a node in each dimension will be used when constructing the tree, as discussed below.

<h3>1.1.1        Example</h3>

Let’s consider a kd-tree node that is associated with ten 2D points (<em>k </em>= 2). The points are shown below. The horizontal axis is dimension <em>x</em><sub>0 </sub>and the vertical axis is dimension is <em>x</em><sub>1</sub>, which correspond to array representation <em>x</em>[0] and <em>x</em>[1]. The red rectangle indicates the minimum and maximum values of the data points in the two dimensions. We will discuss the significance of these values below.

Figure 1: 10 data points in a 2D space. The red rectangle is the hyperrectangle defined by the low and high values in the two dimensions.

<h3>1.1.2        Constructing a KD-tree</h3>

We will not give a formal definition of kd-tree. Instead we give an algorithm for constructing one, given list of data points is &lt;<em><sup>k</sup></em>. The algorithm starts by constructing a root node and proceeds recursively. When constructing a node, we pass in an array of the data points associated with that node. If the array contains just one data point, then the node is a leaf node. Otherwise, the node is an internal node (with one exception which we return to below). The set of data points associated with an internal node is then partitioned into two non-empty sets, which are used to define the two children of that node. If the internal node has two data points associated with it, then the two children nodes will be leaf nodes. If an internal node has three data points associated with it, then one child node will be internal with two points associated with it and the other child node will be a leaf. There is one special case to consider, namely that all the data points at a node are equal (duplicates). In this case, the node is considered a leaf node, and the duplicate data points are removed.

Because the kd-tree will be used to find nearest point to targets, we would like the data points at each node to be a compact as possible in &lt;<em><sup>k</sup></em>. Put another way, we would like the points associated with each child to be as close to each other as possible, and the points associated with the two children to be as far apart from each other as possible. This will allow us to sometimes only search the data points associated with one of the children, which speeds up the search. To try to achieve this compactness property when partitioning the set of data points at a node, we compute which of the <em>k </em>dimensions has the largest range of values, where range is defined by the maximum minus the minimum value within a given dimension. We then partition or <em>split </em>the data points into two sets based only on their coordinate values in this dimension. For this assignment, <em>we require that you follow this rule</em>, namely you must choose the <em>splitting dimension </em><em>d </em>∈{1<em>,…,k</em>} according to this maximum range criterion. This guarantees, in particular, that the range of values in this splitting dimension is greater than zero. (If there are two dimensions <em>d </em>and <em>d</em><sup>0 </sup>that both have the greatest range of values, then either may be chosen.)

Once the <em>splitting </em>dimension <em>d </em>of a node is chosen, the algorithm chooses a <em>splitting value</em>. Data points associated with that node whose value <em>x</em>[<em>d</em>] in coordinate <em>d </em>is less than or equal to the splitting value go in the low child’s subtree, and the remaining data points go into the high child’s subtree.

How to choose the splitting value? We would like the tree to be as balanced as possible, and so we try to choose a splitting value that partitions the data points at a node such that there are a roughly equal number of data points associated with the two children. (This is similar to the role of the pivot in quicksort.) It may not possible to choose a rule that is fast and always achieves this goal, however. For example, choosing splitting value to be the median value works well if the data point values in dimension <em>d </em>are distinct, but if there are repeats then the median method might not work well. (Also, choosing the median quickly is not obvious.) We will leave it up to you how to choose this splitting value. We suggest a simple choice for the splitting value to be the average of the min and max values in the splitting dimension. This choice at least guarantees that we partition the data points at a node into two sets (assuming the points are different).

<h3>1.1.3        Example (continued)</h3>

In our example, the data has a larger range in the <em>x</em><sub>0 </sub>dimension than in the <em>x</em><sub>1 </sub>dimension and so we split in the <em>x</em><sub>0 </sub>dimension.

Figure 2: For this figure and subsequent figures, the <em>splitting plane </em>is defined by the median of the <em>x</em><sub>0 </sub>values of the 10 data points. By convention, points on the splitting plane belong to the low half. Although the median split is very common, for your implementation, we strongly suggest that you do <em>not </em>attempt to split using the median, but instead split using some other value such as the average of the low and high values in the splitting dimension. This will be easier to do.

The figure below shows the result of splitting recursively as described above. On the left, the original bounding rectangle is partitioned into a nested set of rectangles. This is done by choosing a set of splitting planes (dashed black lines) for each rectangle. The splitting planes are labelled with letters a to h. On the right is shown the KD-tree structure that is defined by the splitting planes and the corresponding nesting of rectangles. For example, the large bounding rectangle is partitioned into two rectangles by the splitting plane a, which corresponds to the root node of the tree.

Consider an internal node d and its subtree, and the corresponding splitting plane labelled d in the figure on the left. This subtree defined by d is the low (left) child of node b and thus the points associated with this subtree lie on the low side of splitting plane b, namely these are the points {<em>p</em><sub>4</sub><em>,p</em><sub>3</sub><em>,p</em><sub>5</sub>}. (Recall that points that lie on a splitting plane are included on the low side, not the high side.) The splitting plane d splits this set into two sets {<em>p</em><sub>4</sub><em>,p</em><sub>3</sub>} and {<em>p</em><sub>5</sub>}. The set {<em>p</em><sub>4</sub><em>,p</em><sub>3</sub>} is in turn split by plane e, and corresponding node e’s children are two leaves containing these two points. The point {<em>p</em><sub>5</sub>} becomes a leaf which is the high child of node d. We encourage to examine other internal nodes and ask which regions they correspond to and what are the data points within these regions.

Figure 3: The outer red rectangle is the region defined by the low and high bounds of the 10 data points. The inner red rectangle is the region bounded by the data points below splitting node d , namely points <em>p</em><sub>3</sub><em>,p</em><sub>4</sub><em>,p</em><sub>5</sub>. Notice that this region does not span the entire region to the left of the b splitting plane.

<h2>1.2          Finding the nearest point</h2>

Given a kd-tree, one would like to to find the nearest data point to a given (input) query point. The query point need not belong to the set of data points in the tree.

The search algorithm for the nearest point is recursive, starting at the root node. For any node during the search, the query point’s coordinate value in the splitting dimension <em>x<sub>q</sub></em>[<em>d</em>] is compared to the node’s splitting value. You might think that you could restrict your search for the closest data point to the query point <em>x<sub>q </sub></em>by examining only the data points on the same side of the splitting plane as <em>x<sub>q</sub></em>. This approach would indeed be sufficient if we were trying to find an exact match of the query in the tree. However, the query point may not have an exact match, and in that case the nearest point to the query point might not always lie on the same side of the splitting plane of a node, and so one might have to search through the points on both sides of the splitting plane.

If one were <em>always </em>to search through all points associated with a node, then the tree structure would serve no purpose, and one would be better off just brute force searching the list of data points.

The clever insight that makes kd-trees useful is that we can sometimes restrict our search only to those data points on the same side of the splitting plane as the query point. The crucial condition for restricting the search is as follows: <em>if the distance from the query point to the closest point on the same side of the splitting plane is less than the distance from the query point to the splitting plane itself, then points on the opposite side of the splitting plane cannot be the nearest point and hence do not need to be considered. </em>If, however, the distance from the query point to the nearest point on the same side is greater than the distance from the query point to the splitting plane, then one does need to consider the possibility that a data point on the other side of the splitting plane might be the closest point.

Note that if there are two data points that are the same distance from the target, then we might ask how to break the tie to get a unique answer. It is possible to come with rules for doing so, but we will not concern ourselves with this. Instead, in the case that are multiple closest points to some given query point, if you return one of the points, then this is considered to be correct. This avoids us having to specify a tie breaking policy.

<h3>1.2.1        Example (continued)</h3>

The figure below shows the first splitting plane, which is defined at the root node of the tree. It also shows a query point <em>x<sub>q </sub></em>in blue. Suppose we first search the data points on the same side of the splitting plane as the query point. The nearest data point on the same side of the splitting plane is found to be <em>p</em><sub>1</sub>. A circle is drawn that is centered on the query point and passes through <em>p</em><sub>1</sub>. Notice that the distance from the query point to <em>p</em><sub>1 </sub>is less than the distance from the query point to the splitting plane. That is, the ball shown around the query point contains the nearest point but doesn’t intersect the splitting plane. In this case, we can be sure that the nearest of all 10 data points is <em>not </em>on the other side of the splitting plane, and so we don’t need to search the data points other side.

Figure 4: A query point <em>x<sub>q </sub></em>(blue) and the nearest data point <em>p</em><sub>1 </sub>on the same side as the splitting plane.

Let’s now take an example with a different query point . Now the ball around the closest point <em>p</em><sub>1 </sub>to the query point that is on the same side of the splitting plane which has radius intersects the splitting plane. In this case, we cannot be sure that <em>p</em><sub>1 </sub>is indeed the closest point in the data set, as there may be a point on the other side of the splitting plane that falls strictly within the ball and hence is closer. We therefore also have to search the points on the other side of the splitting plane too. (Indeed in this example, <em>p</em><sub>2 </sub>is such a point.)

The description above should be sufficient for you to write a method for building a kd-tree for a given set of data points, and for finding the closest data point to a given target.

Figure 5: A query point (blue) and the nearest data point <em>p</em><sub>1 </sub>on the same side as the splitting plane, and the nearest point <em>p</em><sub>2 </sub>on the opposite side of the splitting plane.

<h2>1.3          More info about kd-trees</h2>

Kd-trees were invented in the 1970’s. <em>If you are interested</em>, have look at some of the original research papers that presented the method. (Click to access the paper.) As you can see from just these two papers, there are many possible ways to define kd-trees.

<ul>

 <li><a href="http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.160.335&amp;rep=rep1&amp;type=pdf">Bentley, J. L. (1975). ”Multidimensional binary search trees used for associative searching”. </a><a href="http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.160.335&amp;rep=rep1&amp;type=pdf">Communications of the ACM.</a></li>

 <li>Friedman, J. H.; Bentley, J. L.; Finkel, R. A. (1977). ”An Algorithm for Finding Bes<a href="https://dl.acm.org/ft_gateway.cfm?id=355745&amp;ftid=66775&amp;dwn=1&amp;CFID=82352038&amp;CFTOKEN=57db6acbaf6a4b02-FD5C4388-C1BE-92D1-B8EA35F3BB3C8AA8">t </a><a href="https://dl.acm.org/ft_gateway.cfm?id=355745&amp;ftid=66775&amp;dwn=1&amp;CFID=82352038&amp;CFTOKEN=57db6acbaf6a4b02-FD5C4388-C1BE-92D1-B8EA35F3BB3C8AA8">Matches in Logarithmic Expected Time”. ACM Transactions on Mathematical Software.</a></li>

</ul>

There are ample resources about kd-trees on the web. For example, the KD-tree <a href="https://en.wikipedia.org/wiki/K-d_tree">Wikipedia </a><a href="https://en.wikipedia.org/wiki/K-d_tree">page</a> . While we encourage you to learn more if you are interested, be aware that these resources will contain more information than you need to do this assignment, and so you would need to sift through it and figure out what is important and what can be ignored.

This PDF should have all you need to do the assignment. If you require clarification, use the discussion board. Also, share links to good resources and to resolve questions you might have. (But please, before posting, use the search feature to check if you question has already been asked and answered.)

<h1>2            Instructions</h1>

<h2>2.1          Starter code</h2>

In our implementation, the tree will store the data points at the leaves. We assume that the coordinate values of the data and query points are all int values, so the data and query points are all in Z<em><sup>k </sup></em>rather than &lt;<em><sup>k</sup></em>, where Z is a common symbol for the set of integers. The starter code contains three classes:

<ul>

 <li>KDTree: This class has several fields including rootNode which is the root of the tree and is of type KDNode which is an inner class. It also has an integer field k which is the number of dimensions, i.e. Z<em><sup>k</sup></em>.</li>

 <li>Datum: This class holds the coordinate values <em>x</em>[ ] of a data point or query point.</li>

 <li>Tester: Some example tests to verify the correctness of your code.</li>

</ul>

<h2>2.2          Your Task</h2>

Implement the following methods in the KDTree class:

<ul>

 <li>(50 points) KDNode constructor</li>

</ul>

The constructor KDNode is given an array of Datum objects, and it constructs a KDNode object that is the root of a subtree whose leaves contain the given Datum objects.

The KDNode constructor should split the given data points such that, on average, about half go into the low child and the other about half go into high child, and so the resulting tree is approximately balanced. The height of the tree should be roughly log<sub>2</sub>(<em>N</em>) where <em>N </em>is the number of data points.

It is difficult to choose a splitting rule that guarantees an equal (half-half) split, and so we will not require this. In particular, even if you were to choose the splitting value to be the median value of the data points in the splitting dimension, a very unbalanced tree could still result if many data points had that splitting value at many of the nodes. It is good enough for you to let the split be the average of the min and max value in the splitting dimension. If properly implemented, this will lead to roughly log<sub>2</sub>(<em>N</em>) height of the test trees. We have provided you with a helper method KDTree.height that returns the height of the tree, and KDTree.meanDepth that computes mean depth of the leaves of a tree.

If there are duplicate data points in the input, then your constructor must put only one copy of the duplicate points into the tree. It is up to you to find an efficient way to remove duplicates. What you should <em>not </em>do is try to remove duplicates from the initial list of data points, since this would take time <em>O</em>(<em>N</em><sup>2</sup><em>K</em>) – namely comparing all pairs of data points for equality on each dimension – which is too slow for large data sets. We will test that you have correctly and efficiently removed duplicates.

<ul>

 <li>(20 points) KDTreeIterator</li>

</ul>

The method kDTreeIterator returns an Iterator object that can be used to iterate through all the data points. <em>The order of points in your iterator must correspond to an inorder traversal of your kd-tree, namely it must visit all data points in the low subtree of any node before it visits any of the data points in the high subtree.</em>

We will test your iterator by examining the order of data points. (The Grader will have access to the KDNode objects and various fields of your tree: we have given these fields package visibility.)

The tester that we provide you does not provide a test of your iterator, however. We suggest you create your own iterator test cases, and share these testers with each other. For example, you could test a set of 1D data points and ensure data points are visited in their correct order.

It is fine if your iterator stores references to all the data points (Datum objects). There are more space efficient ways to make iterators from trees, which represent only one path in the tree at any time. But we do not require this space efficiency.

<ul>

 <li>(30 points) nearestPointInNode</li>

</ul>

The method nearestPointInNode takes a query point Datum and returns a data point Datum that is as close to the query point as any other data point. There may be multiple points that have the same nearest distance to the query point, and in this case your method should return one of them.

<a href="#_ftnref1" name="_ftn1">[1]</a> <a href="https://en.wikipedia.org/wiki/Feature_(machine_learning)">https://en.wikipedia.org/wiki/Feature_(machine_learning)</a>

<a href="#_ftnref2" name="_ftn2">[2]</a> Note that the nearest point may be non-unique.

<a href="#_ftnref3" name="_ftn3">[3]</a> You can think of them as ‘left’ and ‘right’, respectively, if you like but keep in mind that we are in a <em>k </em>dimensional space. In a 3D space, you might have left/right, up/down, back/front. We will use the same words for each dimension, namely low/high.