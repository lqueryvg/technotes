# readline emacs mode

```
History

 M-p   non-incremental-reverse-search-history
 C-r   reverse-search-history 
 M-<   beginning
 C-p   previous
 C-n   next               
 M->   end-of-history
 C-s   forward-search-history
 M-n   non-incremental-forward-search-history

Movement
        line  word  char  char word line
        |<    <<    <     >    >>    >|
Move    C-a   M-b   C-b   C-f  M-f  C-e
Delete  C-u   M-Del Del   C-d  M-d  C-k

Move    b   f
Delete  Del d
C-x C-u             undo

Return              accept-line
C-t                 transpose-chars
M-t                 transpose-words
C-w                 unix-word-rubout
M-\                 delete-horizontal-space

M-u                 upcase-word
M-l                 downcase-word
M-c                 capitalize-word

```

# Misc

```
#                   comment-begin
C-[, C-J            isearch-terminators
C-l                 clear-screen
M-C-y               yank-nth-arg
M-., M-_            yank-last-arg
M-C-e               shell-expand-line
M-^                 history-expand-line
M-. or M-_          insert-last-argument
C-o                 operate-and-get-next
C-x C-e             edit-and-execute-command
M-y                 yank-pop
M-0, M-1, ..., M--  digit-argument

M-?                 possible-completions
M-*                 insert-completions 
M-/                 complete-filename
C-x /               possible-filename-completions
M-~                 complete-username
C-x ~               possible-username-completions
M-$                 complete-variable
C-x $               possible-variable-completions
M-@                 complete-hostname
C-x @               possible-hostname-completions
M-!                 complete-command
C-x !               possible-command-completions

C-]                 character-search
M-C-]               character-search-backward
M-TAB               dynamic-complete-history
M-{                 complete-into-braces

C-x (               start-kbd-macro
C-x )               end-kbd-macro
C-x e               call-last-kbd-macro

M-a, M-b, M-x, ...  do-uppercase-version
ESC                 prefix-meta
M-r                 revert-line
M-&                 tilde-expand
C-@, M-<space>      set-mark
C-x C-x             exchange-point-and-mark
M-#                 insert-comment
M-g                 glob-complete-word
C-x *               glob-expand-word
C-x g               glob-list-expansions

C-x, C-v            display-shell-version
C-x C-r             re-read-init-file

C-q, C-v            quoted-insert
C-v TAB             tab-insert
C-p                 previous-history
C-n                 next-history
C-f                 forward-char
C-b                 backward-char
C-a                 beginning-of-line
C-e                 end-of-line
M-f                 forward-word
M-b                 backward-word
M-Rubout            backward-kill-word
C-x Rubout          backward-kill-line
C-k                 kill-line
M-d                 kill-word
C-u                 unix-line-discard
```

