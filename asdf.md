# asdf

    brew install coreutils curl git
    brew install asdf

    # zsh
    . $(brew --prefix asdf)/asdf.sh

    asdf plugin list            # none, initially
    asdf plugin add python
    asdf list python            # list installed versions
    asdf list all python        # all versions available to install
    asdf install python 3.8.3
    
    asdf global python 3.8.3
    asdf shell python 3.8.3
    asdf local python 3.8.3

