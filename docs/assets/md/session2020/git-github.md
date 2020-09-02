## FAIRbioinfo for bioinformaticians

#### Introduction to the tools of reproducibility in bioinformatics

#### C. Hernandez^1 T. Denecker^1 J. Sellier^2 G. Le Corguill ́e^2

#### C. Toffano-Nioche^1

(^1) Institute for Integrative Biology of the Cell (I2BC)
UMR 9198, Universit ́e Paris-Sud, CNRS, CEA
91190 - Gif-sur-Yvette, France
(^2) IFB Core Cluster taskforce

#### Sept. 2020


# Introduction to code versioning


## Really need of a files history?

> “Most researchers are primarily collaborating with themselves,” Tracy Teal explains. “So, we teach it from the perspective of being helpful to a ‘future you’.”

![img1](./Images/FAIR_git_comic_phd101212s.png)


## Files history = good practice for reproducible research

> ”Rule 4: Version Control All Custom Scripts”

![img2](./Images/FAIR_10simplesRules.png)


## Version control

### Definition

version control, revision control, source control, or source code management: class of systems responsible for managing changes to files.

### Feature

Each revision is associated with a timestamp and the person making the change. Revisions can be compared, restored, and merged.

### Software

SVN, Git, Mercurial, GNU arch, etc

[wikipedia](https://en.wikipedia.org/wiki/Version_control)

### Revisions graph

![img3](./Images/FAIR_git_revision_graph_wikipedia.png)


## Git and GitHub

### Git

* will track and version your files
* enables you to collaborate with ... yourself
* open source license GPL (GNU General Public License)
* created in 2005 by Linus  Torvalds for the development of the Linux kernel

### GitHub

* stores your repositories online
* enables you to collaborate with others (and yourself)
* first commit in 2007 by Chris Wanstrath, founded in feb. 2008, Microsoft Corporation still 2018


# Git


## Concepts, objects

* working directory: a user private copy of a whole repository of interest
* staging area: list of files of the working directory that will be considered for next commit (ie. could be not all the modified files)
* clone: a local copy of a repository (include all commits and branches), the original repository can be local, or remote (http access)
* commit: a git object, the snapshot of your entire repository compressed into a SHA (also the command the saves changes by creating the snapshot)
* HEAD: pointer representing your current working directory. Can be moved (git checkout) to different branches, tags, or commits
* branch: a lightweight movable pointer to a commit
* merge: combines remote tracking branches into current local branch

[Tuto git 1](https://www.tutorialspoint.com/git/git_quick_guide.htm)

[Tuto git 2](https://www.powershellmagazine.com/2015/07/13/git-for-it-professionals-getting-started-2/)


## Setup


### Git configuration: if not yet done, tell git our identity

```
git config --global user.name <Your Name>
git config --global user.email <Your Email>
```

### Git repository initialisation

The initialisation (red arrow) is the creation of a .git repository:

![img4](./Images/FAIR_git_init.png)

3 ways to initialize a .git repository:

```
# inside an existing folder (possibly containing files)
git init
# create the folder ”myproject” +  initializes the .git subfolder inside it
git init <myproject>
# copy the existing git repository to
git clone </gitfolder/path> </new/path>
```

### Tracking file

git add and then commit commands for myfile.txt :

```
git add <myfile.txt>
git commit -m <"my reason">
```
![img5](./Images/FAIR_git_add.png)
![img6](./Images/FAIR_git_commit.png)

[source](http://swcarpentry.github.io/git-novice/fig/git-staging-area.svg)


### Git file states

Checking the file status:

```
git status
```

File goes from untracked to tracked state (init), unstaged to staged state  (add) and finally, to a committed state (commit).


# Git Exercise


## Objective

### 1st exercise

* access and configure git
* initialize a git repository
* create files in this repository
* use the basic git commands for tracking files changes (status, add, commit)

### 2nd exercise

* copy another repository from github (clone)
* use branching (branch) and merging (merge) to manage code changes


## Setup: objectives 1 & 2

### Git access by doker

```
docker run -i -t -v "${PWD}":/data continuumio/miniconda
```

### Git configuration

Global configuration (checkinguser.namewith:git config --list):

```
git config --global user.name ’Your Name’
git config --global user.email ’Your Email’
```

### Git repository initialization

On a new dedicated folder run:

```
git init # observe the .git folder (ls -la)
git status # find the current branch , "nothing to commit"
```

## adding files: objective 3

### create 2 files, check their git status: obj

```
for i in 1 2 ; do echo "file"${i}" text" > file${i}.txt ;
done
git status # observe list of untracked files
```

### add file1 to stating area

```
git add file1.txt
git status # observe the changing status of file1: untracked => staged
```

### change file1 text

```
sed ’s/text/text change/’ file1.txt > tmp ; mv tmp file1.txt
git status # observe the 3 states , why file1 appears in "to be commited" and also in "not staged for commit "?
```

## commit: objective 4

### stage all files

```
git add file1.txt file2.txt # all files
git status
```

### commit

```
git commit -m "1st commit + file1 change" # always add a message , use present time to explain the change
git status # all ok
```

So far, we have initiated a new project whose code is versioned by git: we have created files and all their successive changes were saved thanks to git.

We will now create a 2nd project by copying an already existing one. We’re going to bring this project from an online git project site,e.g. github.


## cloning: objective 5

### copy of a project: clone

To download a project from github, we use the gitclonecommand:

```
git clone https :// github.com/clairetn/FAIR_bioinfo_github.git

### observe result

* a new folder has been created (check with the shelllscommand)
* its name is directly deduced from the url used
* this FAIR_bioinfo_githubfolder contains a.gitrepository and also aREADME.mdfile (see withls -la FAIR_bioinfo_github/)
* it is a minimal project!

## branching: objective 6

We plan to change the README file by adding our firstname at the authors list. With a git versioning system, a good practice is to create a branch to reserve the initial code until we validate our change.

### create a branch nammed ”branch1”

```
cd FAIR_bioinfo_github
git branch branch
```

### list all branches

```
git branch # find the star
```

## branching: objective 6

### go into the new ”branch1”

```
git checkout branch
git branch # find the star
git status # find the branch
```

### work inot branch: change a file and keep change

Edit theREADME.mdfile and add your firstname to the ”Authors list”

```
git status # file README.md is modified
git add README.md ; git commit -m "add my firstname in branch1"

### return to master branch

```
git checkout master
more README.md # Is README.md modified or initial version?
```

## merging branch: objective 6

We have check that our change is valid, so we now plan to move it into the master branch.

### merge branch, then delete branch

```
git merge branch
more README.md # what README.md version?
git branch -d branch1 # -d for delete
```

# GitHub


## ”Who”?

### Quizz

* public institute (governmental)?
* semi-public institute?
* not-for-profit organisation?
* private company?

### Response

[See](https://github.com/about): Careers’ paragraph, you’ll see a ”company” word


## GitLab, a Github alternative?


## “What”?

### Quizz

* social network?
* desktop application?
* tool to create websites?
* stable repository to publish any file? 


### a social network
![](./Images/FAIR_github_exSocialNet.png)
### a desktop application
![](./Images/FAIR_github_desktop_dl.png)
### a tool to create websites
![](./Images/FAIR_github_pages.png)
### a stable repository ...
![](./Images/FAIR_github_popularity.png)
## ... to publish any file

Files for which git can calculate the difference between versions.

Usually txt files of reasonable size:

* R script: YES
* Python script: YES
* pdf file: NO
* fastq file: NO



# GitHub: main usage: sharing code with others

## GitHub
* so used that Microsoft was interested in it (boughtin june 2018)
* web-based: graphical interface + many more features than git
* git-based: git concepts and commands are retained
* commands for ”sharing”:git push origin master(local to remote) andgit pull origin master(remote to local):

![img git](./Images/FAIR_git_pushpull.png)
