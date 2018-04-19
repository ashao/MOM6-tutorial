# Community development of MOM6
## Introduction
MOM6 uses the git versioning control system to track changes in source code over the course of development. Github is an
online service that accepts git repositories and facilitates common tasks when developing applications like MOM6
including merging changes and issue tracking. Centers (e.g. NOAA's GFDL) are responsible for maintaining their own
regression testing and organizing their own repositories. dev/master is always intended to synchronized across centers
with a frequency of about 1 month or as needed. Conflicts (code clashes and/or changes to answers) are dealt with at
this time of synchronization. Individual users who are not affiliated with a specific center should contact the
development leads at a given center to handle their changes.

## Synchronizing code between centers


## Git terms
Note: the use of copy when describing git does not correspond to its normal definition because git version controls by
differences.
* **Repositories**: The actual project containing the code and git version controlling. Repos contain *branches*
    * **Local**: The local repository stored on the computer you're working on
    * **Remote**: The repository stored somewhere else (i.e. GitHub)
        * **origin**: The default name when cloning a remote, can be renamed and/or otherwise modified
    * **Fork**: A type of a 'copy' of a repository. Changes are not fed back to the trunk
* **Branches**: A `copy' of the source code; git tracks changes along branches. Branches contain `commits'
* **Commits**: Commits contain the actual changes to the code and contain a short message and are identified by a unique
  hash

## General workflow for developers at GFDL
### Initial fork
GFDL's 'center repository' is at https://github.com/NOAA-GFDL/MOM6-examples.git with the specific MOM6 repository at
https://github.com/NOAA-GFDL/MOM6.git. Users develop by forking these repositories onto their own accounts. By 
following the links to those repositories to the center repositories and clicking 'Fork'.
### Cloning into a local repository
Issue the following command to clone the remote GFDL repository to your computer
```
git clone https://github.com/[username]/MOM6.git
```
replacing [username] with your GitHub account name. This repository will be stored under a subdirectory called `MOM6'
wherever the command was issues. The remote `origin' points to your fork. To add another remote called to the GFDL
repository issue
```
git remote add gfdl https://github.com/NOAA-GFDL/MOM6.git
```
### Doing development on your own branch
First checkout the dev/gfdl branch (which should still track the gfdl remote despite being forked on your own account) using
```
git checkout dev/gfdl
```
and ensure that it is up-to-date by issuing
```
git pull
```
Now create a branch from dev/gfdl
```
git checkout -b <branchname>
```
