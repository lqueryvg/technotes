# Tmux - terminal multiplexer

To get latest version (2.3 does not support double-click):
```
brew install --HEAD tmux
```

Keyboard Layout http://www.keyboard-layout-editor.com/#/layouts/9e83433fb7ce7e809513ef406bb45c4a

```
tmux list-commands  # very useful !
tmux list-keys      # bindings

^b  # default escape prefix 
    # (but I prefer Caps Lock)
    # followed by:

set -g mouse-select-pane on     # set an option
set -g mouse-select-pane        # specify no value and it toggles !!!
```

## Windows
```
     c   new
  b n    back / next
   0-9   select by num
     '   prompt select by index
     &   prompt kill win
     ,   prompt rename win
     s   prompt select by tree
     f   prompt find by name
     w   prompt select from list
prefix   prev selected
```

## Panes
```
select   size-1    size-5
   k       ^k         M-k   
 h  l    ^h  ^l    M-h   M-l
   j       ^j         M-j   

| -        vert / horiz split
o          next clockwise
!          break pane (promote to new win)
:join-pane -s :2.0   # join first pane of win 2 in first sess to this

;          prev selected
x          prompt kill
z          zoom (temporary)
{ }        swap with prev / next
Space      next layout
^o         rotate contents clockwise
q          display ids
t          clock-mode
```

## Sessions
```
 d   detach
^z   suspend tmux
 (   switch-client -p
 )   switch-client -n
 $   prompt rename sess
 .   prompt move win to another tmux client
 D   choose-client
```
## Copy/Paste
```
[          start copy mode
j / k      down up line
^f ^b      down up a lot
    q      quit
v / Space  start selection
y / Enter  copy selection
]   paste

# list-buffers
- delete-buffer
= choose-buffer

^               Back to indentation       
G               Bottom of history         
Escape          Clear selection           
y Enter         Copy selection            
hjkl            Move
LMH             bottom middle top screen line
d               Delete entire line        
D               Delete/Copy to end of line
$               End of line               
:               Go to line                
^d ^u         Half page down / up
f t F T         find / to forward / back
; ,             repeat ft / FT
^f ^b           Next Prev page                 
w e W E         words
o               Other end of selection    
p               Paste buffer              
b               Previous word             
B               Previous space            
q               Quit mode                 
v               Rectangle toggle          
C-Down or C-e   Scroll down               
C-Up or C-y     Scroll up                 
n               Search again              
N               Search again in reverse   
?               Search backward           
/               Search forward            
0               Start of line             
Space           Start selection           
g               Top of history            
                Transpose characters      
```

## Misc

```
:   command-prompt
i   info
?   list-keys
r   refresh-client
~   show-messages
```

## Others

```
PPage copy-mode -u
M-1   select-layout even-horizontal
M-2   select-layout even-vertical
M-3   select-layout main-horizontal
M-4   select-layout main-vertical
M-5   select-layout tiled
M-n   next-window -a
M-o   rotate-window -D
M-p   previous-window -a
```
