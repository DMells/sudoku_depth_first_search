### Python Sudoku Solver with Backtracking Depth-First Search & Heuristics
<i>David Mellor (Jan 2021)</i>

### Abstract
This project solves a series of Sudoku challenges with varying difficulties by applying the backtracking algorithm alongside 
heuristic algorithms.

With a test set of 60 different Sudokus with a range of difficulties from very easy to hard, the script completes the 
entire range in approximately 10 seconds.

##### Core algorithm
- Depth-first search with backtracking

 ##### Pruning heuristics used
 - Constraint satisfaction
 - Naked pairs/triples/quads
 - Single remaining value search
 
 ##### Heuristics to aid choice of square
 - Max degree
 - Minimum remaining value
 
 ##### Value heuristics used
 - Least constraining value  

### Introduction

##### The Game
A Sudoku is a puzzle consisting of a 9x9 grid, which starts with any number of filled-in squares. The puzzle is said
 to be said to be solved (if possible) when the following three constraints are satisfied:
1. The squares in each row must contain values 1-9 with no duplicates
2. The squares in each column must contain values 1-9 with no duplicates
3. The squares represented by each block of 3x3 squares must contain the values from 1-9 with no duplicates

##### Objectives
The task presented was to attempt to use the Python programming language (and packages) to solve each of 60 Sudokus 
(or determine that is is unsolvable) that range from "very easy" to "hard" within a maximum of 30 seconds per puzzle.

### Method
The principle approach to solving this challenge was the back-tracking algorithm. This is a depth-first search strategy
which traverses deeper into the nodes of the search tree until a solution is found at the leaf-node. At each node, the 
constraints are checked, and if they have been found to be unmet (i.e. the state is invalid), the algorithm back-tracks to the closest valid 
state/node to try a different route. Because the algorithm is only ever heading down the "solution branch", the space 
complexity is O(m) , where m is the maximum depth of any node i.e the number of blank squares. This is different from 
the standard depth-first search strategy which has space complexity O(bm), where b is the branching factor (9 in this case),
due to needing to store possibly invalid paths. [1][2]

 Heuristics are used in the program to accomplish three things:
 - Narrow down the remaining values lists (<i>pruning</i>)
 - Aid in the selection of which square to fill in next
 - Aid the decision of which value for that square to try next
 
 ##### Method
 1. Upon initialisation of each puzzle, the possible values for each square are calculated based on the starting numbers 
 on the board. These remaining possible values are determined by whether or not they would break the constraints 
 outlined above.
 2. Using the <i>naked pairs/triples/quads heuristic</i> [3], the remaining values are 'pruned', effectively reducing the search space. 
 This heuristic looks across each constraint (row, column or 3x3 block) to find duplicate pairs, triples or quads of remaining values.
 If for example within a row there are two sets of remaining values which are identical, and both of length 2, then it
 is known that those two values must go in those two squares. The exact allocation is not determined, but it is certain
 that those two values cannot go anywhere else in the row, so they are removed from the remaining values lists for the 
 other squares in the same row.
 3. Next is the first heuristic used to help the program choose which square to try next: the <i>minimum remaining values</i> heuristic [4] 
 This scans the remaining values lists and picks the squares that have the shortest counts of remaining values. 
 The idea behind this is choosing squares that have smaller possible choices increases the chance that the choice will
 be a valid one and therefore reduces the amount of back-tracking required. 
4. With a reduced list of squares to try next, the second heuristic for choosing a square is applied : the <i>maximum degree</i> heuristic.[5]
This algorithm checks across each of the 'minimum remaining value' squares and picks the square that is the most highly 
constrained by other squares on the board. The idea here is that in completing the max-degree square, there is a higher
chance that other squares on the board will be easier to solve as the total number of remaining values that can be pruned
is maximised.
5. Now that a square has been selected, the decision of which value to pick is determined by
the <i>least constrained value</i> heuristic[6]. This algorithm produces a count of how many times each possible value appears in the constraints
(the given squares row, column or block) and chooses the value with the lowest count. The idea is to choose a value
that minimises the probability of generating an invalid state.
6. At this point, the 'state' of the board is copied, such that if the updated state (see steps 7 & 8) produces an invalid
state (i.e. it is unsolvable) then the back-tracking algorithm will ensure that the previous valid state is restored, and
a different value is chosen.
7. With the chosen square and value, the program then sets that value to the board and updates the remaining values lists 
based on how this impacts the other constrained values, as well as removing that value from the respective remaining value list.
8. With this updated list, a <i>single value</i> heuristic is applied, which checks the remaining values lists for any single values
and sets them in the board if found.
9. As mentioned in step 6, the validity of the new state is checked, and either reversed or accepted. The process from steps 
2-8 is repeated until the puzzle is solved or it is determined as unsolvable.

### Results

##### Additional Heuristics
Initially I found that the max degree heuristic and least constrained value heuristic provided no additional benefit to
total run time of the tests. However, having successfully implemented naked pairs/triples/quads, I found that both heuristics
were able to take advantage of this and ended up reducing total run time. 

Naked pairs/triples/quads had a significant impact on the efficiency of the program, reducing the total run time from
55 seconds down to approximately 15 seconds. The addition of max degree and least constrained value further reduced the time
by about 4-5 seconds.  

##### Completion Times
- Very easy - approximately 0.0023 seconds per puzzle
- Easy - approximately 0.0028 seconds per puzzle
- Medium - approximately 0.008 second per puzzle
- Hard - ranging from 0.008 seconds to 2.01 seconds per puzzle

<b>Total run time approximately 10-11 seconds.</b>

 
#### Supporting Notes / Remarks
The max-degree heuristic is applied to the minimum remaining value squares (mrv). By trial and error it was found to be most beneficial
to the total running time if max degree is only applied to lists mrv squares of length 5 and above. Otherwise it was more 
efficient to just choose the first mrv square.

On optimisation, it was found in some situations that refactoring multiple iterative functions into a single
iterative function increased total runtime slightly, despite there being less calculations to perform at runtime. The same
was found when converting some of the more complex nested functions to list comprehensions. For that reason there is 
a mixture of list comprehensions and nested functions throughout. 

#### Further Work
Additional investigations into optimisation such as multi threading could provide opportunities to improve the run time of the program.
There are additional heuristics which I have not explored such as "X-Wing"[7]. All explored heuristics eventually added some benefit and were retained. 


### References
   [1][4][5][6] Norvig, Peter, & Russel, Stuart "Artificial Intelligence : A Modern Approach 3rd Edition" pp 87,216,217 Pearson Education Limited, 2016
   
   [2] Kanaikia, Anshul & Klingner, John "Methods For Solving Sudoku Puzzles" pp 3 & 4 "http://tuvalu.santafe.edu/~aaronc/courses/5454/csci5454_spring2013_CSL5.pdf"
   
   [3] Hodoku, "Naked Subsets" "http://hodoku.sourceforge.net/en/tech_naked.php"
   
   [7] "How to identify Sudoku X-Wing",  "https://www.sudokuessentials.com/x-wing.html#:~:text=An%20X%2DWing%20pattern%20occurs,4%20in%20only%20two%20cells."
