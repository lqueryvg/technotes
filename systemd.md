# Systemd

```sh
systemctl list-unit-files   # list all (enabled, disabled, etc) services
systemctl status            # show lots of info for all services
systemctl status httpd
    # shows various info:
    #   start on boot
    #   active
    #   main pid
    #   worker pids
    #   drop-in config file
    #   bandwidth stats
/etc/systemd/system/httpd.service.d/50-httpd.conf          # config file

systemctl daemon-reload                 # notice new service files
systemctl start abrtd.service
systemctl isolate {target}              # start unit and deps, stop others
systemctl isolate rescue.target         # to rescue mode
systemctl isolate multi-user.target     # to multi-user
systemctl start systemd-suspend.service # suspend computer
```

## Cgroups
By default, system is split into user and system slice.
```
systemd-cgls                            # list cgroups

cgcreate -g cpu:A           
cgexec -g cpu:A yes > /dev/null &   # run process under named cgroup
cgget -r cpu.shares A               # shows default shares of 1024
cgset -r cpu.shares=256 A           # min possible value = 2, change is dynamic
systemd-cgtop                       # monitor

systemctl show -p CPUShares user.slice              # show cpu shares, def=1024
systemctl set-property --runtime user.slice CPUShares=300  # set shares

systemd-cgtop                           # like top but showing cg groups
```

On a single CPU system, for two single threaded cgroup workloads `A` & `B` with
cpu shares `a` and `b` resp., `A` gets `a/(a+b)` fraction of cpu time, `b` gets
b`/(a+b)`.  **But** on a 2 cpu system, they get 1 CPU each.  I.e. cgroup shares
only take effect when CPU is constrained.

## Targets
```shell
# like init runlevels, but more expressive
systemctl get-default
systemctl set-target {target}
```
- `init` now redirects to systemd.
- `man systemd` shows mapping of old init runlevels to target names
  - single, s, S, 1   = `rescue.target`
  - 2, 3, 4, 5        = `runlevel{n}.target`

## journalctl
- red = error
- bold = warning/notice
- times are timezone corrected
```sh
journalctl -n        # last 10 lines
journalctl -x        # catalog info where available
journalctl -b        # since current boot
journalctl -f        # like tail -f
logger hello         # send test message
journalctl -b -p err # errors since boot (filter by priority)
journalctl -p err --since today
journalctl -p err --since today --until 12:00
journalctl -o verbose  # shows structure
journalctl --since yesterday
journalctl -u slapd
journalctl _COMM=su     # everything logged by su commands
```

- Items *with* underscore are trusted (from systemd).
- Items *without* underscore are provided by the client

```sh
journalctl {TAB}       # show field names
journalctl _COM{TAB}   # autocomplete _COMM field name
journalctl _COMM={TAB} # autocomplete possible commands
journalctl _COMM={TAB} # autocomplete show command names
```

## Documentation
```sh
man systemd.directives # index of directives / options, pointing to man pages
man systemd.exec       # Execution environment configuration
man systemd.unit       # common options of all unit configuration files
```

## Unit configuration files

- Options are configured in the [Service], [Socket], [Mount], or [Swap]
sections of the unit file, depending on the unit type.
- The section names
and options can be found in the various man pages, e.g.:

```sh
man systemd.service
man systemd.socket
man systemd.swap
man systemd.mount
```
