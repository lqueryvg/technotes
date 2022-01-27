# asdf

## Install

    brew install coreutils curl git
    brew install asdf

    # zsh
    . $(brew --prefix asdf)/asdf.sh


    asdf plugin list all        # none, initially
    asdf plugin add {name}
    asdf plugin list            # none, initially
    asdf plugin update --all

    asdf current

    asdf list                   # list installed versions
    asdf current                # list currently configured versions
    asdf list all {name}        # all versions available to install
    asdf install {name} 3.8.3

    asdf install {name} {latest|version}
    asdf global exa latest
    
    asdf {global|shell|local} {name} {latest|version}

    asdf which command
