# Ansible

```
ansible-doc -l                          # list all modules
ansible localhost -m setup              # view facts
ansible-playbook -l host1 playbook.yml  # limit to host1
ansible localhost -a "echo hello"
export ANSIBLE_KEEP_REMOTE_FILES=yes    # keep remote files
```

## ansible git clone
If using a clone from github, and you switch branches, remember to also
update the submodules.  E.g.
```
git checkout stable-1.9
git submodule update --init --recursive
```


## Filters
### mandatory
```
$ ansible localhost -a "{{wibble | mandatory}}"
localhost | FAILED => One or more undefined variables: 'wibble' is undefined

$ ansible localhost -a "echo {{ some_variable | default(5) }}"
localhost | success | rc=0 >>
5

```

## Action Plugin (1.9)

These run on the control server.

Put .py scripts in `action_plugins/` alongside top-level playbooks.
```
~                                                                               from ansible.runner.return_data import ReturnData

class ActionModule(object):
  def __init__(self, runner):
    self.runner = runner

  def run(self, conn, tmp, module_name,
          module_args, inject, complex_args=None, **kwargs):
    return ReturnData(conn=conn,
                      comm_ok=True,
                      result=dict(failed=False, changed=False, wibble="wobble"))
```
Call as follows:
```
  tasks:
    - name: test action
      action: hello
      register: result

    - name: print wibble
      debug: msg="{{result.wibble}}"
```


## To call Action Plugin as modules

Create an empty file with same name as action plugin without the .py extension
in `library/` directory, alongside level of playbook.

Call as follows:
```
  tasks:
    - name: test action plugin as module
      hello:
      register: result

    - name: print wibble
      debug: msg="{{result.wibble}}"
```

## Creating Filesystems

Simplistic approach:

```
---

- name: setup lvm & filesystems

  - lvol: lv=jb2 vg=myvg size=20M
  - lvol: lv=jb  vg=myvg size=20M
  - lvol: lv=jb1 vg=myvg size=20M

  - filesystem: dev=/dev/myvg/jb  fstype=ext3 
  - mount:      src=/dev/myvg/jb  name=/mnt/jb    fstype=ext3 state=mounted

  - filesystem: dev=/dev/myvg/jb1 fstype=ext4   
  - mount:      src=/dev/myvg/jb1 name=/mnt/jb/1  fstype=ext4 state=mounted

  - filesystem: dev=/dev/myvg/jb2 fstype=ext4    
  - mount:      src=/dev/myvg/jb2 name=/mnt/jb/2  fstype=ext4 state=mounted
```

The following example has the same effect, but is driven with a hash:
```
---

- name: setup lvm & filesystems
  hosts: all
  vars:
    filesystems:
      - name: /mnt/jb
        dev: /dev/myvg/jb
        fstype: ext3
        size: 20M
        vg:   myvg
      - name: /mnt/jb/1
        dev: /dev/myvg/jb1
        fstype: ext3
        size: 20M
        vg:   myvg
      - name: /mnt/jb/2
        dev: /dev/myvg/jb2
        fstype: ext3
        size: 20M
        vg:   myvg

  tasks:
  - name: create vg
    lvg: vg=myvg pvs=/dev/vdb

  - name: create lvs
    lvol: lv="{{item.dev}}" vg="{{item.vg}}" size="{{item.size}}"
    with_items: "{{filesystems}}"
      
  - name: create filesystems
    filesystem: dev="{{item.dev}}" fstype="{{item.fstype}}"
    with_items: "{{filesystems}}"

  - name: create mounts
    mount: src="{{item.dev}}" name="{{item.name}}"
           fstype="{{item.fstype}}" state=mounted
    with_items: "{{filesystems}}"
```
## local_action
`local_action` is for a single task in a play, but will be repeated
for every host targeted by the play. If you only want
the action to run once on `localhost` then it probably makes more
sense to not use `local_action` and instead
create a separate play which targets only `localhost`.
## Modules
`register: some_var` in playbook after calling a module gets the
entire dict returned by the module.

## async & poll

`async` = max time to wait for a task before continuing to the next task.
- `0` = wait forever
- task succeeds if timeout is reached

`poll` = how often to check status of task
- `0` = fire and forget, i.e. continue to next task
- non-zero makes this the minimum time the task will take, even if task
  finishes sooner
