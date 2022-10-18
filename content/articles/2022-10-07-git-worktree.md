```json
{
  "author": "Andreas Linz",
  "title": "Git Worktrees (Update)",
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

**Update**: In the [previous version of this article](https://github.com/klingtnet/klingtnet.github.io/blob/c800968678ffda04ec8a0cab04f28b7774dcefef/content/articles/2022-10-07-git-worktree.md) I used a [bare repository](https://git-scm.com/docs/git-clone#Documentation/git-clone.txt---bare) but this will lead to problems since this type of repository is not intended to be updated or track remote changes.  I did found another simple solution that I will explain below.

Let's say we want to patch something in the Go compiler on `master`, `1.19` and `1.18` branches.
We can setup the project like this:

```sh
$ mkdir -p go/worktrees
$ cd go/worktrees
$ git clone git@github.com:golang/go master
$ cd master
# add the first worktree
$ git worktree add -B release-branch.go1.19 ../go1.19
# and the second worktree
$ git worktree add -B release-branch.go1.18 ../go1.18
```

What we end up with is a project structure like this

```sh
$ tree -L 2 go
go
└── worktrees
    ├── go1.18
    ├── go1.19
    └── master
```

where each directory below `/worktrees` contains the repository at a different branch.


For work projects I could imagine a directory structure that looks similar to this:

```sh
go
└── worktrees
    ├── master
    ├── feature
    └── review
```

Note that each worktree just acts like a copy of the repository, so I can switch to whatever branch I like, i.e. the worktree is not pinned to the branch it was created with.
In the example above I would use `work/` for my local feature development, `experiment/` for trying out some ideas, and `review/` whenever I need to checkout and review some feature of a colleague locally.

Let me know what your experience with git worktree is and if and how you use it.