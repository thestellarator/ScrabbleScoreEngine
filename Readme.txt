The score engine works with two-four players but is designed to work with two players (certain functions don't work with three and four players.) Each turn determines the value of a word and all attached words. 

For example if your Scrabble board has A, B, C... along the x-axis and 1, 2, 3 along the y-axis (the default setting of the engine) then an example of the first word is:

ScoreData score = ScoreData.fromSingleString("OILED, F8, H");

OILED is the word that is placed on the board, F8 is the coordinate of the and H is the direction of
the word on the board (horizontal). The commas are optional and the order the values are entered
are optional:

ScoreData score = ScoreData.fromSingleString("F8 OILED H");
System.out.println(score.toString());

run:
OILED, F8, H

You can enter the ScoreData object into various different classes to get the score. The main main score processing class is Scoring. Scoring requires and instance of Tiles which represents the language the game (currently I have just entered English, but if you want to expand the engine to further languages is should be fairly easy.)

ScoreData score = ScoreData.fromSingleString("OILED, F8, H");
Scoring scoring = new Scoring(new English());
int scoreValue = scoring.getMultipleScores(score);
String data = scoring.getScoreData(score);
        
System.out.println("Score: " + scoreValue);
System.out.println(data);

Score: 12
OILED, F8, H 12
Total score: 12

As you can see the score is 12 here, and there are no attached words. The engine scans alongside the
word to detect if this word creates any new words when placed. The ScrabbleBoard is a singleton to
make adding words easier. This is how the previous word would be added:

ScrabbleBoard board = ScrabbleBoard.getInstance();
board.addWord(score);

Here is an example of a score that consists of new words (this requires the previous word to be added to the board.)
     
 ScoreData score2 = ScoreData.fromSingleString("MOO, I9, H");
 Scoring scoring = new Scoring(new English());
 String data = scoring.getScoreData(score2);        
 System.out.println(data);

MOO, I9, H 8
EM, I8, V 7
DO, J8, V 3
Total score: 18

This word consists of MOO (8), EM (7) and DO (3) which is worth a total of 18 points. 

You probably won't need to use this class though since there is a class built in that adds the scores up for 2, 3 and 4 players and writes the score out for 2 players and even determines which tiles are left at the end and automatically adds and subtracts them.

The MultiPlayer class again requires the use of a Tile object to identify which language you are using and also requires the names of the players. It can be instantiated 1 of 4 ways:

MultiPlayer mp = new MultiPlayer(new English(), "Player 1", "Player 2");
MultiPlayer mp2 = MultiPlayer.createTwoPlayer("Player 1", "Player 2", new English());        
MultiPlayer mp3 = MultiPlayer.createThreePlayer("Player 1", "Player 2", "Player 3", new English());        
MultiPlayer mp4 = MultiPlayer.createFourPlayer("Player 1", "Player 2", "Player 3", "Player 4", new English());

You create ScoreData objects like before and enter them into the MultiPlayer object. When you have entered the correct word you manually switch turns. You look up the scores by calling the player by number:

MultiPlayer mp = new MultiPlayer(new English(), "Player 1", "Player 2");
ScoreData sd = ScoreData.fromSingleString("OILED, F8, H");
mp.setScore(sd);
mp.nextTurn();
System.out.println(mp.getPlayerScore(1));

run:
12

The TwoPlayerScoringEngine writes the scores data and score down in a form that can be recovered at the end. Here's an example of a Scrabble game created by the class:

OILED, F8, H (12)                  |   WILLER, G7, V (14)
MOO, I9, H (30)                    |   --- (0)
--- (0)                            |   TREADING, F12, H (86)
HIT, K11, V (42)                   |   RACES, E4, V (107)
LOG, M10, V (46)                   |   ARGUING, C3, H (190)
TOASTI??, C1, V (102)              |   --- (0)
LIE, M10, H (107)                  |   FRENZIED, O8, V (519)
JAG, A8, H (134)                   |   --- (0)
VANE, H1, V (158)                  |   WE, G7, H (526)
TAX, I11, V (176)                  |   PARTIED, L3, V (605)
BI, M2, V (192)                    |   PICK, L3, H (617)
YE, N14, H (202)                   |   AN, M14, V (625)
TRY, F12, V (216)                  |   ASK, O1, V (646)
FAB, K4, H (231)                   |   HATE, A1, H (670)
HU, A1, V (236)                    |   POOR, C13, H (682)
QUEEN, I15, H (251)                |   TUM, L6, H (693)
POW, C13, V (259)                  |   NON, D12, V (701)
-ISV 253                           |   +ISV 707 

The Debug class shows an example of the TwoPlayerScoringEngine in action (it created this game).