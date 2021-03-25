# VIM

## Misc
```
 vim -u NONE     # skip all configuration (for testing bare vim bugs)
 `.              # go to last change position
 '.              # go to start of line with last change
 ^a / ^x         # add / subtract from num under cursor
 ^o              # back to previous jump location (in stack)
 gf              # open file under cursor (in this win)
 * #             # find word under text forwards / back
                 # or visual sel with nelstrom/vim-visual-star-search
 :noh            # clear search highlights
 :e .            # Netrw file browser
 :e!             # Revert file to original state
 :@"             # execute yanked text as : command
 @q              # execute contents of register q
 @@              # repeat last @ command
 @:              # execute last : command (: register is last : command)
 :cd %:h<TAB>    # cd to path of active buffer file
 :ta {tag}       # jump to tag
 :echo $VIM      # show vim config base location

 C-f             # toggle places to search in CTRL-P (buffers, files, mru files)
 C-a             # increment number under cursor

 gg=G            # autoformat file
 :w !diff % -    # view diffs made to current buffer

 :%!xxd          # convert buffer to hex
 :%!xxd -r       # convert hex buffer back to ascii after editing

 # screen positioning
 gg              # go to line 1
 zz              # current line to centre of screen
 zt              # current line to "t"op of screen
 zb              # current line to "b"ottom of screen
 H               # first ("H"igh) line of screen
 M               # middle line of screen
 L               # last ("L"ow) line of screen
```

## Help
```
 :h cmd            # help (normal mode command)
 :h i_cmd          # insert mode (eg. i_CTRL-X)
 :h v_cmd          # visual
 :h c_cmd          # command line editing
 :h :cmd           # command line
 :h 'option'       # help for a setting
 :h option?        # ditto
 :h /ordinary-atom # help on special chars
 :h tag            # tags

 K                               # help for command under cursor
 :h index                        # one liners for lots of stuff
 :h patCTRL-D                    # show all topics matching pat
 :h <TAB>                        # use tab completion to see options
 :helpgr                         # grep help
 :helptags ~/technotes           # rebuild tags file after editing
                                 # puts tags in specified dir
                               

 CTRL-]         # go to tag under cursor
 CTRL-O         # jump back
 CTRL-t         # tag back
 CTRL-w ]       # open tag under cursor in new split ( see |CTRL-W_]|)
 :cn / :cp      # jump next / prev matches
```

## NERDTree
```
 :N<Tab>   # open NERDTree
 m         # add/delete files
 cd        # cd vim to dir
 C         # navigate into dir
 u         # up

 ?         # toggle help
 q         # close NERDTree

 :tabf .   # open NERDTree in new tab
 t         # open file in tab
 I         # toggle hidden files
 i / s     # open in horiz / vert split
```

Buffers
```
:ls      list buffers
:ls!     include unlisted buffers
    Key:    a   active (loaded in a window, which may be in another tab)
            %   current window buffer
            #   alternate
            u   unlisted
            h   hidden
            =   readonly (saving requires !)
            -   not modifiable (editing won't work)
            +   modified
 CTRL-6       # switch to previous buffer edited in this win
 :bn / :bp    # next / prev buffer
 :bf / :bl    # first / last buffer
 :e file      # opens file in new buffer
 :b           # back to current buffer (e.g. get out of help)
 :b{n}        # edit buffer n
 :bp          # previous buffer
 :bd          # close current buffer
 :bw          # delete buffer (not file)
 :new         # new empty buffer in split
 :ene[w]      # new empty buffer in this win
 :bufdo bd    # close all buffers
```

## Windows
```
 CTRL-W s/v    # create horiz / vert split
 CTRL-W hjkl      # move focus
 CTRL-W T         # promote to tab
 CTRL-W c         # close current
 CTRL-W o         # only; close all except this (eg. close help)
 CTRL-W q         # use CTRL-W c instead

 CTRL-W n         # new split with empty buffer
 CTRL-W w         # switch splits
 CTRL-W f         # open file under cursor in new win split
 CTRL-W gf        # open file under cursor
 CTRL-W =         # vert splits same width
 CTRL-W +         # 1 line higher
 CTRL-W 10+       # this 10 lines higher
 CTRL-W | 80      # 80 cols wide
```

## Tabs
```
:tabnew               new tab
:tabf file            new tab with file
gt / gT                    # :tabnext / :tabprevious
<C-PageDown> / <C-PageUp>  # :tabnext / :tabprevious
:tabnew  :b 9              # open buffer 9 in new tab         |:buffer|
:tab sball                 # open all buffers in tabs
:tabm                      # move tab to last
:tabm 2                    # move tab to first
:tabm +1 / -1              # move tab right / left
```

## Command_Mode
```
%       current buffer filename
##      args list
^r/     insert last search pattern
q:      open :command history vim window
            |Enter| on command to re-execute.
            |Enter| on last blank command to return to editor.

:so %                      # source current file (eg. if editing .vimrc)
:reg                       # display registers
:redo                      # redo last undo
:args `ls *.txt`           # set args to list of text files
:argdo %s/blah/bla/ge      # run subst on all args, "e" ignores errors
                           # in case one file doesn't have the pattern
:argdo update              # write changes to all files
:vim /pattern/g ##         # vimgrep in all arg files, populate quickfix list
:argdo %s/foo/bar/g | w    # change all foo to bar and save
:set ignorecase?           # query a value
:set ignorecase!           # toggle a boolean
:set ignorecase&           # set to default
:set ignorecase tabstop=2  # multiple assignments on one line
:setlocal tabstop=4        # local to a buffer only
:echo $MYVIMRC             # set to path of .vimrc
:scriptnames               # list all scripts sourced during init
```

## formatoptions

My preference:

    :set formatoptions=jqlnor

    j   sensibly remove comment leaders on joining lines
    q   allow gq to format comments
    l   long lines not broken in insert mode
    n   recognise numbered lists when formatting
          needs autoindent set, and uses formatlistpat
    o   add comment leader with o or O
    r   add comment leader on <Enter> in insert mode

I DON'T want these:

    t   wrap using textwidth
    c   as t, but insert comment leader when wrapping
    w   trailing space?
    a   auto format paras
    2   allow for first line indentation of paras, like in books
    v   vi compat
    b   wrap at blank before margin

I'm not interested in these:

    m   break Asian multi byte char
    M   multi-byte join no space
    B   related to M
    1   break before 1 letter word

## Edit

    "xy        # yank to buffer x
    "+y        # yank to OSX clipboard
    "xp        # put from buffer x
    S          # substitute whole line
    ciw        # change inner word (both directions)
    ciW        # include non-alphanums
    viwp       # replace inner word with yanked text
    daw        # delete a word (includes trailing space)
    gqip       # format in para (works in simple comment blocks too)
    gqap       # format a para (as above)
    yyp^v$r-   # underline
    ^r"        # paste yank buffer in INSERT or COMMAND mode
    v$p        # paste over rest of line
    Vp         # paste over whole line
    X          # delete char before cursor (good for moving rest of line left)
    t#X        # delete char before first #
               #   (e.g. moving end of line comment to left)
    :ret       # retab (replace tab chars with spaces or whatever)

## Movement

    gg         # line 1
    gj gk      # move down/up line visually (within wrapped lines)
    { } ( )    # back/forward para/sentence
    0          # start of line
    ''         # last location
    '.         # last edit
    %          # matching bracket
    ge         # back word (on end)
    F          # back search for x
    H          # top of screen
    M          # middle of screen
    L          # bottom of screen
    ^e ^y      # scroll up / down, leave curor on same buffer line
    ]l         # next error

## Folding

    set foldmethod=marker
    za        # toggle fold
    zf        # create fold on visual selection
    zj        # move down file to next fold
    zk        # move up file to previous fold
    zm / zM   # more   folds / most  folds  (fold all)
    zr / zR   # reduce folds / least folds (fold all)

## Visual_Selection

    CTRL-V   # start visual selection
    gv       # re-select last visual selection
    o        # move to start/end of selection
    >        # indent selection
    <        # move left (e.g. align script comments)
    :        # start ex operation within visual selection

## Insert Mode

    CTRL-T / CTRL-D  # indent containing line right / left
    CTRL-U           # delete to start of line
    CTRL-W           # delete word back
    CTRL-R x         # insert register x
    CTRL-R =         # calculator

## vimdiff
With git, left pane is the current file, right pane is HEAD.

    ]c          # jump forward/back through changes
    [c          # jump forward/back through changes
    3]c         # 3rd change
    do          # diff obtain (from other pane)
    dp          # diff put (to other pane)
    :diffupdate # force recolouring if gone awry

## surround

    ysiw"       # surround with quotes (iw is text object)
    cs"'        # change double to singles
    ds"         # delete surrounding quotes

## Compile vim

```sh
./configure \
 --enable-pythoninterp=dynamic \
 --enable-python3interp=dynamic \
 --with-python-config-dir=/home/local/lib/python2.7/config \
 --with-python3-config-dir=/home/local/lib/python3.4/config-3.4m \
 --prefix=/home/local
```
IMPORTANT: if re-configuring, remove `config.cache` file.

## Recording

    qq	 # start recording to buffer "q"
    q	   # stop recording
    @q   # play back recording from buffer "q"

## Performance problems

    :profile start profile.log
    :profile func *
    :profile file *
    " At this point do slow actions
    :profile pause
    :q

    vi profile.log

## Deleting swap file

    vim filename    # Choose (R)ecover
    :e              # Choose (D)elete

See [here](https://superuser.com/questions/480367/whats-the-easiest-way-to-delete-vim-swapfiles-ive-already-recovered-from).
