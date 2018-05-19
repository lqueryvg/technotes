# Rsync
```
-h  # human readable numbers
-a  # = -rlptgoD
    #   -r = recurse
    #   -l = links
    #   -p = perms
    #   -t = times
    #   -g = group
    #   -o = owner
    #   -D = devs & specials
-n  # dry run (for SAFETY)

rsync -n -a source dest   # copy whole directory locally

# adding -delete is RISKY but makes both identical
rsync -n -hva --delete-after /data/photos /run/media/john/Toshiba

# Back again, but no delete.
rsync -n -hva /run/media/john/Toshiba/photos/ /data/photos/
```

### Trailing slash
- irrelevant on destination
- *on source*, means copy contents of (not the dir itself)

E.g. these are quivalent:

    rsync -av /src/foo /targ  # copy dir foo and contents to target dir

    rsync -av /src/foo/ /targ/foo
      # copy contents of dir foo (not dir foo itself) to target dir


### Re-organising large directories

#### Scenario:

You have a large directory with lots of files in a directory tree which are
backed up via rsync to another directory (possibly on a remote machine or an
external disk).

You want to restructure your directory, perhaps renaming files and/or moving
them to different sub-directories, but without changing the contents of the
files themselves.

But, the next time you run rsync to it copies all of the files whose names have
changed or been moved again.  The following is a way to avoid copying all the
data again, and is *quick*.

Some key options:
```
-H  # preserve hard links
-P  # = --partial --progress
    #     --progress              show progress during transfer
    #     --partial               keep partially transferred files
```

Initial copy:

    rsync -havHP /data/dir1 /targ

Next create a working directory within the source directory
which consists of hard links to the original (this is *quick*):

    cd /data
    cp -rlp dir1 dir1-work
    # -l = link not copy
    # -p = preserve permissions etc

Now make changes in `dir1-work`, including moving and renaming files.

The next sync is *quick*, since it only replicates hard links on the target,
and doesn't copy data unless file contents have changed.

    rsync -n -havHP --delete-after --no-inc-recursive \
    /data/dir1 /data/dir1-work /targ

Finally, tidy up the working dir on both source and target:

    mv dir1 dir1-old    # or delete it
    mv dir1-work dir1
