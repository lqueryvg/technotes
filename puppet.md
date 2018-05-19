# Puppet

## Versions

Version        | Date      | Notes
---------------|-----------|------
0.22.x         | 2007      |
0.25.x         | 2009      |
2.6.x          | 2010      |
2.7.x          | 2011      |
3.x            | 2012      | "modern" puppet
3.3            | Sep  2013 | manifest ordering
3.4            | Dec  2013 | introduces `contain`
4.0            | Apr  2015
4.2, PE 2015.2 | June 2015
4.3, PE 2015.3 | Nov  2015

## Install

### Pre-requisites

These DNS facts need to be consistent:
```
  $ facter domain hostname fqdn
  domain => mydomain
  fqdn => mypc.mydomain
  hostname => mypc
```

### PE 2015.3 install
- Download PE & unpack it.
- Change to unpacked directory.
- `./puppet-enterprise-installer`
- Connect with browser as instructed.

Clean a failed install with: `puppet-enterprise-uninstaller`.

### Puppet 3.8 install

If firewall enabled, allow `8140` traffic.
```
  rpm -ivh http://yum.puppetlabs.com/el/7Server/products/x86_64/puppetlabs-release-7-11.noarch.rpm
  yum install puppet puppet-server facter
  cd /etc/puppet
  puppet master --genconfig > puppet.conf
  vi puppet.conf 
    [main]
    server=puppet.mylab.com
  cd /etc/puppet/manifests
  touch site.pp
  puppet master --verbose --no-daemonize -d   # to debug
  systemctl start puppetmaster
```

### Add agent

On agent:
```
site=yum.puppetlabs.com
url=$site/el/7Server/products/x86_64/puppetlabs-release-7-11.noarch.rpm

rpm -ivh http://$url
yum install puppet
puppet agent --test --verbose   # add server=blah.fqdn if not "puppet"
```
Note: if master is not resolvable as "puppet":
- add `server=blah.fqdn` to `puppet` command line
- `vi /etc/puppet/puppet.conf` and add:
```
   [main]
   server=mypuppetmaster.fqdn
```

Master:
```
puppet cert list    # on master
puppet cert sign mynode.fqdn
```
May need to wait 2 mins for PE console to pick this up.

#### Using PE script
Master:
```
 mkdir -p /opt/puppetlabs/server/data/staging/pe_repo; cd $_
 curl -LO {url}    # get agent rpm
```
Agent:
```
 curl -k https://{master}:8140/packages/current/install.bash | sudo bash
 puppet agent -t   # no need to edit puppet.conf
```

#### Remove agent:
Master:
```
puppet cert list --all      # list all know (signed) certs
puppet cert --revoke {agent}
puppet cert clean {agent}
```
Agent:
```
yum erase puppet ruby augeas facter hiera
rm -r /etc/puppetlabs/puppet/ssl
```

## Puppet Commands

Agent run:
```
puppet agent --test
puppet agent --test --server=blah   # if master not "puppet" or in puppet.conf
puppet agent --test --debug  # debug
```
Start/stop:
```
puppet master   # start master
puppet master --no-daemonize  # to debug
```
Resources:
```
puppet describe -l          # list all resource types
puppet describe -s <res>    # short info
puppet describe <res>       # long info
puppet resource user                        # show user resources on this node
puppet resource user root                   # just root user
puppet resource user dave ensure=present    # create user dave
puppet resource user dave ensure=present --debug
puppet resource user dave ensure=absent     # delete user dave
puppet resource user ftp comment="FTP User" --debug
  # set an attribute and show commands used
puppet resource user ftp comment="FTP User" -noop --debug
  # As above but don't change.
```

Running manifests:
```
puppet apply -e "user { 'fred': ensure => present, }"
puppet resource -e user galatea   # pull settings into vi to edit and apply
puppet parser validate blah.pp    # validate
puppet apply --noop blah.pp       # check after making changes
FACTER_operatingsystem=Debian puppet apply --noop blah.pp
puppet apply -e 'notify {"hello": }'    # a dummy resource
puppet apply -e 'notice ("hello")'      # a function
puppet apply -e 'if "P" == "p" { notify { "true!": } }' # == is case insensitive
puppet apply --modulepath=/mydir blah.pp
```

Debugging:
```
puppet agent --test --debug           # add --debug to end of any puppet command
puppet master --compile somenode.fqdn # display compiled catalog
puppet master --no-daemonize --verbose --debug
puppet apply -e 'notify {"hello": }'
puppet apply -e 'notice ("hello")'
```

Hiera command line:
```
hiera classes mynode    # lookup classes for node
```

## File Structure
```
puppet config print                         # display config
puppet master --configprint modulepath      # display a master config variable
/etc/puppetlabs/puppet/puppet.conf
/etc/puppetlabs/puppet/ssl/        # certs
/etc/puppet/puppet.conf  # on master
  certname=mypuppetmaster.fqdn
/etc/puppet/manifests/site.pp  # .pp files are manifests
```
Default configured directories:
```
  vardir = /var/opt/lib/pe-puppet
  logdir = /var/log/pe-puppet
  rundir = /var/run/pe-puppet
```

## Class

- can be declared multiple times, without causing the run to fail
- will only be evaluated *once* for a node / catalog

## Define

- can be declared (and *evaluated*) multiple times with different params
- causes run to fail if declared more than once with same params
- **so beware**: resources declared inside a `define` must to be unique

## Style
- use single quotes for strings
  - unless they contain variables
- put variables in `{}` when in a string
- `ensure` attribute (if there is one) should come first in a resource
- line up `=>` arrows
- indent = 2 spaces
- no TABs
- group resources by logical relationship
- no `;`s between statements inside a `{ }` block
- put resource defaults at top scope (`site.pp`)

## Resources
- can use an array in resource title
```
package { ['postfix', 'mailx']:
  ensure => present,
}
```
## Resource defaults
- only use at top scope (`site.pp`) or in class which will never be inherited
  or defined anywhere else
- capitalised with no title to specify resource defaults for **current scope**
```
File {
  owner => 'fred',
  mode  => '0644',
}
```
## Selectors
- use only in variable assignments
- always supply a default
```
$package_name = $::osfamily ? {
  'RedHat'  => 'openssh-server',
  'Solaris' => 'openssh',
  default   => 'ssh',
}
```

## include

To use a class, we have to *declare* it.
- no implied ordering
  - resources in included class do not necessarily get applied first ?


Prior to 2.6:
- had to use `include` to declare a class
- `include` can declare a class multiple times in same catalog,
  but it will only be applied once

From 2.6 (Jul 2010) onwards:
- `class { 'ntp': }` is like `include ntp`
- class parameters introduced
- can declare (i.e. use) a class like a resource, use parameters:<br>
`class { 'classname': param => 'myvalue', … }`
- only allowed to declare class once in same catalog

## inherits (*avoid*)

```
node fred inherits basenode { ...  }
```
- avoid
- `inherits` creates new scope, so impossible inherited class's
-  ariables from this scope
- use `class` instead; allows you to set variables either before
  or using parameterised classes

## import (*avoid*)

Deprecated; **avoid**.
Instead:
- use modules
- specify a directory as main manifest for node definition files (in
  alphabetical order)

## require (function, not metaparameter)

- includes a class, but with dependency on all resources within
- style guide says it is "largely unnecessary", because class-level
  dependencies can be handled in other ways
- use `include` followed by chaining arrows instead (which can also be
  used for refreshes):
```
class thisclass {
  include thatclass
  Class['thatclass'] ~> Class['thisclass']
}
```

## contain
- use `contain` instead of `include` to maintain
  dependencies (if any) between contained resources
- >= 3.4

## Scope

- cannot change a variable in same scope (because language is declarative)
- 4 types of scope:
  - top
    - outside any class, type or node definition
    - can only access variables from top scope
    - top scope variables are accessible everywhere
    - use `$::var`
  - node
    - code inside a node definition
    - can access variables from its own scope and top scope
  - local
  - inherited
- *dynamic* scope is **deprecated** and should be avoided since it can be
confusing and unpredictable; use top scope instead.

## Resource Ordering

Chain with `->` (ordering), `~>` (notifications) or use attributes:

- `before    => that` = do this before that
- `require   => that` = do this after that (*preferred*; more intuitive)
- `notify    => that` = do this before refreshing that
- `subscribe => that` = refresh this after doing that (*preferred*)

Notes:
- `ordering = manifest` in `puppet.conf` under `[agent]` section means
resources without explicit ordering will follow order of appearance
in manifest file
- `puppet agent --configprint all |grep order` to show ordering
- file resources create an implicit ordering for subdirs
- most common refreshable types are `service` and `exec`

## Core Types & Attributes


Name | Attributes & Notes
:----|:----------
notify | sends message to agent log
file | 
package |
cron | minute; use with `fqdn_rand(60)` to spread agent connection times
exec | creates, onlyif, unless
service |
user |
group |

- capitalise initial letter of type names when using them
- lowercase initial letter of type names when declaring them

## notify {} vs notice()

- `notice()` is a function
- `notify {}` is a dummy resource
- `notify {}` will appear in a Puppet report

## Templates

```
<%= @variable %>                        # instance variable (preferred)
<%= variable %>                         # local variable
erb -x -T- mytemplate.erb | ruby -c     # check syntax
```

## Modules

### Module File Structure
- site modules go in `/etc/puppet/modules/`
- `modulepath` in `puppet.conf` has colon separated path to modules
- module subdirs are `manifests`, `files` & `templates`
- manifests dir must contain `init.pp`

### Module Class Structure

- main class:
  - goes in `init.pp`
  - same name as module (e.g. `ntp`)
  - inherits from `params`
  - parameterised
- `params` class
  - goes in `params.pp`
  - inherits nothing
  - parameterised
- other classes (e.g. `install`, `config` or `service`)
  - go in their own file, e.g. `service` goes in `service.pp`
  - inherits from main class

```
  # init.pp
  class ntp (
    $blah = $ntp::params::blah,
    ...
  ) inherits ntp::params {
    ...
  }

  # params.pp
  class ntp::params {
    $blah = some_useful_default
    ...
  }

  # service.pp
  class ntp::service inherits ntp {
    ...
  }
```
### Module Parameters
- named `thing_property`
- use validate functions in `params` class
- use parameters instead of hard-coding data
- don't allow user to override templates; provide more parameters instead or allow arbitrary chunks of text

### Module Internal Class Ordering
- use classes (not resources) to specify order
  - this shields users from implementation details (e.g. what resources are contained in a class)
- use `contain` (>=3.4) or `include` / `anchor` pattern
  - this ensures that any user `require` dependencies happen after
    your module's resources
  - otherwise, classes will not automatically "contain" the classes they declare
- example anchor pattern:
```
Anchor['module::begin'] ->
Class['module::install'] ->
Class['module::config']  ->
Class['module::service'] ->
Anchor['module::end']
```
### Module Internal Class Dependencies
- `include` any dependencies from `init.pp`
- add them to `.fixtures.yml` for rspec to work

### Module generation tool
`puppet module generate yourname[/-]modulename`

#### Terminology

- **agent**: puppet + facter
- **agent modes**: daemon, cron or manual
- **auto-import**: when module is loaded, puppet searches module manifests
  directory happens for namespaces classes and imports them
- **bucketing**: backing up files (can be configured to do this on master)
- **catalog**: output of manifest compilation on master
  - specific for each node
  - nodes are not able to see each other's catalogs
- **class**: group of resources (and other classes)
- **classification**: can use external sources,
  e.g. LDAP or ENC (external node classification)
- **ENC**: see classification
- **ensure**: can be: present, running, latest
- **facter**: runs on node to provide key=>value facts
- **function**: only gets executed on the master
- **heira**: key-value lookup system, from which puppet can get its data
- **idempotent**: can run multiple times with same result
- **resources**: things we manage, e.g. service, file, user, etc
- **librarian**: (see r10k instead) installs modules from puppet forge with
    dependencies
- **manifest**: DSL configuration instructions, not node specific
  - release must be > agent release
- **master**: puppet + facter + puppetmaster
- **passenger**: (aka mod_rails) web app server in Apache or Nginx
- **provider actions**: can create, destroy, modify (platforms specific)
- **r10k**: installs modules from puppet forge without dependencies
- **RAL**: Resource Abstraction Layer (tells puppet *how* to do things)
- **require**: there are two types of *require* in Puppet DSL: one is for
  classes, the other is a metaparameter
- **resource types**: see types
- **run**: (aka Puppet Run or Transation or Configuration Run)
   - compile catalog for each agent
   - send to agent
   - apply on agent
   - report back to master
- **Transaction Layer**: determines order of operations
- **types**: units of configuration, e.g. package, service, file
- **type types**: Can be *defined* (DSL) or *native* (Ruby)
- **WEBrick**: deprecated ruby http service providing basic puppet web service


## Misc

### Forgotten PE console user/password ?
Add a new admin user as follows, then you can change/delete existing users.
```
cd /opt/puppet/share/puppet-dashboard
/opt/puppet/bin/bundle exec /opt/puppet/bin/rake -s \
  -f /opt/puppet/share/console-auth/Rakefile db:create_user \
  USERNAME=user@somedomain.com PASSWORD=password \
  ROLE="Admin" RAILS_ENV=production
```

## Scalability
- have several masters
- manually distribute which agents point to which master
  or round-robin them with a SRV record or a load balancer
- have a single master handle certs

## MCollective

```
su - pepuppet
mco help
mco help {cmd}
mco ping
mco puppet status
mco puppet count
mco find  --with-id /pup/
mco facts hostname
```

## Roles & Profiles
- a node is assigned 1 (and only 1) role
- a role includes multiple profiles
- profiles perform hiera lookups & include multiple modules
- if you ever need multiple roles for a node, create a new role

### Roles
- only include profiles
- don't include environment name
- only use include inside Role, so profiles can be included multiple times
- if two nodes differ by data, put the data in heira

### Profiles
- group classes & modules by technology
- name according to technology (e.g. profiles::wordpress)
- declare classes
- heira lookups (usually ONLY profiles do lookups)
- only include environment & organisational names if different
  parts of organisation have different setups, BUT this is not idea code
  should be shared
- don't set defaults - let heira do that; code should fail to compile so
  you know about it
- component modules don't use hiera
- refer to modules with top level names e.g. ::sometech
