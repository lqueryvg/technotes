# SSH Port forwarding

SSH opens listens on a specified port and forwards all traffic to a 3rd
host and port.

Two types: 
- local (`-L`): the listening port is on the ssh client host
- remote (`-R`): the listening port is on the ssh server host

## Local port forwarding
```
ssh -L {listen-port}:{target-host}:{target-port} {ssh-server}

localhost            {ssh-server} 
   ssh  ----------->     sshd  

 {listen-port} ------------.
                           |
                           V
                       {target-host}
                       {target-port}
```
- {listen-port} listens on localhost
- {target-host} is from {ssh-server}'s perspective
- traffic passes via {ssh-server}

## Remote port forwarding
```
ssh -R {listen-port}:{target-host}:{target-port} {ssh-server}

localhost      {ssh-server}
   ssh ------>     sshd

     .-------- {listen-port}
     |
     V
{target-host}
{target-port}
```
- {listen-port} listens on {ssh-server}
- {target-host} is from localhost's perspective
- traffic passes via localhost

# Interactive Port Forwarding
```
~?   help
~C   command line
~#   list forwarded connections
```

Example:
```
~C
-L 8080:localhost:8001
```
Port 8080 on ssh client is now being forwarded to 8001 on remote.

# Misc
ssh-copy-id                 # copy ssh id to another server
ssh-keygen -lf id_rsa.pub   # show fingerprint

## `~/.ssh/config`
```
Host github.com
    IdentityFile ~/.ssh/github

Host bitbucket.org
    IdentityFile ~/.ssh/bitbucket
```


```
ssh-keygen -R {host}   # remove all known_hosts entries for {host}
```
