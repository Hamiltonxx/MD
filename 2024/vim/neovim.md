# Basic Editing
h(left) j(down) k(up) l(right)  
:q! to exit and trash all changes.  
:wq to exit and save the changes. 
i insert before the cursor.  
A append after line.

# Deleting
x delete character.  
dw delete until the start of the next word.  
d$ delete to the end of the line.  
de delete to the end of the current word.
dd delete a whole line (store it in a NeoVim register).  
:%d 删除所有行

# Count For a Motion
2w move the cursor two words forward.  
3e move the cursor to the end of the third word forward.  
0 move to the start of the line.  
d4w delete 4 words.  

# Undo
u undo previous actions.  
U undo all the changes on a line.  
Ctrl+r undo the undos.  

# Put
p put text after the cursor.(if a line was deleted, put the line below the cursor)  
r replace the character.  
ce change word.
c$ change to the end of a line. (input mode)  

# Cursor Location and File Status
Ctrl+g displays your location and the file status.  
G moves to the end of the file.  
number G moves to that line number.  
gg moves to the first line.  
/ searches FORWARD.  
? searches BACKWARD.  
n find the next occurrence.  
N search in the opposite direction.  
Ctrl+o back to order positions.  
Ctrl+i to newer positions.  
% go to parentheses match ()[]{}  
: s/old/new to substitute new for the first old in a line.  
: s/old/new/g to substitute new for all olds on a line.  
:#,#s/old/new/g to substitute between two lines.  
:%s/old/new/g to substitute all occurrences in the file.  
:%s/old/new/gc to ask for confirmation each time.  

:10 goto the line 10.

# Execute an External Command
type : to set the cursor at the bottom of the screen. enter a command-line command.  
type ! to execute any external shell command.  

# COMMAND
o open up a line BELOW the cursor, insert mode.  
O open up a line ABOVE the cursor.  
a insert text AFTER the cursor.  
a,i,A go to the same Insert mode, but where.  
R replace mode.  
e moves to the end of a word.  
y copies text, p pastes it.

# Getting Help
:help
Ctrl+w Ctrl+w to jump from one window to another.  
:q to close the help window.  

# Copy
method 1: v + y + p 可视行模式   
method 2: :.,.+4y 向下4行共5行  
method 3: :5,10y 使用行号复制  
method 4: 5yy 复制5行
method 5: ggVGy 全选复制
method 6: %y 复制文件内容。 同样%d 删除文件内容

# Move
:m -2 向上移动
:m +1 向下移动
:m 10 移动到特定行
:.,.+3m 10 当前行及下面的3行，到第10行
:3,6m 10 第3到第6行，到第10行