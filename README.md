<a name="br1"></a> 

Final Project

Software Project (0368-2161)

Due Date: 24/04/2022, NO EXTENSIONS!

1 Introduction

In this project you will implement a version of the normalized spectral clustering algorithm. This

document starts by introducing the mathematical basis and algorithms for the project, and then

describes the code and implementation requirements. We are not going to provide a tester for the

project – implementation and correctness are up to you. You will be graded for code modularity,

design, readability, and performance.

Normalized Spectral Clustering We present the Normalized Spectral Clustering algorithm based

on [[1](#br8), [2](#br8)]. Given a set of n points X = x , x , . . . , x ∈ R<sup>d</sup> the algorithm is:

1

2

N

Algorithm 1 The Normalized Spectral Clustering Algorithm

1: Form the weighted adjacency matrix W from X

2: Compute the normalized graph Laplacian L<sub>norm</sub>

3: Determine k and obtain the ﬁrst k eigenvectors u , . . . , u of L

norm

1

k

n×k

4: Let U ∈ R

be the matrix containing the vectors u , . . . , u as columns

n×k

1

from U by renormalizing each of U’s rows to have unit length, that is

k

5: Form the matrix T ∈ R

P

2

1/2

set t = u /(

u )

ij

6: Treating each row of T as a point in R , cluster them into k clusters via the K-means algorithm

ij

ij

j

k

A few things to keep in mind:

• Step 3 - eigenvalues must be ordered increasingly, respecting multiplicities (for example, all

the ﬁrst ﬁve eigenvalues could be equal to 0). By “the ﬁrst k eigenvectors” we refer to the

eigenvectors that correspond to the k smallest eigenvalues. Determining k would be based on

the eigengap heuristic described at subsection [1.3](#br4)[ ](#br4)or given as an input.

• Step 6:

– (Python): you should use the K-means implementation from the homework assignments,

with the needed adjustments to ﬁt the project. You are expected to use the K-means++

initialization when applying K-means as in HW 2. The MAX ITER variable should be set to

300\.

1



<a name="br2"></a> 

1\.1 Graph Representation

We aim at creating an undirected graph G = (V, E; W), that will represent the n points at hand. Each

point x is viewed as a vertex v (also known as node) to produce V = {v , v , . . . , v }. A common

i

mapping is to set v = i. The set of edges (also known as arcs) E will be the union of all connected

i

1

2

n

i

pairs {v , v }. Next, we present the way to decide the weights of the graph and the structure of the

i

j

graph.

1\.1.1 The Weighted Adjacency Matrix

Let w<sub>ij</sub> represent the weight of the connection between v and v . Only if w > 0, we deﬁne an edge

i

j

(also referred to as the aﬃnity matrix)

ij

n×n

between v and v . The weighted adjacency matrix W ∈ R

ij i,j=1,...,n

i

is deﬁned as W = (w )

j

. The weights are symmetric (w = w ) and non-negative (w ≥ 0).

ij

ji

As we do not allow self loops, we set w = 0 for all i’s. In order to create a fully connected graph, the

ij

ii

rest of the values are set to:

~~k~~x ~~−~~ x ~~k~~

i

j

2

w<sub>ij</sub> = exp (−

)

2

q

P

d

i=1

We denote by kk the ` -norm or the Euclidean norm (ka − bk =

(a − b )<sup>2</sup>).

2

2

2

i

i

1\.1.2 The Diagonal Degree Matrix

The diagonal degree matrix D ∈ R<sup>n×n</sup> is deﬁned as D = (d )

, such that:

ij i,j=1,...,n

(

P

n

z=1

w<sub>iz</sub>, if i = j

d =

ij

0,

otherwise

We get that i-th element along the diagonal equals to the sum of the i-th row of W. In essence, D’s

diagonal elements represent the sum of weights that lead to vertex v<sub>i</sub>. Note that





1

d<sub>11</sub>

√

0

. . .

. . .

.

0





1

 0

√

0

.



1

2



d



D<sup>−</sup> = 

22

.

.



.

.

.

.

 .

.

.

. 

1

0

0

. . .

√

d

nn

1\.1.3 The Normalized Graph Laplacian

The normalized graph Laplacian L<sub>norm</sub> ∈ R<sup>n×n</sup> is deﬁned as

1

2

1

2

L<sub>norm</sub> = I − D<sup>−</sup> WD<sup>−</sup>

The reason we are interested in L<sub>norm</sub> is that it has all eigenvalues λ<sub>1</sub> ≤ . . . ≤ λ<sub>n</sub> are real and

non-negative.

1\.2 Finding Eigenvalues and Eigenvectors

In this section, we lay the foundation needed to ﬁnd all of the eigenvectors and eigenvalues of a real,

symmetric, full rank matrix. Then we present a heuristic that utilizes the eigenvalues to determine

2



<a name="br3"></a> 

the number of k clusters the data holds. It is a mathematical approach very similar to the visual

”elbow” method presented in the second homework assignment.

1\.2.1 Jacobi algorithm

The Jacobi eigenvalue algorithm is an iterative method for the calculation of the eigenvalues and

eigenvectors of a real symmetric matrix (a process known as diagonalization).

1\. Procedure

(a) Build a rotation matrix P (as explained below).

(b) Transform the matrix A to:

0

A = P AP

T

A = A<sup>0</sup>

(c) Repeat a,b until A<sup>0</sup> is diagonal matrix.

(d) The diagonal of the ﬁnal A<sup>0</sup> is the eigenvalues of the original A.

(e) Calculate the eigenvectors of A by multiplying all the rotation matrices:

V = P P P . . .

3

1

2

2\. Rotation Matrix P

Let S be a symmetric matrix and P be the Jacobi rotation matrix of the form:





1





. . .









c

. . .

s







.

.



P = 

.

.



.

1

.









−s . . .

c









. . .

1

Here all the diagonal elements are unity except for the two elements c in rows (and columns) i

and j and all oﬀ-diagonal elements are zero except the two elements s and −s. Also, s = sin φ

and c = cos φ.

3\. Pivot

The A<sub>ij</sub> is the oﬀ-diagonal element with the largest absolute value.

4\. Obtain c, t

A<sub>jj</sub> − A<sub>ii</sub>

θ = cot 2φ =

2A

ij

sign(θ)

t =

√

|θ| + θ<sup>2</sup> + 1

1

c = √

,

s = tc

t<sup>2</sup> + 1

Note: We deﬁne sign(0) = 1

3



<a name="br4"></a> 

2

5\. Convergence: Let off(A) and off(A ) be the sum of squares of all oﬀ-diagonal elements of

0 2

A and A<sup>0</sup> respectively. Then the square of oﬀ diagonal elements of A is

X<sup>N</sup> X<sup>N</sup>

off(A)<sup>2</sup> =

a

ij

2

i=1 j=1,j=i

At step c in the above procedure, we deﬁne convergence as follow:

2

0 2

off(A) − off(A ) ≤ ꢀ

We will be using ꢀ = 1.0 × 10<sup>−5</sup> OR maximum number of rotations = 100

6\. Relation between A and A<sup>0</sup>:

After each transformation of step 2, the modiﬁed elements of A are only the i and j rows and

columns. Therefore, from the symmetry of A, we obtain the following formula to calculate A<sup>0</sup>:

0

a = ca − sa

r = i, j

r = i, j

ri

ri

rj

0

rj

a

= ca + sa

rj ri

0

2

2

a = c a + s a <sup>− 2sca</sup>ij

ii

ii

jj

0

jj

2

2

a

= s a + c a <sup>+ 2sca</sup>ij

ii jj

0

2

2

0

ij

a = (c − s )a + sc(a − a ) ⇒ a = 0

ij

ij

ii

jj

Note: A<sup>0</sup> is always symmetric.

1\.3 The Eigengap Heuristic

In order to determine the number of clusters k, we will use eigengap heuristic as follow:

let (δ )

be the eigengap for the increasingly ordered eigenvalues 0 ≤ λ ≤ . . . ≤ λ of L<sub>norm</sub>,

i i=1,...,n−1

1

n

deﬁned as:

δ = |λ − λ<sub>i+1</sub>|

The eigengap measure could indicate the number of clusters k through the following estimation:

i

i

j k

n

k = argmax (δ ), i = 1, . . . ,

i

i

2

In case of equality in the argmax of some eigengaps, use the lowest index.

4



<a name="br5"></a> 

2 Assignment Description

Implement the following ﬁles:

1\. spkmeans.py: Python interface of your code.

2\. spkmeans.h: C header ﬁle.

3\. spkmeans.c: C interface of your code.

4\. spkmeansmodule.c: Python C API wrapper.

5\. setup.py: The setup ﬁle.

6\. comp.sh: Your compilation script.

7\. \*.c/h: Other modules and headers per your design.

2\.1 Python Program (spkmeans.py)

1\. Reading user CMD arguments:

(a) k (int, < N): Number of required clusters. If equal 0, use the heuristic [1.3](#br4).

(b) goal (enum): Can get the following values:

i. spk: Perform full spectral kmeans as described in [1](#br1)[.](#br1)

ii. wam: Calculate and output the Weighted Adjacency Matrix as described in [1.1.1](#br2).

iii. ddg: Calculate and output the Diagonal Degree Matrix as described in [1.1.2](#br2).

iv. lnorm: Calculate and output the Normalized Graph Laplacian as described in [1.1.3](#br2).

v. jacobi: Calculate and output the eigenvalues and eigenvectors as described in [1.2.1](#br3).

(c) file name (.txt or .csv): The path to the ﬁle that will contain N observations, the ﬁle

extension is .txt or .csv.

2\. Implementation of the k-means++ algorithm when the goal=spk, as detailed in HW2:

(a) Set np.random.seed(0) at the beginning of your code.

(b) Use np.random.choice() for random selection.

3\. Interfacing with your C extension spkmeansmodule. All implementations of the diﬀerent goals

must be performed by calling the C extension.

4\. Outputting the following:

In case of ’spk’:

• The ﬁrst line will be the indices of the observations chosen by the K-means++ algorithm

as the initial centroids. We refer to the ﬁrst observation index as 0, the second as 1 and so

on, up until N - 1.

• The second line onwards will be the calculated ﬁnal centroids from the K-means algorithm,

separated by a comma, such that each centroid is in a line of its own.

For the other cases, output the required matrix separated by a comma, such that each row is in

a line of its own.

Example:

5



<a name="br6"></a> 

\>>>python3 spkmeans.py 3 spk input\_1.txt

0,4,22

-4.2435,9.1568,5.4105

3\.3226,-1.3896,-9.1927

8\.2239,-8.5714,-8.4985

2\.2 C Program (spkmeans.c)

This is the C implementation program, with the following requirements:

1\. Reading user CMD arguments:

(a) goal (enum): Can get the following values:

i. wam: Calculate and output the Weighted Adjacency Matrix as described in [1.1.1](#br2).

ii. ddg: Calculate and output the Diagonal Degree Matrix as described in [1.1.2](#br2).

iii. lnorm: Calculate and output the Normalized Graph Laplacian as described in [1.1.3](#br2).

iv. jacobi: Calculate and output the eigenvalues and eigenvectors as described in [1.2.1](#br3).

(b) file name: The path to the ﬁle that will contain N observations, the ﬁle extension is .txt

or .csv.

2\. Outputting the following:

Output the required matrix separated by a comma, such that each row is in a line of its own.

The program must compile cleanly (no errors, no warnings) when running the following command:

$bash comp.sh

After successful compilation the program can run for Example:

\>>>./spkmeans 3 lnorm input\_1.txt

-4.2435,9.1568,5.4105

3\.3226,-1.3896,-9.1927

8\.2239,-8.5714,-8.4985

2\.3 Python C API (spkmeansmodule.c)

In this ﬁle you will deﬁne your C extension which will be, mainly, your wrap of the algorithm imple-

mented in spkmeans.c.

You can use functions implemented only in spkmeans.c. It is up to you to decide which and what

type of arguments you want to pass when calling the API. Same thing about the return.

2\.4 C Header ﬁle(spkmeans.h)

This header have to deﬁne all functions prototypes that is being used in spkmeansmodule.c and

implemented at spkmeans.c.

6



<a name="br7"></a> 

2\.5 Setup (setup.py)

This is the build used to create the \*.so ﬁle that will allow spkmeans.py to import spkmeans.

2\.6 Build and Running

1\. The extension must built cleanly (no errors, no warnings) when running the following command:

$python setup.py build\_ext --inplace

2\.7 comp.sh

Script for compiling your ﬁles. The compilation command should include all the ﬂags as below with

your modules accordingly:

#!/bin/bash

\# Script to compile and execute a c program

gcc -ansi -Wall -Wextra -Werror -pedantic-errors spkmeans.c -lm -o spkmeans

2\.8 Assumptions and requirements:

1\. You may assume that the input ﬁles are in the correct format.

2\. All the algorithmic implementations detailed at [1](#br1)[ ](#br1)must be implemented in the C program, except

the k-means++ which is in Python (as in HW2).

3\. Validate that the command line arguments are in correct format.

4\. Outputs must be formatted to 4 decimal places (use: ’%.4f’) in both languages, for example:

• 8.88885 ⇒ 8.8888

• 5.92237098749999997906 ⇒ 5.9224

• 2.231 ⇒ 2.2310

5\. If the Jacobi doesn’t reach convergence [5](#br4)[,](#br4)[ ](#br4)you should run it up to 100 iteration.

6\. Your code should be gracefully partitioned into functions. The design of the program, including

the main interface\integration, function declarations, and how they interact is entirely up to

you. You should aim for modularity, reuse of code, clarity, and logical partition.

7\. Source ﬁles should be commented at critical points in the code and at function declarations.

8\. Please avoid long lines of code, and shorten lines that exceed 80 characters.

9\. You may import external includes (in C) or modules (in Python) that are not mentioned in this

document.

10\. Comment your code. If you use code from the internet, please cite the source.

11\. Handle errors as following:

(a) In case of invalid input, print ”Invalid Input!” and terminate the program.

7



<a name="br8"></a> 

(b) Else, print ”An Error Has Occurred” and terminate.

12\. Do not forget to free any memory you allocated.

13\. You can assume that data dimensionality is up to 1000 points and up to 10 features.

14\. You can assume that all given data points are diﬀerent.

15\. Use double in C and float in Python for all vector’s elements.

16\. There is an example doc that depicts the steps of the algorithm (you can ﬁnd at Moodle under

the project section), you can generate data using the provided method in the example document

and test yourself.

2\.9 Submission

1\. Please submit a ﬁle named id1 id2 final.zip via Moodle, where id1 and id2 are the ids of the

partners.

(a) In case of individual submission, id2 must be 111111111

(b) Put the following ﬁles in a folder called id1 id2 final:

i. spkmeans.py

ii. spkmeans.h

iii. spkmeans.c

iv. spkmeansmodule.c

v. setup.py

vi. comp.sh

vii. \*.c/h

(c) Zip the folder using the following linux cmd:

$zip -r id1\_id2\_final.zip id1\_id2\_final

Do not use other ways to create the zip!

2\.10 Remarks

1\. For any question regarding the assignment, please post at the Final Project’s discussion forum.

2\. No test ﬁles are provided, you can generate data using sklearn.datasets.make blobs

3\. An example of using C code for direct call and for Python is provided with the assignment under

Demo1.

4\. You can create symmetric matrices in order to test Jacobi by using:

a = np.random.rand(N, N)

m = np.tril(a) + np.tril(a, -1).T

References

[1] Andrew Ng, Michael Jordan, and Yair Weiss. On spectral clustering: Analysis and an algorithm.

Advances in neural information processing systems, 14:849–856, 2001.

[2] Ulrike Von Luxburg. A tutorial on spectral clustering. Statistics and computing, 17(4):395–416,

2007\.

8

