---
title: My Sudoku Solver
date: 2025-01-21 7:00:00 -500
math: true
categories: [python programming, algorithmic problem solving]
tags: [python programming,backtracking,data validation,logic puzzles]
---

# Background

Sudoku is a classic single player game that many people know and love. It consists of a nine by nine grid of squares, with some squares filled in with a digit 1-9, and the rest empty. Within the main grid are nine three by three subgrids. The goal is for the player to fill the grid with numbers, while following the three big rules.

* Each row must contain every number, 1-9, in no particular order

* Each column must contain every number, 1-9, in no particular order

* Each subgrid must contain every number, 1-9, in no particular order

While this may seem easy, it can be frustrating. Before I started making this program, I solved a few Sudoku puzzles, and would encounter this problem where everything seemed to be going well, until I realized that no matter what, how I had it aranged was breaking one of the rules. The method that I found worked the best involved marking which numbers were not able to be in each spot, and eventually narrowing it down to one number in a square. This seemed to cause a domino like affect, causing everything to be ruled out until I arrived at the answer. This stratagy, however, was much less effective for more difficult puzzles, with fewer marked squares. Taking this information, I started to think about my approach.

# My Approach

After concidering several options, I decided a backtracking algorithm would do the job well. Backtracking is a process in which you explore all posibilities until either the solution is found or everything is exhausted. If everything is explored under one option, the next option is explored. If nothing is found, then there is no solution. An example of this is a desicion tree, where each branch splits into more branches, until it reaches that end state for those decisions. Backtracing has a subtype of algorith called Depth First Search (DFS), which makes a desion randomly, and explores that branch until a solution is found or it is exauhsted. Then the next desicion that could have been made is made, and the cycle repeats.

## Backtracking and Pruning

The approach for Sudoku is very similar. At the first unknown nuumber, the algorith would choose a number, then at the next one, it would choose another number. This would continue until that path is exhausted. Then the algorith would back up one desicion, and choose a different number. Then, once the board reached a solved state, it would break and return the solved board. The one issue with this seemingly briliant plan is that because of the nature of this type of algorithm, the time to go through all the posibilities until a solved state is reached grows exponentially. And Sudoku has a lot of different end states. The total number of states that would have to be checked is around 
$$
9^{81} \approx 2.96 \times 10^{77}
$$
possibilities. That an astronomically large number, and complete unrealistic. Luckily there is a method for dealing with this very problem. The answer is pruning. Pruning is were the algorith has some way for determining which solutions could be ruled out imediatly, so that they and everything that follows is not concidered. In this instance, the method for pruning wuld be the rules of Sudoku. If the possible number broke any rule, it would be disregarded for that desicion. With this pruning, the posible states were cut down to millions or billions, much more manageable from a time perspective.

## Recursive Functions

The way in which I intended to make the Backtracking algorithm was using a recursive function. A recursive function require two things. First, it requires the function to call itself. Second, there must be some check for the desired state or solved state. Imagine a function that uses recursion to make a countdown, that takes arguements n, which is the number for the countdown.

```python
def countdown_with_recursion(n):
    if n == 0:
        print("Blastoff!")
    else:
        print(n)
        countdown_with_recursion(n - 1)
```
In this function, the if else fufills the two required things for a recursive function. In the if clause, the function checks if n is zero. If so, it will end the function and print "Blastoff!". But if n is not equal to zero, the function runs within itself with n-1. This process repeats again within the recursion, until n is equal to zero. If the function was called with arguement n equal to 5, this would be the output.

```zsh
5
4
3
2
1
Blastoff!
```

As you can see with this output, the function starts at 5, but then using recursion, goes through calling n - 1 until the desired state is reached. Here, when n is equal to zero, the desired state is reached, and the recursion breaks!

## Pruning

Pruning allows the algorithm reduce the amount of data searched. Imagine being stuck at a fork in the road. You do not know which road will lead you to where you want to go. This is what a DFS algorithm has to do. It eventually chooses one, explores it thoroughly, then goes back and does the next one. This takes a lot of time. If only we could take some part of the data, and compare it to a set of rules that are established, and if it breaks the rules then we do not explore it. That is what pruning is! I talked about this earlier in the backtracking and pruning section, however it is so crucial to this algorithm that I want to give it it's own section. I also just love prunning so much, and use it in many projects of mine and in [Advent of Code](https://adventofcode.com/)! Anyways, I want to include a basic example of prunning with a DFS algorithm to briefly explain how it is so amazing. Here is a python script with two functions. First, prune takes two arguements, the node and the limit. Then the second function, depth_first_search, takes three arguements: node, target, and limit. when the second function is called, it searches for the target until either the limit is hit, or the target is found.

```python
def prune(node, limit):
    return node > limit 

def depth_first_search(node, target, limit):
    if prune(node, limit):
        return False

    if node == target:
        return True

    return depth_first_search(node + 1, target, limit)
```

In the first function, when the node is greater than the limit, the prunning function returns True. In the search function, when the prunning function returns True, the search function returns False. (I know that the prunning function could be within the search function, however in the actual Sudoku Solver, the prunning function is outside the solving function. It is also a stylistic choice?) Below the if statement for prunning, there is an if statement that checks if the node is equal to the target. If so, it returns True. Then if neither is if statement is True, the the functin recursively calls itself. Lets look at some outputs!

```python
print(depth_first_search(1, 10, 4)) # Prints False
print(depth_first_search(1, 10, 15)) # Prints True
```

When the search function is called with these arguements set to these values, it returns the commented values. And when you think about the function it makes sense. It starts at whatever node you put, then it goes up incrementally until either the target or limit is found. If the limit is smaller than the target, it will be found first and return False. However if the limit is greater it will return True! This is a basic example of prunning in a DFS algorithm, using recursion. The actual code is slightly more complicated than this, however the principles are the same.

# Sudoku Solver

My first step after making my ideas and having a plan was to decide how I wanted to make the board. I decided that a list made of 9 lists of 9 numbers would be easiest. Then any number that was not already set would be a zero. Here is an example board!

```python
example_board = [
    [0, 0, 0, 2, 6, 0, 7, 0, 1],
    [6, 8, 0, 0, 7, 0, 0, 9, 0],
    [1, 9, 0, 0, 0, 4, 5, 0, 0],
    [8, 2, 0, 1, 0, 0, 0, 4, 0],
    [0, 0, 4, 6, 0, 2, 9, 0, 0],
    [0, 5, 0, 0, 0, 3, 0, 2, 8],
    [0, 0, 9, 3, 0, 0, 0, 7, 4],
    [0, 4, 0, 0, 5, 0, 0, 3, 6],
    [7, 0, 3, 0, 1, 8, 0, 0, 0]
]
```

The next thing I needed to do was make helper functions. These functions would make that actual solving and pruning algorithms much easier! Here is one helper function that I made!

```python
def get_columns(board, num):
    if num < 1 or num > 9:
        print("Invalid column number")
        return []
    return [row[num - 1] for row in board]
```

This helper function takes two arguements, first the board, and second the numbered column desired. Then it returns a list of the numbers in that column. It does this by looping through each row, and adding the number of that column to the list. This function also has a safeguard, so that if the num arguement is passed as a number that isn't between 1 and 9, then instead of throughing an error, the code will print a message and return an empty list.
## Pruning Function

The is_valid_move function is what prunes the backtracking algorithm. It takes four arguements: board, row, column, and number. The row and column arguements work as a xy axises, provide the cooridantes of the number being placed, and the number is the number being placed. Here is the function.

```python
def is_valid_move(board, row, col, num):
    if num in board[row]:
        return False
    if num in [board[i][col] for i in range(9)]:
        return False
    start_row, start_col = 3 * (row // 3), 3 * (col // 3)
    for i in range(start_row, start_row + 3):
        for j in range(start_col, start_col + 3):
            if board[i][j] == num:
                return False
    return True
```

First the function checks if the number is in the row and column. If either, the function returns False, because that would break either the first or second rule. Checking for the thrid rule is slightly tricky, however it works. The function first calculates the top left corner of the subgrid the number is trying to be placed in. The function then uses a nested for loop to check each number in that subgrid to see if it matches. If so, the function returns false. This insures that the function will only return True of the number breaks no rules for where it is trying to be placed. This eleminated many options that would never work.

## Solving Function

I am going to show the function and then explain what it does!

```python
def solve_sudoku(board):

    def find_empty_cell():
        for row in range(9):
            for col in range(9):
                if board[row][col] == 0:
                    return row, col
        return None

    empty_cell = find_empty_cell()
    if not empty_cell:
        return board

    row, col = empty_cell

    for num in range(1, 10):
        if is_valid_move(board, row, col, num):
            board[row][col] = num
            if solve_sudoku(board):
                return board
            board[row][col] = 0

    return False
```

Starting off, The function takes one arguement, board, and returns the solved board if there is one. If not, it will return false. First thing within this function is a second function, called a nested function. This is used because it eliminates a reason to pass the arguement board, because the function "remembers" it. The find_empty_cell function looks at each cell and checks if it is a zero. The outer function calles the nested function, and has an if statement. This checks if there are any zeroes. If not, then the board is filled and returned. Then, if there are zeros, it starts at the first zero, and checks numbers 1 - 9. However this is where prunning gets involved. The is_valid_move function checks if the move follows all the rules. If the number passes the valid move check, it is the recursively called until there are no more zeroes. If this never happens, then the function returns false, because there is no solution.

After running this function on the example board, this is the output:

```python
solved_board = [
    [4, 3, 5, 2, 6, 9, 7, 8, 1],
    [6, 8, 2, 5, 7, 1, 4, 9, 3],
    [1, 9, 7, 8, 3, 4, 5, 6, 2],
    [8, 2, 6, 1, 9, 5, 3, 4, 7],
    [3, 7, 4, 6, 8, 2, 9, 1, 5],
    [9, 5, 1, 7, 4, 3, 6, 2, 8],
    [5, 1, 9, 3, 2, 6, 8, 7, 4],
    [2, 4, 8, 9, 5, 7, 1, 3, 6],
    [7, 6, 3, 4, 1, 8, 2, 5, 9]
]
```

# Conclusion

This project was super fun to work on. I really like working with algorithms like these to solve problems like this. It is very satisfying to be able to put in an unsolved sudoku board, and have a solved board as the output. Some things that I could improve with this script all plentiful, however I can think of a few big things. First, function that creates unsolved Sudoku boards. This could then make this a kind of game. Building of that idea, I could use a library like [Pygame](https://www.pygame.org/news) to make a user interface, which would make it much easier to input different boards. Finally, I could add a heuristic that prioritizes solving specific cells first, making the solving process more effiecent. I hope that I will come back to this project in the future and include these inprovements, because Sudoku is a pretty fun game to play. The code is available on my [Github](https://github.com/ETpresKaz/Sudoku-Solver), and that you so much!