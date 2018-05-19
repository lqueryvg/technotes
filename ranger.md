# ranger

```
:terminal   open xterm in current dir
om          sort by modified time
E           edit
enter       open
:           open console (TAB completion works)
:t          open xterm in current dir
dd          cut
yy          paste
:delete     delete selected
A           rename file
f           find with part of filename
!           :shell
@           :shell %s    (cursor before %s)
r           :open with
cd          :cd
[ ]         move in parent (saves going up and back down)
a           rename append
:linemode   change filename display
W           log window
w           task window
mX          make bookmark
`X          go to bookmark

ranger --copy-config=all        # copy config files to ~/.config/ranger
```

## Tabs
```
gn      new
^i      next tab
gc      close
```

## Rifle
```
ext md,  label open, flag f         = grip "$@"         # fork in background
mime ^text,  label editor, flag t = $EDITOR -- "$@"     # open vim in new xterm

:eval fm.rifle.reload_config()      # reload after rifle.conf changes
```
Labels 

## Colours
- Copy solarized.py to colorschemes/.  
- In rc.conf add: `colorscheme=solarized`

