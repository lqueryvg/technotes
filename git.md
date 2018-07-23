# GIT

## Concepts
```
stage/index/cache     same thing: conceptual place to group files for commit
index                 proposed next commit; it is a file implementing virtual
                      tree for index, initially populated with what your files
                      looked like when they were last checked out
stage                 wrong!, technically should be "index"
"staging"             should be "adding files to the index"
```

## Commands
```
git add file1         # add file1 create or mod to index in prep for commit
git rm file1          # add file1 del to index in prep for commit
git diff              # working files vs index
git diff --cached     # index vs HEAD
git diff master:fred.sh fred.sh  # diff file in current branch against master
git diff --name-only  # show only filenames
git diff x..y         # the diff between two endpoints or commits
                      #   (same as git diff x y)
git diff x...y        # find common ancestor of x and y,
                      #   and then diff from there to y
git log x..y          # list commits on branch y but not on branch x
                      #   (same as git log y ^x)
git log x...y         # list commits in x or y, but not both
git commit            # commit files in index
git commit -a         # add all modified files (not untracked!) + commit
git add -u            # add all modified files (not untracked) files
git add -p            # add partial file "hunks"
```

# Getting info
```
git status                    # for current repo
git blame file1               # show commit id against each line
git log -p                    # log with diffs
git log --stat                # log with file stats
git log --oneline --graph     # ascii diagram
git log branchA ^branchB      # commits in branchA not merged to branchB
git log origin/master ^master # what's incoming (after fetch)
git log ^master origin/master # what's outgoing (after fetch, before push)
git config --get-regexp alias # aliases
```

## Correcting Mistakes:
Steps to correct a commit, before pushing to remote
(e.g. if added a file by mistake or typed wrong message):
```
  git reset --soft 'HEAD^'  # undo commit, don't change work files
  git reset file1           # unstage
  git commit -c ORIG_HEAD   # then get chance to change message
  git checkout -- file1     # revert working changes (DANGER)
```
or
```
  git commit --amend        # press Enter
```

## reset
```
git reset --soft HEAD~              # undo last commit, files still staged
git reset HEAD~                     # unstage
git reset --hard origin/branch      # reset working directory and branch to match origin


                               Level     HEAD     Index   Workdir  WD Safe?
git reset --soft [commit]      commit    REF      NO      NO       YES
git reset [commit]             commit    REF      YES     NO       YES
git reset --hard [commit]      commit    REF      YES     YES      NO ***
git checkout <commit>          commit    HEAD     YES     YES      YES
git reset [commit] <paths>     file      NO       YES     NO       YES
git checkout [commit] <paths>  file      NO       YES     YES      NO ***

REF = moves the branch
HEAD = moves head
*** = be careful !!!
```

## Remote
```
git diff remote           # diff against remote
git remote                # list remotes
git remote -v             # list remotes details
git remote show origin    # more details, e.g. remote branches
git pull                  # fetch from remote then merge, syncs local files
git fetch remote          # fetch remote branch info w/o changing local content
git fetch remote {branch} # fetch single branch
git merge origin/master   # create a merge commit here and with upstream
git push -f               # force push (careful!)
git push origin --delete {branch}   # delete remote branch
```

## Config
```
~/.gitconfig                               #  global vars for all user repos
.gitignore                                 #  in each repo dir
  *.swp                                    #  ignore swp files
git config -l                              #  list config variables
git config --global core.autocrlf false    #  leave newlines alone!
```

## Branching
```
git branch b                         # new branch at current commit
git checkout -b myfeature develop    # new branch from "develop" branch
git checkout develop                 # ready to merge back
git merge {branch}                   # merge branch into current branch
git merge --no-ff myfeature          # --no-ff makes a commit of the merge
git branch -d myfeature              # delete the feature branch
git push origin develop
git push origin :somebranch          # delete remote branch
git checkout other-branch -- f.txt   # get file from another branch
git branch -vv                       # show remote tracking branches
git cherry -v {upstream}             # list commits yet to be applied upstream
git lol {base-branch}..              # list commits since branching from base-branch
```

## Rebase 1

```
git rebase {branch}     # move current branch to tip of {branch}
```

- transplants the current branch to the tip of {branch}
- current branch remains current
- current branch commits are replaced with new commits
- new commits for changes which already exist in the target branch will be
  skipped

```
      A---B---C topic
    /
D---E---F---G master

git co topic; git rebase master
# or...
git rebase master topic

              A'--B'--C' topic
            /
D---E---F---G master
```

## Rebase 2
Scenario:
```
    o---o---o---o---o  master
         \
          o---o---o---o---o  next
                           \
                            o---o---o  topic
```
To make `topic` fork directly from `master`, but not include commits from `next`:
```
git rebase --onto master next topic

               o---o---o---o---o  master
                   |            \
                   |             o'--o'--o'  topic
                    \
                     o---o---o---o---o  next
```

## Newlines
Vundle FAQ says you should set these:
```
git config --global core.autocrlf false
git config --global core.safecrlf true
git config --global core.eol lf
```

## Squashing

- rename branch to be squashed to `{branch}_unsquashed`
- create new branch with original name based on common root (e.g. master)
- merge unsquashed into new branch

Example where the base branch is master:

```
mybranch=jb_somebranch
git co ${mybranch}
git branch -m ${mybranch}_unsquashed    # rename original branch to be squashed
git co master
git co -b ${mybranch}   # new branch with original name to merge into
git merge --squash ${mybranch}_unsquashed
git diff --staged
git commit -a -m 'some macro comment'
```

## New project

- existing files (with README.md) are in a current dir
- create new remote repo
```
git init .
git commit -m 'initial empty commit' --allow-empty
git add .
git commit -m 'first commit'
git remote add origin git@github.com:lqueryvg/some-project.git
git push -f --set-upstream origin master
```

## Misc

```
git submodule update --init --recursive
git merge-base branch1 branch2              # find first common commit
```

## Specifying commits (gitrevisions)
```
{ref}^     # first parent
{ref}^2    # second parent (assuming there are 2 parents)
{ref}~1    # first ancestor === same as {ref}^1
{ref}~2    # second ancestor === first grandparent === {ref}~1^1
{ref}~1^2  # second parent of first ancestor === second grandparent
```

Examples:
```
G   H   I   J
 \ /     \ /
  D   E   F
   \  |  / \
    \ | /   |
     \|/    |
      B     C
       \   /
        \ /
         A

A =      = A^0
B = A^   = A^1     = A~1
C = A^2  = A^2
D = A^^  = A^1^1   = A~2
E = B^2  = A^^2
F = B^3  = A^^3
G = A^^^ = A^1^1^1 = A~3
H = D^2  = B^^2    = A^^^2  = A~2^2
I = F^   = B^3^    = A^^3^
J = F^2  = B^3^2   = A^^3^2
```

## Workflow

Most of these rules are based on...
https://git-scm.com/docs/gitworkflows

### Definitions

| Term | Definition |
|------|----------|
| Feature (aka topic) branch | Temporary branch used to develop new feature or fix.
| Public branch | Any branch which has been published and is in-use or controlled by a group of people. Examples are central or integration branches.
| Private (aka personal) branch | A branch controlled by one person (but visible to others, so not intentionally not really "private" at all). For example, feature branches where only one developer is working on the branch.
| Downstream | From parent branch to child, or more stable to less stable, e.g. master to feature.
| Upstream | From child branch to parent, or less stable to more stable, eg. feature to master.

### Goals
- one commit per feature in central branch

### Rules

#### Public Branches

- **DON'T** re-write commits (i.e. rebase / squash) on a **public branch**
  - causes duplicates
- **DON'T** commit directly to public / integration branches

#### Private Branches

- **DO** create a branch for each feature
  - feature branch should contain a single, well separated change
- **DO** commit & push often, but this rule applies **only to private
  branches**
  - reasons:
    - backup
    - if you die, others can continue
  - don't be afraid of making too many commits:
    - work doesn't need to be complete before you commit / push
    - you have full control of the private branch, so you can always edit /
      squash the commits later
    - it's easier to combine commits than to split them
- **DO** regularly rebase a **private feature branch** but only relative
  to the branch it came from
  - this will keep you abreast of other changes being developed by the team
    and make the ultimate merge painful
  - if you rebase on a different branch you are likely to mix other changes
    into your feature branch

#### Publishing Changes

- **DON'T** rebase a feature branch if it has already been merged to a public
  branch
- **ONLY** merge features upstream (not downstream)
- **DON'T** merge features downstream
  - example: don't merge from integration branch into feature branch to pickup
    latest work
  - reasons:
    - feature branch will other changes from the integration branch and will no
      longer contain a single, well-separated change
  - rebase (see below) or cherry-pick instead
- **DO** *merge* features upstream, once *complete* and *fully tested*
- **DO** delete feature branches once the work is complete and it has been
  fully merged upstream (into it's final destination branch)

#### Throw-away integration branches
- **DO** use a throw-away integration branch to test if multiple features
  work together
- **DON'T** base any work on a throw-away integration branch
  - i.e. DON'T merge or rebase from the throw-away integration branch
- **DO** delete throw-away integration branches after testing
- **DON'T** merge a throw-away integration branch upstream


Notes:
- features should graduate from less stable to more stable branches
- JOHN only rebase a feature branch on the branch which it came from
