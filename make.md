# GNU Make
## Special variables


    {target}: {prereq}

    $@  = {target}
    $<  = {prereq}}

## .PHONY

A target which is always out-of-date even if file with same name exists.

    .PHONY: clean
    clean:              # always runs even if file called "clean" exists
        rm -rf *.o

## Pipe failures

Dealing with commands involving pipes.

### The problem

`$?` takes the exit status of the last command in the pipe.
But you may want the target to fail if *any* command fails in the pipe.

    test1:
        exit 1 | cat
        echo "Oh no, I'm still running"

### Preferred solution

The following approach is for a single line:

    test2;
        set -o pipefail; exit 1 | cat
        echo "This echo will not run and make will exit"

### Alternative

The following affects the whole file:

    test4:
        exit 1 | cat
        echo "Will not run"

    SHELL=/usr/bin/bash -o pipefail
    # All targets, before and after now have pipefail behaviour.

    test5:
        exit 1 | cat
        echo "Will not run"

