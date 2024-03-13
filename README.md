                                   Reversi - CS3500
                              By Kevin Jones and Jan Heinz



Overview:
For our game of Reversi, we make these assumptions:
    - A player knows their color (black or white).
    - They know the basic rules of Reversi and what makes a move valid.

Changes for Part 2:
    - Playing no longer requires colors or players
    - Added the following methods to our read only interface:
        - getBoard
        - getPossibleMoves
        - isMoveLegal
        - valueOfMove
        - whoseTurn
    - Made vectors public
    - Moved player interface and class outside the model directory into its own directory "player"

Changes for Part 3:
    - Updated JavaDocs
    - IBoard
    - Empty enum
    - AI() constructor
    - ModelFeatures & PlayerFeatures interfaces, both inherited by the controller.
    - Adjusted clicks to allow for panels to be moved
    - Fixed mouse and key listeners
    - Start Game

Changes for Part 5:
    - Implemented all 5 extra credit features
        - Hints: Keep track of hints with a boolean flag that determines whether to run
        a display hints method in our view. If true, display hints, if not don't display hints.
        They can be turned on and off while playing for each player with "h" and can also be
        initialized at runtime with "hints" or nothing for no hints.

        - Square Model: Abstracted our board into an IBoard interface. We have two classes for our
        different board types. Then we abstracted our Reversi implementations into an abstract
        class for the functionality that doesn't care about board shape (ie placeToken, etc) and
        then extended that class into two classes, HexReversi for hexagon and SquareReversi.
        An important note is that the abstract class Reversi is of type CellShape, which is
        interface that two classes Hex and Square implement. This allows us to create any shape
        Reversi game of our choosing as the methods in CellShape "set-up" the shape to work in the
        MVC design.

        Square View: Abstracted ReversiGraphicsView that holds methods like render, keyPressed, etc
        and then we have a SquareGUI and HexGUI extend it and provide unique implementations for
        makeShape and xOffSet as that is shape dependent.

        Square Controller: Abstracted our coordinate system into an ICoords interface. We have two
        classes implementing it called HexCoords and SquareCoords. These classes represent our
        coordinate representation for the specific shape game of Reversi: hexagon having q, r coords
        and square having x, y coords. This is very important because it allows our controller to
        work with any coordinate system representation as long it is of type ICoord.

        Square Strategies: Made very minimal changes to our strategies. Simply changed our list
        of coords from our specific HexCoords to our general ICoords so that any coordinate system
        could be used because of the methods ICoords gives us.



Quickstart:
Below are various snippets showcasing our game and how a user may interact with it
    - Start game
    - Place a token
    - Pass a turn

Go to GAMEPLAY.txt in the docs directory to see various different game states and what the view
will look like in each one.

Playing a Game:
To begin playing a game of Reversi, you must first configure the game. Enter a combination of two
of the following keywords:
    - human
    - strategy1
    - strategy2
    - strategy3
    - strategy4
    - hints


The human keyword is to allow for a human player, while strategies 1-4 configure an AI with a
specific move strategy. Please refer to the strategy section below for what each strategy means.
Below are some example VALID command line arguments:
human human
human strategy1
strategy1 strategy2
strategy4 human

The following examples configure various game states, such as a human vs human, a human vs ai, and
an ai vs ai.

Once the game is configured, the corresponding windows will appear for the player(s). To select a
cell, click on an empty cell with your mouse, it should become highlighted in light blue. To place
a token, you must first select a VALID cell (please refer to the game rules for what a valid cell
is) and then press enter. If you wish to pass your turn, simply press p. If you are playing
human vs human, two windows will appear and the players will take turn making moves on their
corresponding window until the game is over.




Key components:

     HexReversi class which implements both the ReversiModel which extends the ReversiROM.
     This class is the model which enforces the rules and the information of our Reversi Game.

     The IPlayer interface which represents the types of players we allow to play the game,
     which we expect to be implemented by a generic player which are the physical users of our
     game, and the AI player that they may play against.

     The HexTextView is the current view that implements the TextView interface and renders the
     board as a String. Black is represented by O's and White as X's.

     ReversiGraphicsView is the new swing view that implements GUI. We plan handle keyboard inputs
     such that...
        - If user selected a cell, they can press enter to place a token
        - If a user presses p, they can pass



Key subcomponents:

     GameColors, which is the enumeration of valid player "colors" (the representation, not the
     physical type). The valid GameColors are Black and White. This is used in the model to
     alternate plays, ensure the Black player goes first, and ensure the correct player
     is playing each turn.

     Hexcoords, which are the coordinate system we use for our hexagonal reversi board. The class
     uses Axial Coordinates and a four sided model is shown in AxialCoords.jpeg, included in our
     documents. The first coordinate is the desired q-position, and the second in the desired
     r-position.

     MyHashMap, is a class that extends HashMap. It overrides one portion of the get method,
     making it so if a requested key is not found, instead of returning null, it throws an
     IndexOutOfBounds exception to signify that your coordinates are off the board, which is
     where the Map is used. This is useful as we are then able to represent empty pieces of our
     board as null.

Code Navigation:
Our codebase has three directories of note:
    1. docs
    2. src
        a. controller
        b. model
        c. player
        d. strategy
        e. view
    3. test

docs contains this file (README) and various other text files explaining the gameplay flow and
rules. It also has a jpeg of the coordinate system we are using and what it looks like on a
hexagonal board shape.

src contains all our implementations. There are three subdirectories that represent the different
components of the MVC model. Meaning the code for our model component is found in the model
directory, view component in view directory, etc...

test contains all our test cases. TestModel holds all the tests for our model methods.
TestTextView has the tests for the TextualView, etc...

Extra Credit Implementations:
We have implemented various extra credit features. They are listed below along with their file
locations.
    1. Strategies. We implemented all 4 strategies. They can be found within the strategy directory
       in src. Our strategies are set up so that each one returns a list of possible moves given
       that strategy. The list is sorted from top left to bottom right in terms of Coords. Meaning
       the first element in the list is the top leftmost available cell for that strategy. This is
       for two reasons, first, it allows us to break ties if two moves have the "same" value.
       In a case of a tie, we pick the first element of the list of moves, which is the top
       leftmost cell. Second, it allows us to chain strategies together. If we chained strategy 1
       with strategy 2 for example, let's say strategy 1 returns 5 possible moves for the current
       game state using the get highest score. Next we will pass in that list of 5 moves into
       strategy 2 which will "trim" the list into moves that are only highest score AND avoid the
       next corner. Meaning, that by chaining strategies, we pass along all possible moves and trim
       the list given a current strategies requirements.
            a. Strategy 1 = GetHighestScore
            b. Strategy 2 = AvoidNextToCorner
            c. Strategy 3 = GetCorner
            d. Strategy 4 = MinMax
