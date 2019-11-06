---
layout: post

---

# Project: PyChess (Part 02)
### Console GUI:

The first design choice is to make a console based GUI to represent the board in the simplest way. The code should be written to port it to better graphics in the future.
This is a copy of the rendering for all the pieces on the board:

```shell
 -- -- -- -- -- -- -- -- 
|Rw|Nw|Bw|Qw|Kw|Bw|Nw|Rw|
 -- -- -- -- -- -- -- -- 
|Pw|Pw|Pw|Pw|Pw|Pw|Pw|Pw|
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|Pb|Pb|Pb|Pb|Pb|Pb|Pb|Pb|
 -- -- -- -- -- -- -- -- 
|Rb|Nb|Bb|Qb|Kb|Bb|Nb|Rb|
 -- -- -- -- -- -- -- -- 
```

Color might be added in a future contribution, for now the piece name indicates its type and color.

### Object oriented design
The initial design is based on the combination of three classes:

- **`Square`** -> in charge of handling the coordinates of pieces in the board. A square is described by its file and rank.
- **`Piece`** -> in charge of handling the chess pieces with an individual approach. An object of this class is identified by its name, color, square and state.
- **`Chess`** -> main class in charge of handling the game. Contains the object pieces and it is in charge of the game dynamics and rendering.

### Rules for individual chess piece movements
Coding the entire rules for chess is a rather cumbersome task, given the amount of types of pieces and the specific rules and situations that arise during the game. A few examples of unusual rules are castling, promotion, pawns taking *en passant* and many others.

To simplify the development and have a minimum playable game. I have decided to develop "sub-chess" games during the way. The first "sub-chess" will be to have an individual piece on the board that can move according to the chess rules. Since the movement is for a single piece, this logic is best suited for the **`Piece`** class.

The following image represents the legal moves for all pieces:


| ![chess_moves](https://study.com/cimages/multimages/16/rsz_chess_moves.jpg) |
| :--: |
| *Image link: https://study.com/cimages/multimages/16/rsz_chess_moves.jpg* |

The first task becomes computing all the legal moves for a certain chess piece in a certain square. The class **`Square`** was designed to store the position of a piece as two characters for file and rank. A method on this class allows to transform this coordinates into a integers in the range [0,7]. With this numeric values, computing the valid positions for a piece becomes easier.

For instance, the positions for a bishop are determined by diagonals, this diagonals can be reinterpreted as lines with unitary slope, one positive and another negative, when we think if the board as integer coordinates. The rooks on the other hand, can move over horizontal and vertical lines, which makes it easier to compute its positions. As a result the queens are a simple combination of the valid positions for rooks and bishops.

For the knights I have decided to simply hard-code the displacements in L-shapes with respect to the current square, there might more elegant mathematical options, but I decided to go for the fastest one.

All the logic to compute the valid squares for a piece can be found in the **`Piece.valid_squares()`** method. (Link to github repo at the end of the blog post)

The representation of the valid squares is available with the current console rendering of the game. The method **`Chess.print_valid()`** allows to visualize all the valid moves for a piece. Examples to demonstrate de validity for all pieces are showed next:

```console
 -- -- -- -- -- -- -- --  
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|Rb|++|++|++|++|++|++|++|
 -- -- -- -- -- -- -- -- 
```

``` console
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |++|  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |++|  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |Pb|  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
```

```console
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |++|++|++|  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |++|Kb|++|  |  |
 -- -- -- -- -- -- -- -- 
```

```console
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |++|  |++|
 -- -- -- -- -- -- -- -- 
|  |  |  |  |++|  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |Nb|  |
 -- -- -- -- -- -- -- -- 
```

```console
 -- -- -- -- -- -- -- -- 
|++|++|++|Qw|++|++|++|++|
 -- -- -- -- -- -- -- -- 
|  |  |++|++|++|  |  |  |
 -- -- -- -- -- -- -- -- 
|  |++|  |++|  |++|  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |++|  |  |++|  |
 -- -- -- -- -- -- -- -- 
|  |  |  |++|  |  |  |++|
 -- -- -- -- -- -- -- -- 
|  |  |  |++|  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |++|  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |++|  |  |  |  |
 -- -- -- -- -- -- -- -- 
```

```console
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |Bw|  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |++|  |++|  |
 -- -- -- -- -- -- -- -- 
|  |  |  |++|  |  |  |++|
 -- -- -- -- -- -- -- -- 
|  |  |++|  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |++|  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|++|  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
|  |  |  |  |  |  |  |  |
 -- -- -- -- -- -- -- -- 
```

### *What is next:*

Now that a list of all valid squares can be computed for certain piece, this list needs to be filtered/reduced according to the presence of other pieces. For instance all pieces are obstructed by others except for the knights. This logic needs to be implemented for the game to work.

Next, is the issue of introducing moves into the game. Initially the standard notation is considered, but a simpler one might be the best option for an initial implementation, since the standard notation is designed for brevity and it has to encompass various cases.

--- 
[pychess Github repository](https://github.com/johnrest/pychess "https://github.com/johnrest/pychess")