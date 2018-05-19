# Packages

```
apt list              # all available
apt list --installed

apt-get update        # usually need this in container before install will work
apt-get install {pkg}
dpkg-query -L {pkg}   # list files of installed package
apt-file list {pkg}   # list files of NOT installed package

dpkg -S /bin/ls       # which package provides command at exact path
dpkg -S */ls          # ... any location

```
