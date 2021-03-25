```
vi ~/.config/asciinema/config

    [record]
    idle_time_limit = 0.5
    [play]
    idle_time_limit = 0.5

asciinema rec -i 0.5 --overwrite demo.cast

. ~/.my_zshrc          # otherwise PATH will be screwed
precmd_functions=""    # disable dynamic prompt
export PS1="$ "        # set minimal prompt

echo "START"

exit        # to quit asciinema

vi demo.cast
# delete lines you don't want
# don't worry about time gaps because the idle_time_limit on line 1 will speed up the playback

# A line like this will set the colour to white:
  [39.930364, "o", "\u001b[0m"]

# Or Green:
  [39.930364, "o", "\u001b[32m"]



