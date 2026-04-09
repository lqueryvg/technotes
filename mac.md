# Mac notes

## keys

```
Ctrl Cmd q          # lock screen
Fn Up / Down        # page up / down
Cmd z x c v         # undo cut copy paste
Ctrl Left / Right   # nav workspaces
Cmd `               # nav wins (in same app)
Cmd Left / Right    # to start / end of line    (Shift to select)
Alt Left / Right    # word back / forward       (Shift to select)
Ctrl Up             # visual nav workspaces
Cmd w               # close win
F11                 # show desktop
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
```

### Clear Buffer Shortcut

Go to `System Settings` (old = `System Preferences`)
-> Keyboard Shortcuts -> App Shortcuts
Choose iterm, menu name = `Clear Buffer`, key stroke = `shift-ctrl-k`

Or:
```
defaults write com.googlecode.iterm2 NSUserKeyEquivalents -dict-add 'Clear Buffer' '^k';
```

## less common

```
Ctrl Down           # select window by num
```

## mdls

```
mdls *.mp4    # show attrs
xattr -d com.apple.metadata:kMDItemWhereFroms *.mp4    # strip an attr
```

## Fix non-breaking space problem

Manifests as markdown headings not working correctly.

The problem:

- With a UK keyboard you have to type `Option + 3` to get a `#` character.
- In markdown or scripts you often follow the `#` with a space.
- If you type this quickly, mac thinks you have type `Option + space` which inserts a
  unicode Non-Breaking Space (U+00A0) which is not seen as a heading in markdown
  and causes confusion.

Solution:

```
d=~/Library/KeyBindings
mkdir -p $d
echo '{"~ " = ("insertText:", " ");}' > $d/DefaultKeyBinding.dict
```
Restart (or log out and back in).

## Performance issues

Tahoe 26.2 seriously laggy when typing on M5 laptop.
It seems that Apple introduced a bug and here is a possible workaround.
https://www.reddit.com/r/MacOS/comments/1no872w/psa_macos_26_bug_leads_to_performance_issues_in/

Basically just run this command and reboot:
```
defaults write -g NSAutoFillHeuristicControllerEnabled -bool false
```


## Performance issues

```
defaults write com.apple.finder AppleShowAllFiles YES   # show dot files in finder
```
