# LudoGame

1.- Introduction

This program is a Java implementation of the popular board game “Ludo”.
The program starts with 3 successive selector screens that allow the user to choose a theme for the game, the number of players and their colors, and the type of board (regular or special).
Next, a Ludo board is generated according the user’s settings and the game starts with the yellow player (if active) or the next active player following a clockwise order.


2.- Implementation

This application comprises 13 classes, which can be arranged by functionality in 5 different groups:
- Control/support (Main, ImgPath, GameState),
- Game elements (Player, Token, Board, Dice),
- Graphic interface (Selector, LudoGUI),
- Artificial intelligence (Autoplay, QLearning) and
- Testing (Test, TestMain).

a.- Setting up GameState
- The class Main initializes an instance of the class Selector which is a customized dialog box that allows the user to input the initial game settings (theme, players, colors, type of board).
- Then, an instance of GameState is generated using the output of Selector. The constructor for the GameState class takes as arguments the aforementioned game settings, or alternatively, if the constructor is called without arguments, the instance of the class will be initialized with the “default settings” stored as constants in the class.
- Finally, the static method drawGUI takes the instance of GameState as argument to create the graphic interface of the game.

b.- Initializing graphics
- drawGUI creates the instance of JDialog that will act as container of the LudoGUI class. It also generates and sets up the JMenuBar that will allow the user to modify the game settings during a round of ludo.
- Additionally, the following key combinations are set up to work with the graphic interface:

CTRL+D : allows the user to input the dice result instead of using a random value.

CTRL+P: shows a Selector dialog box to modify the number and colors of the players participating in the round of ludo.

CTRL+B: shows a Selector dialog box to modify the type of Board (regular or special).

CTRL+T: shows a Selector dialog box to modify the Theme (plain, solid, fruits).


c.- Game start and execution
- The autoplayer timer is started by the GUI constructor. The timer delay is stored as a constant and set at 1000 ms. The first autoplayer timer event will trigger the autoPlay method, that checks if the game is going on (not finished) and stops the autoplayer timer temporarily.
- If the player who has the first turn is set to “auto”, the dice is rolled and the timer for the dice animation is started.
- If the player is set to manual, a mouse click will trigger the dice roll and start the timer for the dice animation.
- After the dice roll, the timer for the animation is stopped and the one for the autoplayer is started again.
- If the player is set to manual, a click of the mouse at the position of the selected token will update the token to its new position, check for special tiles and other tokens, and pass the turn to the next player.
- If the player is set to auto, the Autoplayer class will calculate the best possible move according to the AutoplayMode set up for the player (3 possibilities: custom IA, QLearning and Random).
- Every move is finalized by checking if the token arrived at its GOAL position. If so, the goal counter increases and the token is set as inactive (attribute out set to false and attribute finalTrack set to true).
- When the 4 tokens of a player arrive at the goal position, the player is marked as inactive and added to the list of winners.
- When all the active players have their 4 tokens in the goal position, a dialog window shows the game results on screen.
- The game is terminated after the user’s confirmation (click on the dialog window OK button).

d. Autoplayer

This implementation includes a controller for the computer-operated player. This controller decides the player’s next move according to the AutoplayMode settings.

There are 3 possible AutoplayModes:
- CustomAI: Selects the next move by calculating the reward of all possible moves using a reward hash table.
- QLearning: Selects the next move by reading the reward value from a table generated by a QLearning algorithm.
- Random: Selects the next move by choosing randomly from all possible moves.

The next-move decision is executed by the method selectToken, which takes as argument the current value of the instance of the class GameState.

If the AutoplayMode is set to customAI, the token to be moved is selected using the maxRewardToken method. The customAI mode verifies a certain condition or “rule” with if statements, and reads the respective reward from a hash table. Then, it adds up the reward from each fulfilled condition and selects the token with the highest total reward.

The QLearning mode uses the maxQToken method to select the next move. This method reads the reward of each of the possible moves from a QTable, and selects the token with the highest reward.

This QTable is generated from the QLearning class, by simulating 100000000 games of ludo and updating the reward of each board tile with each iteration according to a given QLearning rule.

The current implementation considers a special board (with special tiles) to generate the QTable. This QTable is created beforehand and stored in a text file so that it can be accessed by the controller during a round of ludo.

Finally, the random mode uses the randomToken method to decide the player’s next move. The method checks all the possible moves and selects one of them randomly.
