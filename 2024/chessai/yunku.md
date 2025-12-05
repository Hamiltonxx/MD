# 云库web
chessdb.cn  
rmooo.com/yunku/  
qqzze.com/yunku/

# 云库API
http://www.qqzze.com/yunku/api.html  

## 接口访问格式
http://api.chessdb.cn:81/chessdb.php?action=ACTION{&OPTION1=VALUE1&OPTIONn=VALUEn}  

ACTION: queryall, query/querybest, queue  
OPTION: board等

## 查询所有着法queryall
必须参数: board, 为FEN格式局面代码, 例 board=rnbakabnr/9/1c5c1/p1p1p1p1p/9/9/P1P1P1P1P/1C5C1/9/RNBAKABNR w  
可选参数: egtbmetric, 设置为残局库类型, 例 egtbmetric=dtc 或 egtbmetric=dtm, 默认为dtm  
返回结果: 以|分隔的着法信息, 每项包含以,号分隔的着法(move)、分值(score)、排序(rank)、胜率(winrate)、及备注(note)  
若局面代码错误,返回 invalid board, 若查询的局面没有已知着法, 返回unknown, 若走棋方被将死或困毙, 返回checkmate/stalemate

## 查询最佳、随机或候选着法(querybest/query/querysearch)
必须参数: board, 为FEN格式局面代码, 例 board=rnbakabnr/9/1c5c1/p1p1p1p1p/9/9/P1P1P1P1P/1C5C1/9/RNBAKABNR w
可选参数: ban, 设置为禁止着法列表，以|隔开, 例 ban=move:c3c4|move:b2e2|move:c0e2, 默认为空  
可选参数: endgame, 设置是否只返回残局库数据, 默认为0

# FEN
FEN局面表示法，即福斯爱德华德表示法(Forsyth-Edwards Notation), 是专门用来表示象棋局面的记录规范, 一个局面可以简单地用一行FEN字符串表示。  
FEN局面表示法来源于国际象棋, 在中国象棋中做了些许变通。  
```
rnbakabnr/9/1c5c1/p1p1p1p1p/9/9P1P1P1P1P/1C5C1/9/RNBAKABNR w - - 0 1
```
上边的串符合FEN的6段表示规则，各段用空格分隔:  
1. 中国象棋有10行，用9个/分隔出来。  
2. 棋子用字母表示:  
    R/r: 车 Rook  
    N/r: 马 Knight  
    B/b: 相 Bishop  
    A/a: 士 Advisor  
    K/k: 帅 King  
    C/c: 炮 Cannon  
    P/p: 兵 Pawn  
3. 第2段w表示轮到红方。 b则表示黑方。 国际象棋是白方(white)和黑方(black)。  
4. 第3段和第4段用 - 表示跳过。 在国际象棋里是"王车易位"和"吃过路兵"。  
5. 第5段表示无吃子步数。 120判和，即六十回合无吃子判和。  
6. 第6段表示总回合数。 两步为一回合。  

## FEN文件
FEN文件是象棋局面传递的一个有效方式: 将代表当前局面的FEN串以文本文件的方式保存，通常后缀为.FEN, 这样就形成了一个FEN文件。  

# UCI
The Universal Chess Interface (UCI) is a standard text-based protocol used to communicate with a xiangqi engine and is the recommended way to do so for typical graphical user interfaces (GUI) or chess tools.   

Developers can see the default values for the UCI options available in Pikafish by typing ./pikafish uci in a terminal, but most users should typically use a xiangqi GUI to interact with Pikafish.  
## Standard commands
### quit 
Quit the program.  
### uci  
Tell the engine to use the UCI. 
This will be sent once, by a GUI, as a first command after the program boots to tell the engine to switch to UCI mode.  
After receiving the uci command the engine will identify itself with the id command and send the option commands to tell the GUI witch engine settings the engine supports.  
After that, the engine will send uciok to acknowledge the UCI mode.  
### setoption
Usage: setoption name xxx value yyy  
This is sent to the engine when the user wants to change the internal parameters of the engine. For the button type no value is needed.  
### position
Usage: position fenstring | startpos moves  move1 ... movei  
Set up the position described in fenstring.  
If the game was played from the start position the string startpos must be sent.  
Examples:  
```
> position startpos
> position fen rnbakabnr/9/1c5c1/p1p1p1p1p/9/9/P1P1P1P1P/1C5C1/9/RNBAKABNR w - - 0 1
> position fen rnbakabnr/9/1c5c1/p1p1p1p1p/9/9/P1P1P1P1P/1C5C1/9/RNBAKABNR w - - 0 1 moves h2e2 h9g7 h0g2 g6g5
```
### ucinewgame
This is sent to the engine when the next search(started with position and go) will be from a different game. This can be a new game the engine should play or a new game it should analyze but also the next position from a test suite with positions only.  

If the GUI hasn't sent a ucinewgame before the first position command, the engine won't expect any further ucinewgame commands as the GUI is probably not supporting the ucinewgame command.  

So the engine will not rely on this command even though all new GUIs should support it.  
### isready
This is used to synchronize the engine with the GUI.  
When the GUI has sent a command or multiple commands that can take some time to complete, this command can be used to wait for the engine to be ready again or to ping the engine to find out if it is still alive.  
### go
Start calculating on the current position set up with the position command.  
There are a number of parameters that can follow this command and all will be sent in the same string.  
If no parameter is sent, then go depth 245 will be executed.  
Examples:  
```
> position startpos
> go infinite
> stop
```
```
> position startpos
> go depth 25
info string NNUE evaluation using pikafish.nnue enabled
info depth 1 seldepth 1 multipv 1 score cp 8 nodes 74 nps 37000 hashfull 0 tbhits 0 time 2 pv b2e2
...
info depth 25 seldepth 26 multipv 1 score cp 20 nodes 2694626 nps 959283 hashfull 841 tbhits 0 time 2809 pv b2e2 b9c7 b0c2 a9b9 a0b0 h9g7 g3g4 c6c5 h0g2 i9i8 h2i2 h7h3 i0h0 h3g3 b0b4 i8f8 e3e4 c9e7 c3c4 c5c4 b4c4 c7d5
bestmove b2e2 ponder b9c7
```
```
> position fen 3k5/9/4P4/9/9/9/9/9/9/4K4 w
> go mate 1
info string NNUE evaluation using pikafish.nnue enabled
info depth 1 seldepth 1 multipv 1 score cp 158 nodes 5 nps 2500 hashfull 0 tbhits 0 time 2 pv e7d7
info depth 5 seldepth 2 multipv 1 score mate 1 nodes 34 nps 11333 hashfull 0 tbhits 0 time 3 pv e7d7
bestmove e7d7
```
```
> setoption name MultiPV value 2
> position startpos
> go depth 5
info string NNUE evaluation using pikafish.nnue enabled
info depth 1 seldepth 1 multipv 1 score cp 20 nodes 101 nps 50500 hashfull 0 tbhits 0 time 2 pv b2e2
info depth 1 seldepth 1 multipv 2 score cp 8 nodes 101 nps 50500 hashfull 0 tbhits 0 time 2 pv h2e2
info depth 5 seldepth 5 multipv 1 score cp 20 nodes 623 nps 207666 hashfull 0 tbhits 0 time 3 pv b2e2 b9c7 b0c2 a9b9 a0b0
info depth 5 seldepth 5 multipv 2 score cp 17 nodes 623 nps 207666 hashfull 0 tbhits 0 time 3 pv h2e2 h9g7 h0g2 i9h9 i0h0
bestmove b2e2 ponder b9c7
```
```
> setoption name UCI_ShowWDL value true
> position startpos
> go depth 5
bestmove b2e2
```
### stop
Stop calculating as soon as possible
### ponderhit
The user has played the expected move.  
This will be sent if the engine was told to ponder on the same move the user has played.  
The engine will continue searching but switch from pondering to normal search.  
Examples:  
```
> setoption name Ponder value true
> position startpos moves h2e2
> go movetime 1000
bestmove h9g7 ponder h0g2
```
```
> position startpos moves h2e2 h9g7 h0g2
> go ponder movetime 1000
info depth 22 seldepth 34 multipv 1 score cp -24 nodes 1487777 nps 985935 hashfull 588 tbhits 0 time 1509 pv i9h9 i0h0 b9c7 c3c4 g6g5 h0h6 g7f5 b0c2 g9e7 h6h4 b7b3 e3e4 f5g3 g2e3 h7h5 e4e5 e6e5 a0a1 b3c3 e3d5 a9b9 d5c3 b9b3

> ponderhit
info depth 23 seldepth 27 multipv 1 score cp -23 nodes 1632161 nps 988589 hashfull 624 tbhits 0 time 1651 pv i9h9 i0h0 b9c7 c3c4 g6g5 h0h6 g7f5 b0c2 g9e7 h6h4 b7b3 e3e4 f5g3 a3a4 b3c3 e4e5 e6e5 a0a3 c3c0 d0e1
bestmove i9h9 ponder i0h0

> position startpos moves h2e2 h9g7 h0g2 i9h9 i0h0
> go ponder movetime 1000
```

## Non-standard commands
### bench
This runs a standard search benchmark on a pre-selected assortment of positions. It prints the total combined nodes searched, as well as time taken. This command serves two primary purposes:  
1. it can be used as a basic nodes-per-second speed benchmark.  
2. the total number of nodes searched can be used as a "signature" or "fingerprint" of the exact search algorithm version in the binary.  

### d
Display the current position, with ASCII art and FEN.  

### eval
Display the static evaluation of the current position.  

### compiler
Give information about the compiler and environment used for building a binary.  

### export_net [filename]
Exports the currently loaded network to a file.  

### flip
Flips the side to move.  