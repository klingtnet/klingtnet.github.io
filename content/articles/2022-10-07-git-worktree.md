```json
{
  "author": "Andreas Linz",
  "title": "Git Worktrees",
  "description": "Git Worktrees are a lesser known utility of git and I want to show how to use them and also motivate why I think they're useful.",
  "created_at": "2022-10-07"
}
```

# Git Worktrees

At work I often face the situation that I want to locally review some feature branch of a colleague.
In general that's easy, one `git switch --track origin/branch-name` later and you have the changes in your local repository.
However, most of the time I was currently working in my own feature branch where I had unstaged changes.
This means I needed to commit my local changes before I could switch to the colleague's branch.
Wouldn't it be nice if I could work on multiple branches of a repository at the same time?

You might be tempted to say, I know how to do that, just make a second clone of the repository and call it a day.
That would work, of course, but it'd also be very wasteful since the whole repository needs to be cloned twice.
For large repositories this could mean duplicating hundreds or thousands of megabytes.
But guess what, git got you covered!  There's a lesser known command that handles exactly this use case, it's [git worktree](https://git-scm.com/docs/git-worktree).

There's a variety of ways how to use git worktrees.

## Existing repository

Let's assume that you're in some repository that you cloned a while a go and now you want to switch to a different branch without having to commit your local changes.
We can do that by creating a new _worktree_ like this:

```sh
$ git worktree add ../repository.feat-x origin/feat-x
```

This will create a copy, which shares the same `.git` data, in `../repository.feat-x` at branch `origin/feat-x`.
It's sensible to not add the worktree inside the existing repository because then you would need to ignore the worktree directory.

Checking out a new branch when creating a worktree is also very easy, just pass `-b my-branch-name`, that's it.

If you're done with your work you can remove your worktree like 

```sh
$ rm -rf ../repository.feat-x
```

or run

```sh
$ git worktree remove repository.feat-x
```

instead.
But `git worktree remove` should be preferred, otherwise some metadata will not be removed until a `git gc` or `git worktree prune` was run.

All worktrees of a repository can be listed with `git worktree list`.

## New repository layout

On [lobste.rs](https://lobste.rs) I found a very [appealing idea](https://lobste.rs/s/r5c6o8/git_worktrees#c_opoqjn) that I will try the next time I need to clone a repository.
They suggested to do a bare clone, that means only fetch the `.git` folder and then add worktrees for the branches you need.
I think that's best shown at an example.
Let's say we want to patch something in the Go compiler on `master`, `1.19` and `1.18`:

```sh
$ git clone --bare git@github.com:golang/go go/.git
cd go
git worktree add master
git worktree add release-branch.go1.18
git worktree add release-branch.go1.19
```

This will create the following directory structure

```
go
├── .git
├── master
├── release-branch.go1.18
└── release-branch.go1.19
```

where each directory contains the repository at a different branch.


For work projects I could imagine a directory structure that looks similar to this:

```sh
go
├── .git
├── work
├── experiment
└── review
```

Note that each worktree just acts like a copy of the repository, so I can switch to whatever branch I like, i.e. the worktree is not pinned to the branch it was created with.
In the example above I would use `work/` for my local feature development, `experiment/` for trying out some ideas, and `review/` whenever I need to checkout and review some feature of a colleague locally.

Let me know what your experience with git worktree is and if and how you use it.