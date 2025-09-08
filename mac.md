# Mac notes

## keys

```
Fn Up / Down        # page up / down
Cmd z x c v         # undo cut copy paste
Ctrl Left / Right   # nav workspaces
Cmd `               # nav wins (in same app)
Cmd Left / Right    # to start / end of line    (Shift to select)
Alt Left / Right    # word back / forward       (Shift to select)
Ctrl Up             # visual nav workspaces
Cmd w               # close win
```

## Screenshots

To file:
```
Cmd Shift 3         # screen
Cmd Shift 4         # area
Cmd Shift 4 Space   # window
```

Add `Ctrl` to make it go to clipboard.

Save location:
```
defaults write com.apple.screencapture location  ~/Desktop/screenshots
```

## iterm2

```
Shift Cmd [ / ]   # nav tabs
Cmd [ / ]         # nav splits
Cmd n             # new tab
Cmd d             # vert split
Cmd Shift d       # horiz split
Cmd Enter         # fullscreen toggle
Alt Click         # select when in vim


defaults write com.googlecode.iterm2 NSUserKeyEquivalents -dict-add 'Clear Buffer' '^k';
```

### Clear Buffer Shortcut

Go to `System Settings` & find application keyboard shortcuts.
Choose iterm, menu name = `Clear Buffer`, key stroke = `shift-ctrl-k`

## less common

```
Ctrl Down           # select window by num
```

## mdls

```
mdls *.mp4    # show attrs
xattr -d com.apple.metadata:kMDItemWhereFroms *.mp4    # strip an attr
```

