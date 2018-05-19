# gpg

```
gpg --gen-key    # answer with defaults and give empty pass phrase
                 # give real name and email
                 # be patient while it generates random bytes

gpg --list-keys                    # should see new key
gpg -e -r myname@myemail.com file1 # encrypts file1 to file1.gpg
gpg -d file1.gpg                   # decrypts file1
```

## To copy keys to another computer
```
keyid=39998913   # for example
gpg --export-secret-keys -a $keyid >  keys.txt  # only need this one
gpg --export             -a $keyid >> keys.txt

scp keys.txt user@host:
ssh user@host gpg --import keys.txt
```
