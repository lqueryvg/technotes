#### GNOME 3 (not classic)

Keyboard Shortcuts
```
Super+{thing}   # search for application (Super = Windows key)
Alt+Tab         # switch apps
Alt+F8          # resize
Alt+F7          # move

Alt+`           # switch between windows of current app
Super+M         # message tray
Super+Up        # maximise
Super+Down      # minimise
Super+Left      # left half of screen
Super+Right     # right half of screen
Ctrl+Click      # launch new win (don't just go to existing)
Middle-Click    # launch in new workspace
Ctrl+Alt+Up     # workspace above
Ctrl+Alt+Down   # workspace below
Super+F10       # open application menu
Alt+F2          # command prompt
```

To make wired network connect automatically:
```
- network icon
- network settings
- Indentity
- Connect Automatically
# (I think this just sets ONBOOT=yes in ifcfg-* file.)
```

Command Prompt
```
Alt+F2          # command prompt
lg              # looking glass
r               # restart gnome-shell
```

Settings
```
dconf-editor               # gui to view/edit all sorts of settings
xmodmap -pm                # show current xmodmap settings
gsettings list-schemas
gsettings list-recursively # show all setting schemas, keys and values
setxkbmap -print           # show keyboard files currently in use,
                           # these can be found in /usr/share/X11/xkb
xev -event keyboard        # look at keyboard events
```

Use xkb instead of xmodmap (quick hack)
```
xkbcomp $DISPLAY xkb.dump
vim xkb.dump                    # swap some scan codes
xkbcomp xkb.dump $DISPLAY
```
