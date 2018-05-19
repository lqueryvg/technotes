# yum / rpm

```sh
cd /etc/repos.d                 # repo config dir
yum repolist all                # a column shows enabled/disabled
yum-config-manager --enable Dropbox     # set enabled to 1 in file

yum --disablerepo='*' \
--enablerepo=updates repolist all   # should now only see "updates" enabled

yum list available          # list all available rpms and their repo

yum list                        # list installed all available packages
yum list installed              # list installed all available packages
yum -C list                     # use only local and cached files

yum list xxx                    # list installed package (whole name)
yum history list                # show recent updates
yum history info <index-num>    # show packages in update
yum history undo                # undo last transaction
yum grouplist
yum groupinfo "group1"
yum search                      # can match against rpm name
yum whatprovides                # finds what package a file is in

yum clean all       # purge yum cache
yum clean headers
yum clean packages
yum localinstall         # install local rpm file, but consider your repos
                         # to install dependencies
repoquery                # rpm -q like queries on uninstalled rpms in repos
repoquery -ql {package}  # query contents of package in yum repo

package-cleanup --leaves        # show leaf packages
rpm -q kernel
package-cleanup --oldkernels --count=3      # keep only 3 kernels
rpm -q --whatprovides /usr/bin/nslookup   # find rpm containing file (must be
                                          # full path)
rpm -qp                     # query un-installed .rpm file
rpm -qf /usr/sbin/chronyd   # need full path
rpm -qc chrony              # query config files for an rpm
rpm -qd chrony              # query doc files for an rpm
rpm -q --scripts chrony     # query scripts of installed rpm
rpm -qp --scripts xyz.rpm   # view install scripts of uninstalled rpm
rpm --querytags             # display list of possible tags
rpm -qa --last          # show date/time of install and sort most recent first
rpm -qa --queryformat '%{NAME} was installed on %{INSTALLTIME:date}\n'
rpm -ivh    # install, verbose, hashes

vi /etc/yum.conf
keepcache=1         # Retain downloaded files
                    # cache in /var/cache/yum/ one dir per repo

repomanage -o .                 # list all rpms which can be deleted
repomanage -o . | xargs rm -v      # now delete them!

```
Minimal repo referring to local repo in `/repo`:
```
[myrepo]
name=myrepo
baseurl=file:///repo
gpgcheck=0                  # for test only
```

