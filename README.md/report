# Amazons-Game-Project
Introduction
This report covers an artificial intelligence (AI) agent that was designed for COSC 322. The AI was designed to play the Game of Amazons on a ten-by-ten board, using the traditional rule set with 8 queens on the board. The AI was tested offline, then implemented to a remote server’s API to connect with and play opponents online. The purpose of the designed AI is to make the best play on each turn. This is done by examining the board then successively determining possible moves from its game state, analyzing the moves and determining the best move to make. The Game of Amazons has a very large initial branching factor which is computationally extremely demanding, and largely impossible to compute the very best move. Thus, this AI was designed to make the best move that it could find in 30 seconds. Limiting the time that the AI can compute will limit its effectiveness, but make it more manageable to demonstrate and use in real time.
The AI principally works by performing a state-space search on the current board state. The search will find all possible moves from the given board state, then all possible moves from each successive board move state. The AI will continue recursively for a given period of time until it has built a tree composed of the quality of each move. Finally, the AI performs an alpha-beta search through the generated tree to determine the very best move. This report will cover the details on each portion of the AI, as well as possible improvements that could be made to the AI.

# AI Discussion
The AI implemented for this report utilizes the COSC 322 hosting server to update the state of the AI through each move. The AI first stores a blank copy of the board, which will be constant for the start of each game. As moves are made on the online board, the AI will read the moves made, and update the local board to reflect the current state of the online board. There is a translation of coordinates between the server board and the local board, for ease-of-understanding our AI utilizes a local board indexed from zero and mirrored row locations with respect to the server board.
The AI consists of a few crucial functions for it to operate. The primary method that the AI implements is a function to get all of the possible moves for a specific player on the board, called get-moves (see appendix A for pseudocode). The get-move function works by finding each queen position, then loops through to find each potential move for the queen, and each spear throw for each queen move. The get-move function is crucial for checking if the opponent makes a valid move, as well as finding all moves for a given board state which is used in the alpha-beta searching. 
An important feature of the AI is to check for wins, without such it would never know when to stop playing. Traditionally in the Game of Amazons, a player wins when the opponent cannot make a legal move. This is equivalent to an opponent running out of time, or making an invalid move in the online version. Checking if an opponent has timed out is handled by the server but the AI will check each of the opponent’s moves to ensure that they made a valid move. Validating our opponent’s moves is done by first forming a list of all possible moves the opponent could make by using the get-moves function. Then the AI will check if the opponent's move is contained within the set of all possible moves. By keeping track of the time, and validating the opponent’s moves the AI never needs to check if it has won only if the opponent has lost. Under the circumstances, if the opponent makes an invalid move the AI will send a message to the server stating it, which will have to be manually validated by a human.
The next crucial function contained within the AI is a method to score a move to see a move's viability, called score-move (see appendix A for pseudocode). Having an efficient and accurate evaluation function will allow the alpha-beta search algorithm to better prune the tree. The more branches the tree can prune the faster the algorithm will work. Beyond this, having an accurate evaluation function will allow the AI to perform the best move available, hence increasing the chances of winning. The AI implements an evaluation function that scores a move based on the net mobility of the AI. This means for any given move position, it will find all possible moves for itself, then all possible moves for the opponent. With the move list, it will find the number of moves available to each player. The evaluation of a state is the number of moves available to the AI minus the number of moves available to the opponent. This evaluator will lead to the AI grouping queens about the center of the board and avoiding getting trapped. The game is lost when the AI has no more moves available, thus this evaluator will act to preserve the queens as long as possible. The greatest strength of this evaluator is its speed, since it only must make two get-move calls then it is complete. The quickness of the mobility evaluator allows a much larger search tree to be generated than incorporating other methods. The deeper the tree is, the more likely the AI is to find misleading or bad moves.
Incorporating the methods discussed thus far allows for an AI that makes random moves, or the highest scoring move from the current state. In order to improve the AI, it should not only look at each move, but also the consequences of the move. In other words, for each possible move it should see what the best move the opponent can make since this will lead to the largest disadvantage to the enemy AI. This idea of maximizing moves followed by minimizing moves can continue to an arbitrary depth, where a larger depth correlates to a better move. While there are caveats on this broad statement, it can be generally accepted that the deeper the generated move tree the more accurate the final move. Ideally, the AI should run this algorithm to terminal states of the board to determine if a move correlates to a win or a loss. In reality, due to the Game of Amazons’ large branching factor, it is practically impossible to compute the tree to its terminal states in the early-game. This introduces a need to limit the time that the tree of moves is generated. 
The AI handles this challenge by incorporating an iterative-deepening-search (IDS) technique combined with aggressive alpha-beta pruning. Alpha-beta pruning is the key to the AI’s operation and thus will be discussed in great detail later in this paper, this section's scope is limited to the generation of the search tree. IDS search works by iteratively dynamically generating a search tree where each iteration has an incremented depth starting from depth one initially. In reality, IDS does not itself generate a tree but rather is a method to call alpha-beta which will generate a tree. IDS does not have an intuitive time-breaking condition since each tree depth will take an unknown amount of time. To compensate for this, the alpha-beta search function, which generates the tree, will keep track of the time and suddenly break out of the search if the time exceeds a threshold. The IDS will detect this break from the search and return the best move from the most recent complete depth-search. Through trial and error, it was found that breaking from the search tree at 27 seconds left enough time for the rest of the actions to be completed before sending information back to the server. With a 27 second search time, the total time to generate a move and send it to the server was always under 29 seconds, which coincides with the move time limit of 30 seconds.
With the addition of a tree-search method, the AI is equipped to have foresight when making moves. These future considerations greatly improve the AI’s chances of winning. The only aspects of the AI left to discuss are the edge cases that it may only encounter once, or a few times in any given game. Firstly, it should be carefully considered what the AI should do once it runs out of viable moves. The AI is equipped with a random-move generator that, in the case of no valid moves available, will pick a random queen and move it to a random tile, throwing a spear to a random tile. This move is guaranteed to be illegal, however, the onus is on the opponent to check if they won opposed to the AI checking if it lost. This leads to a potential vulnerability of the opponent, wherein if they do not validate the AI’s move it can escape a loss by jumping around the board until the opponent runs out of valid moves.
Finally, once the best move has been returned from the AI, albeit a move from the search tree or a random move it must be sent back to the server. The AI first translates the board coordinates to the server board standard then utilizes server methods to transmit data back.

# Search Algorithm
The AI utilizes the Minimax algorithm with Alpha-Beta pruning and IDS on a tree containing possible moves to search for the move with the highest utility for the current user. We chose the minimax algorithm because it is both simple and effective when paired with Alpha-Beta pruning, which is also fairly easy to understand. We wanted to make a decision concerning our algorithm as soon as possible due to the limited length of the course. The combination of Minimax and Alpha-Beta pruning was one of the first algorithms presented to us; due to this as well as the extensive explanations and examples in both the course content and available online. We decided this algorithm would be the one our group members could both understand and implement most effectively.
In the Minimax algorithm, the total utility is divided between both players, and the maximizer works to get the highest score for the player while the minimizer finds the lowest score for the opposing player. Amazon’s Chess is a zero-sum game, therefore, raising one player's score lowers the other and vice-a-versa. To improve the efficiency, the algorithm uses iterative-deepening, which results in a shallow depth in the early game when the branching factor is extremely large. But it allows for the AI to search extremely deeply in the late game when the branching factor of the tree is reduced because of the limited number of moves. To prevent the AI from going over time the search algorithm has a lifetime which when expired terminates the search returns the best move found in the elapsed time.
Alpha-Beta Pruning’s main goal is to reduce the number of nodes generated in a search tree. This method achieves this by skipping branches when a possibility has been found that is worse than a previously examined state/node. The Maximizer updates the value of Alpha while the Minimizer updates the value of Beta. Then the node values will be passed to parent node’s instances of Alpha and Beta while backtracking. The condition deciding if a node is less valuable than the previously evaluated nodes is if Alpha is greater than or equal to Beta, in which case that node and its subsequent children are skipped, improving efficiency.
Our implementation of the Minimax algorithm with Alpha-Beta and pruning iterative-deepening can be seen within the alphaBeta method of the AI (see appendix A). After the AI object is initialized, this method is called and the root node corresponding to the current board state of the tree is passed alongside the default smallest integer and largest integer values for alpha and beta respectively. First, the node is checked for being at the desired depth or a terminal node. If this is not the case then the maximizer or minimizer take their turn depending on the current depth where odd depths are minimized and even depths are maximized. The maximizer finds the optimal value for the AI from the node’s children and assigns Alpha. Then the pruning condition is checked (if Beta <= Alpha) and if true the branch is pruned, this saves time that would otherwise be wasted searching a branch already known to be suboptimal. The minimizer takes its turn to find the worst values for the opponent and updates Beta accordingly. The Alpha-Beta pruning condition is checked here as well and the branch is pruned if so. Both the maximizer and minimizer return the list of optimal moves in the form of an integer array.


# Development Issues and Barriers
The first and most challenging issue in the development of this project was the development time. With only a handful of weeks from inception to the tournament, the prototyping and iteration had to be fast yet thoughtful. The fast-paced development led to an implementation focused design process, wherein the most appropriate approach was to “give it a go” and learn from mistakes. This resulted in large-scale redesigns of the software as time went on and performance bottlenecks were found. While this resulted in some inefficiency in time usage, it was extremely valuable from a learning perspective as the team was able to see how changing implementations improved performance.
The next major issue that was faced was in the implementation of alpha-beta pruning. While minimax is a fairly simple algorithm, and alpha-beta pruning a simple extension of it, the best method of integrating the algorithm into the structure of the program was the subject of many hours of discussion. To overcome this challenge the team decided to search outside of the course material for various pseudocode examples of alpha-beta pruning to find a style that fit well with the implemented AI. Initially, we solved the problem by creating a tree of nodes using breadth-first-search (BFS), then applying alpha-beta to the pre-generated tree. We quickly found that the true advantage of alpha-beta lies in its ability to ignore branches altogether, thus never visiting branches of nodes and saving much more time. We quickly pivoted to a dynamically generated approach, where a node is only visited if the alpha-beta search deems it necessary. The next major discovery was the misstep of using BFS to generate the nodes, this resulted in nodes having a partial list of children being generated. The partial list would often be misleading since all state possibilities were not being examined, and the AI would choose an incorrect move. This led to the final overhaul of the AI, implementing the alpha-beta pruning using an IDS approach. IDS techniques allow for constant depth trees through any branch which improves accuracy greatly. 
Once alpha-beta pruning was implemented the AI still appeared to be operating extremely slowly, barely completing a search at depth one. Confused, the team spent many hours reviewing the code and testing small changes to its operation. Ultimately major efficiency gains were had by tightening up the variable usage and reusing variables across classes and functions instead of copying them for each iteration.
Another challenge the team faced when integrating the AI with the online API was the conversion of variables between the board API and the AI’s internal board variable. The challenge was a result of a seemingly-arbitrary board indexing. The server’s board had flipped row coordinates, and the row and column on the server began from one instead of zero (Java standard). While coordinating the coordinates seems like a straightforward task, in practice it consumes a lot of time. As a result, our team spent many hours debugging and troubleshooting the coordinate system conversion to ensure that there was never a mismatch between the local and true board state (server board).

# Tournament Results

Unfortunately, through all of the hard work, our AI performed poorly. We placed 4th in the final standings. While our AI played great against us humans, it was severely outmatched against the opponent’s sophisticated evaluators, and efficient Monte Carlo AI’s.
Our team spent so much time at the beginning of the project, that we could not justify the excess time required later during the tournament to completely redo our AI to compete with the best AI’s. During the preliminary rounds, our AI versed two teams and beat them both handily. We were given the false impression of a powerful AI. As soon as we faced the same teams in the round robin, we were easily defeated and our AI looked juvenile in comparison. 
Our AI is well thought out, and extremely efficient; but given proper time, we would regroup and attempt a Monte Carlo simulation based AI. Woefully, the tournament has passed us and the class is ending and alas we are stuck with a mortal alpha-beta AI.

# Future Improvements
As evident from the tournament results, our AI requires a large overhaul in order to compete with a strong AI. While a time crunch did not permit these changes from being made, it serves as guidance for those reading this when designing their own AI.
The quickest and easiest improvement to make upon the current AI is to improve the position evaluator function. The current evaluator is trivial and has a large room for improvement. Later in the tournament, we realized that there was not enough time to overhaul the whole AI and we made drastic changes to it. We changed the evaluator from a mobility evaluator to a territory and mobility evaluator. The new evaluator would search the board to see which tiles our AI had control over by seeing the minimum number of moves to reach the tile, if our AI can reach it in fewer moves than the opponent we controlled it. Then with these “winning” tiles, the AI would find all the queen's mobility from the tile. Finally, it would sum the score of each tile together and return that as the state score. This evaluator was rushed and thus had flaws but it illustrates an important evaluator consideration: the control over board segments. The mobility evaluator will tend to group the AI’s pieces around the center of the board where it has the most moves available. The pitfall of this is that the smart opponents will wall off the edges which tend to trap our AI in the center of the board. By introducing territorial advantage to the evaluator the AI would move into the edges with the opponent in order to maintain a similar territorial advantage. By pairing a white queen with a black queen the AI will tend to lose less often, since the opponent will not be able to wall itself off into a large segment. Future position evaluators should strike a balance between territory and mobility; testing found that mobility was favoured in the early game, while territory was in the late game. 
The largest improvement to make to this AI is to scrap everything and move to a Monte Carlo simulation approach. Monte Carlo handily dominated our AI in every single match. Monte Carlo’s ability to simply try every move until it finds the best will likely beat any alpha-beta no matter how clever the evaluator is. Monte Carlo simulations involve picking a move from the state, then playing a random game out, assigning a winning percentage to each move. The winning percentage will update based on each game played through that move. The AI will then pick the best move based on the highest winning percentage using the move. The detailed implementation of Monte Carlo simulations is beyond the scope of this report, but serves as guidance to those building a Game of Amazons AI: use Monte Carlo simulations to win.
Finally, to implement the best possible AI using non-super computers a combination between alpha-beta and Monte Carlo must be used. Monte Carlo is fantastic during the late game where the branching factor is small and it can simply find the winning moves, however, early on there are too many moves to test each move enough. This means that during the beginning of a match, the Monte Carlo AI will essentially be picking random moves, since each move does not have enough exploration. By implementing an AI that used alpha-beta pruning for the early game, then switching to Monte Carlo simulations for the late game a superb AI can be formed. The early game alpha-beta pruning would allow for the AI to be put in a mediocre position, but the late game Monte Carlo would be able to dominate. Being that Monte Carlo dominates with a random start, it surely would win with an adequate early game.

# Conclusion
The AI developed through COSC 322 and discussed in this report represents a reflection between class knowledge and real-world implementation. The AI makes use of clever searching techniques paired with condensed and thoughtful code. 
Through each step of the design process, following drastic changes to the AI, we would run a test game between the old and new AI. It was astounding to see the improvements the AI can make with more thought and meaning put behind the code.
While future improvements are ripe and plentiful, the current state of the AI performed woefully. With more time, the AI has potential to transform into an extremely threatening bot though. All in all, this class project was a fantastic introduction to artificial intelligence; the experience gained from designing and testing your own AI greatly outweighs what could ever be taught in the classroom.

# Appendix A
Game of Amazon’s AI Pseudocode
MainLoop (username password)
{
	Initialize game client
	Initialize game GUI
	Initialize board_state

	Login to game room
	
	HandleGameMessage (messageType, messageDetails)
	{
		Switch (messageType)
		{
		Case Move:
			AI := RecursiveAI (board_state, player)
			
			opponentMove := messageDetails.opponentMove
			moveIsValid := AI.WasValidMove(board_state, opponent, opponent_move)
			
			If (moveIsValid)
			{
				Update board_state
				
				best_move := AI.iterativeDeepeningSearch ()
				
				If (Valid move exists)
				{
					Make best_move
				}
				Else
				{
					Game has been lost, start making illegal moves
				}
			}
			Else
			{
				Send to server: opponent made invalid move
			}
			
		Case Game_Start:
			player := assign player
			opponent := assign opponent

			If (Playing First)
			{
				Make first move
			}
		Case Board_State:
			Update board_state
			Update GUI
		}
	}
	
}


RecursiveAI (Board_State, Player)
{
	ScoreMove (board_state, player)
	{
		score := possible_moves - possible_opponent_moves
		return score
	}
	
	GetMoves (board_state, player)
	{
		listOfQueens := Queens on board
		
		For Each queen In listOfQueens
		{
			listOfQueenMoves[queen] = list of valid moves for queen
		}
		
		return listOfQuuenMoves
	}
	
	WasValidMove (board_state, opponent, opponent_move)
	{
		valid := check ruleset against move
		return valid
	}
	
	IterativeDeepeningSearch (player, opponent)
	{
		alpha := -Inf
		beta := Inf
		depth := 1
		timer := start timer

		While (timer < timeout && depth < maxDepth)
		{
			current_move := AlphaBetaPruning (board_state, alpha, beta, player, opponent)
			maxDepth++
		}
	}
	
	AlphaBetaPruning (board_state, alpha, beta, player, opponent)
	{
		max := -Inf
		min := Inf

		If  (this.timer >= timeout || this.depth > maxDepth)
		{
			return this.currentMove
		}
		
		If  (player)
		{
			For Each move In board_state.GetMoves (board_state, player)
			{
				newBoard := board_state
				newBoard.Move(move)
				
				currentMove = AlphaBetaPruning (newBoard, alpha, beta, player, opponent)
				
				If (currentMove.value == -Inf)
				{
					return currentMove
				}
				
				If (currentMove.value > max)
				{
					max = currentMove.value
				}
				
				If (max > alpha)
				{
					alpha = max
				}
				
				If (beta <= alpha)
				{
					break
				}
			}
			
			return max
		}
		Else-If (opponent)
		{
			For Each move In board_state.GetMoves (board_state, player)
			{
				newBoard := board_state
				newBoard.Move(move)
				
				currentMove = AlphaBetaPruning (newBoard, alpha, beta, player, opponent)
				
				If (currentMove.value == -Inf)
				{
					return currentMove
				}
				
				If (currentMove.value < min)
				{
					min = currentMove.value
				}
				
				If (min < beta)
				{
					beta = min
				}
				
				If (beta > alpha)
				{
					break
				}
			}

		}
		
		return min
	}
}


# Group members
Michael Ogden 

Jacob Vanzella 

Jared Paull 

Sirus Wang 

Griffin LaFreniere 
