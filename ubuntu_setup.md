
# my ubuntu setup
- install ubuntu 16.04
- install guest additions (does not work from virtualbox iso)
```
apt-get install virtualbox-guest-dkms
```

- Enable workspaces `settings -> Appearance -> Behaviour -> Enable Workspaces`
- install `google-chome` from google website (need to use firefox once!)
- basic settings
```
sudo apt-get install curl
echo 'set -o vi' | tee /etc/profile.d/john.sh
```
- install homebrew: copy/paste instructions from http://linuxbrew.sh/ to
  install and add to path
```
brew install zsh git vim liquidprompt
brew install ipython
ln -s /home/linuxbrew/.linuxbrew/share/liquidprompt ~/.liquidprompt
sudo ln -s /home/linuxbrew/.linuxbrew/bin/zsh /bin
```
- https://slack.com/downloads/linux
  - enter in workspace url and email address
  - ask it to email magic link instead of pw
  - nagivate link in chrome and tell chrome to always use xdg-open to open urls
- setup personal ssh keys
```
mkdir -m 700 ~/.ssh; cd ~/.ssh
tar xvf {ssh_keys}.tar
```
- clone git repos
```
cd
git clone https://github.com/lqueryvg/technotes.git
git clone https://github.com/lqueryvg/dotfiles.git
cd ~/dotfiles/bin; ./install
```
- docker

  install docker-compose as per https://docs.docker.com/compose/install/#master-builds
```
sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo usermod -aG docker john
```

- Editors
```
sudo snap install pycharm-community --classic
sudo snap install atom --classic
```

- Dropbox
```
cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
~/.dropbox-dist/dropboxd
# login on browser
mkdir ~/bin; cd ~/bin
wget "https://www.dropbox.com/download?dl=packages/dropbox.py" -O dropbox.py
chmod +x dropbox.py
./dropbox.py autostart y
```

- KeePassXC (from Ubuntu software)

- ipython
```
python3 -m pip install ipython
python2 -m pip install ipython     # last to be install = default
ipython profile create
echo "c.TerminalInteractiveShell.editing_mode = 'vi'" >> \
  ~/.ipython/profile_default/ipython_config.py
```

- console mouse
```
apt-get install gpm
```


## From base packer box
### Install Unity
    
    yes | apt-get -yq install ubuntu-desktop
    dd if=/dev/zero of=/EMPTY bs=1M
    rm -f /EMPTY
    init 0

    apt-get -yq remove --purge 'libreoffice*'
    apt-get -yq autoclean
    apt-get -yq autoremove

    sudo locale-gen
    sudo localectl set-locale LANG="en_US.UTF-8"


#### Cut down - does it work ???
    export DEBIAN_FRONTEND=noninteractive
    yes | apt-get -yq install --no-install-recommends ubuntu-desktop \
        snapd \
        baobab \
        ubuntu-software \
        evince \
        fonts-liberation \
        xterm \
        gnome-terminal \
        gnome-panel

    sudo locale-gen
    sudo localectl set-locale LANG="en_US.UTF-8"

    apt-get install gnome-user-share
    apt-get install gnome-system-monitor
    unity-gtk2-module
    unity-gtk3-module
    psmisc
    gir1.2-unity-5.0

    apt-cache show ubuntu-desktop

### Set -o vi

    echo 'set -o vi' > /etc/profile.d/vi.sh

### Set locale

    update-locale LC_ALL=en_GB.UTF-8 LANG=en_GB.UTF-8

### Lightdm

```
cat <<EOF > /etc/lightdm/lightdm.conf.d/50-log_me_in.conf
[SeatDefaults]
autologin-user=vagrant
greeter-show-manual-login=true
EOF
systemctl restart lightdm
```


