Authors: Lagniez Jean-Marie
Date: 2017/07/09

# :exclamation: Changes
The directory contains the following modifications made for the purposes of the paper.

### 1) Seed
Reason: to randomise compilations <br>
File: heuristics/ClauseBipartiteGraphPartitioner.cc <br>
Line: 144 (changed) <br>
Original: *args.seed = 1;* <br>
New: *args.seed = -1;*

### 2) Stopwatch
Reason: to correctly measure compilation times <br>
File: core/Main.cc <br>
Line: 20 (added), 136 - 138 (added), 141 - 146 (added)

## Paper

    @article{Illner_Kucera_2024, 
        author  = {Illner, Petr and Kučera, Petr}, 
        title   = {A Compiler for Weak Decomposable Negation Normal Form}, 
        volume  = {38}, 
        url     = {https://ojs.aaai.org/index.php/AAAI/article/view/28926}, 
        DOI     = {10.1609/aaai.v38i9.28926}, 
        number  = {9}, 
        journal = {Proceedings of the AAAI Conference on Artificial Intelligence},
        year    = {2024}, 
        month   = {Mar.}, 
        pages   = {10562-10570} 
    }

# How to compile

To compile and print out the help please use the following command lines:

```bash
make -j8
./d4 --help
```

To compile in debug mode (make possible the use of gdb and valgrind) and print out the help  please use:

```bash
make -j8 d
./d4_debug --help
```

To compile in profile mode (make possible to use gprof) and print out the help please use:

```bash
make -j8 p
./d4_profil --help
```


To compile in static mode and print out the help please use:

```bash
make -j8 rs
./d4_static --help
```

# How to run

To run the model counter:

```bash
./d4 -mc benchTest/littleTest.cnf
```

To run the dDNNF compiler:

```bash
./d4 -dDNNF benchTest/littleTest.cnf
```

To get the resulting decision-DNNF representation in file /tmp/test.nnf please use:

```bash
./d4 -dDNNF benchTest/littleTest.cnf -out=/tmp/test.nnf
cat /tmp/test.nnf
o 1 0
o 2 0
o 3 0
t 4 0
3 4 -2 3 0
3 4 2 0
2 3 -1 0
2 4 1 0
1 2 0
```


Note that the format used now is an extension of the previous format
(as defined in the archive of c2d available from http://reasoning.cs.ucla.edu/c2d/).
The management of propagated literals has been improved in the new format, where
both nodes and arcs are represented. When a literal becomes true at some node
there is no more need to create an AND node and a literal node to capture it.
Instead the literal is attached to the arc connecting the node with its father.
Each line represents a node or an arc, and is terminated by 0.
When a line represents a node it starts with a node type and is followed by its index.
Here are the node types:

    o, for an OR node
    f, for a false leaf
    t, for a true leaf
    a, for an AND node (not present in this example)

    The second argument just after the type of node is its index.

    In the example above the decision-DNNF representation has
    3 OR nodes (1, 2 and 3) and 1 true node (4).

As expected arcs are used to connect the nodes.
In the file .nnf, arcs are represented by lines starting with a node index
(a positive integer, the source node), followed by another node index
(a positive integer, the target node), and eventually a sequence of literals
that represents the unit literals that become true at the target node.


    In the example, 3 4 -2 3 0 means that OR node of index 3 is connected to the
    true node of index 4 and the literals -2 and 3 are set to true.


To get the resulting certified decision-DNNF representation in file /tmp/test.nnf enhanced
with the drat proof saved in /tmp/test.drat, please use:

```bash
./d4 -dDNNF benchTest/littleTest.cnf -out=/tmp/test.nnf -drat=/tmp/test.drat
cat /tmp/test.nnf
o 1 1 0
o 2 2 0
o 3 2 1 0
t 4 0
3 4 2 -2 3 0
3 4 2 2 0
2 3 2 -1 0
2 4 2 1 0
1 2 2 0
cat /tmp/test.drat
1 2 3 0
d 1 2 3 0
```

The format used for certifying the d-DNNF has been sligthly modified in order to gather the clauses that have been used to propagate the unit literals.
Since literals are always associated to decision nodes, this information has been added on these kind of nodes.
More precisely, just after the index of the node we can now find the number of branches the node has.
Then the indexes of the clauses, following the order in the drat file, are given.


    In the example, 0 1 1 0 means that we are considering an OR node of index 1 which is connected to 1 node. 0 3 2 1 0 means that we are considering an OR node which is connected with 2 other 
    nodes and such that the clause with the index 1 has been used to progatate some literals on some branches connected to it.
    
