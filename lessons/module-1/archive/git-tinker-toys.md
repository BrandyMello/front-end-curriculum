---
title: Git Under The Hood aka Git With Tinker Toys
length: 120
tags: git, github
---

## Vocab

- `Repository` A collection of code managed by git. Tends to be the folder your source files are in
- `Commit` A snapshot of what your code looked like at a particular time
- `Branch` A list of commits, one after another, one based on the next
- `Working Area` Where unstaged files live. Untracked files are not in the working area
- `Staging Area` Where staged files live
- `Untracked Files` Files that are not managed by Git

## Git, but more in depth

At this point, we have talked about Git from a few different directions. We started with an introduction to Git when implemented locally on our machines. Then, we introduced Github.com, and learned how to leverage the power of Git's version control system to push our local repos up into a public repository that other developers can access.  

Today, we are going to dive a little deeper, and also do some significant review, of what exactly is happening with Git under the hood.

This will help us get a clearer idea of why we use the commands we do, and how at the end of the day Git isn't quite as scary as `FATAL DETACHED HEAD` makes it out to be.  

We will also be using Tinker Toys today to help visualize how our repository tree is created as we navigate through the Git commands.  

## Start
There are two main ways to tell Git to start tracking a project.
1. `git init`  
2. `git clone git@github.com/username/repo`  
*[Tinker Toy Stage: A large X to symbolize our initialized repository.]*  

The git repository at this state looks like a whole lot of config files, but this folder is where ALL of your repository lives (or, in our case, will eventually live). That's the whole thing! In your project folder, run the command `tree .git/` to see the files currently hanging out in this 'secret' file.  

![git-init-file-structure][git-init-file-structure]

## Do Stuff
To actually make changes to your code and then tell Git to care about them, we need to start "making commits" to our repository. Recall that each commit is like a tiny version of "file save" back from when Microsoft Word was a thing.  

Let's create an empty `index.html` file. Then:  

1. `git add`  
  - This command actually writes your file, or changes, to the repository and creates a new node in the "staging area" of your git-world.  
  *[Tinker Toy Stage: Add a red dowel with a circular connector to the middle of the X]*
2. `git commit -m "Initial commit"`  
  - Takes what was written to your repository, and creates a "commit object", or node, with an assigned ID.  
  - This **commit node** now has **references** indicating what branch you are on, and where you have checked out that branch.  
  *[Tinker Toy Stage: Twist circular connector part around to show ID and attach the label of `HEAD` and `MASTER` GREEN.]*  

  (Note here that on the tinker toys, GREEN indicates we are currently checked out on that branch, ORANGE indicates the branch exists but we are not checked out to that branch.)

![git-add-file-structure][git-add-file-structure]  

![git-commit-file-structure][git-commit-file-structure]


## Make Changes
Let's say we now make more changes to our codebase, like putting in the boilerplate HTML code in that `index.html` file.  
When we repeat this procedure, git knows that we have added an additional commit to our chain of events.
1. `git add`  
  - *[Tinker Toy Stage: Add another dowel and circle node]*
2. `git commit -m "Another terrible commit message"`  
  - *[Tinker Toy Stage: Dowel/Circle gets an ID, and the Head/Master flags get moved up to this new commit object, `MASTER` is on the GREEN side bc we are checked out on this branch]*  


## Creating A New Branch  
Typically we'll create a new branch when we want to start working on a new feature, but that part of our code is not ready for production.

1. `git branch feature-a`  
  - *[Tinker Toy Stage: Attach a small dowel and a new flag with `FEATURE` showing the ORANGE side]*  

At this point, we have not navigated TO this branch, we have simply told git to create a reference to a new part of our repository tree. To actually navigate to this branch to add code, we'll need to run the checkout command, which tells Git to make commits to THIS branch instead of Master.  

![git-branch-feature-file-structure][git-branch-feature-file-structure]

2. `git checkout feature-a`  
  - *[Tinker Toy Stage: Flip over `FEATURE` card to the other side so that it is GREEN, flip `MASTER` to be ORANGE, move `HEAD` to this new node]*  

## Making Changes to New Branch
When we add and commit to this new branch, we repeat the steps above, but this time our pointers move with our new `feature-a` branch.  

Let's create a css file with `touch styles.css`. You don't have to put any actual CSS in the file for now. Then, we commit our changes.  

1. `git add styles.css`  
  - *[Tinker Toy Stage: Add another dowel with a new node to write the changes to disc]*
2. `git commit -m "Create new styles"`  
  - *[Tinker Toy Stage: Move the Head and Feature flags up to the new branch node]*  
*Note: The HEAD will always point to what you have checked out.*  

![git-branch-commit-file-structure][git-branch-commit-file-structure]  

## What is a Git ID/Hash/Sha?  
Behind the scenes, Git is assigning each of our commits a unique identifier. This type of ID is often called a `checksum`. It's generated by running a bunch of data through an algorithm to create a hash associated with that exact set of data. If one piece of that data changes, an entirely new hash would be created.  

Example SHA:  
```shell
032a9fdc3cde226b25669490b3d86529759526
```  

In Git, this checksum is a hash generated from the following:  
  - Content of files
  - Author
  - Date commit was made  
  - Log of changes  
  - Previous commit  

## Git Merge Workflow
When working with other developers, you will rarely have the green-light to merge your code directly into the master branch. Instead, you'll be working off of feature branches, and your code will only be merged into master after significant testing and code review. That workflow looks something like this:  

1. Pull down any new code from the master branch  
2. Checkout a branch for `feature-a`  
3. Add code to feature-a  
4. Test new feature-a code  
5. Add and Commit new code to GIT on feature-a  
6. Pull in any updates from Master (repeat 3-6 if necessary)  
7. Submit PR for feature-a  
8. QA testing happens (maybe)  
9. Someone else merges your feature-a branch into master  

## Merging Branches  
For now, let's say that we DO have the green light to merge our code directly into master. (Once again, in general this is a strict no-go on a professional dev team).  

Make sure you've created a new file, or added new code, on the `feature-a` branch, and have already run the commands `git add .` and `git commit -m ...`.  

Lastly, to demonstrate how code gets written to disc in isolated pieces, run the command `ls` while you're on the `feature-a` branch. Notice we now have two files, `index.js`, and `styles.css`.  

1. `git checkout master`  
  - We need to check out our master branch before we can merge in new code,
  - Run `ls` here. Notice that we no longer see the `styles.css` file that we created on the `feature-a` branch. This is the code we want to merge into our master branch.
2. `git merge feature-a`  
  - Run `ls` again. You should now see both `index.html` and `styles.css`  
  - At the top of your terminal output, you'll see that we've merged in two different commits. `Updating 35c96bc...50d51ae`, with a `fast-forward`. This `fast-forward` bit happens because the branch we created is an immediate ancestor of the previous commit on the Master branch, so GIT can just slide the pointers up a notch to now point to the target code addition.    
  - *[Tinker Toy Stage: Move HEAD and MASTER flags up to FEATURE-A flag]*

![git-merge-command-output][git-merge-command-output]  

Take a minute to run the command `git log --graph --decorate --all`. This command lets you see a print out in a more visual way in your terminal.  


<!-- IMAGE FILE REFERENCES -->
[git-init-file-structure]: /assets/images/lessons/git-tinker-toys/tree-init.png
[git-add-file-structure]: /assets/images/lessons/git-tinker-toys/tree-add.png
[git-commit-file-structure]: /assets/images/lessons/git-tinker-toys/tree-commit.png
[git-branch-commit-file-structure]: /assets/images/lessons/git-tinker-toys/tree-branch-commit.png
[git-branch-feature-file-structure]: /assets/images/lessons/git-tinker-toys/tree-branch-feature.png
[git-merge-command-output]: /assets/images/lessons/git-tinker-toys/merge-command-output.png


## References

[Git For Ages 4 And Up](https://www.youtube.com/watch?v=1ffBJ4sVUb4)  
  - This is the original video where most of this material was pulled from.  
