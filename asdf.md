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
    cut -d' ' -f1 .tool-versions | xargs -I% asdf plugin add %

    asdf current

    asdf list                   # list installed versions
    asdf current                # list currently configured versions
    asdf list all {name}        # all versions available to install
    asdf install {name} 3.8.3

    asdf install {name} {latest|version}
    asdf uninstall {name} {version}
    asdf set {name} {latest|version}   # set in current .tool-version
    asdf set -u bun latest             # adds "bun 1.3.1" to ~/.tool-versions
    asdf set -p bun latest             #   in closest parent

    asdf which command
