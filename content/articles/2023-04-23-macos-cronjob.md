```json
{
  "author": "Andreas Linz",
  "title": "How to setup and debug a cronjob in macOS",
  "description": "A macOS installation comes with cron, so a regular can setup cronjobs.  How to setup a new cron job and how to debug a failing one will be explained in this article.",
  "created_at": "2023-04-23"
}
```

# How to setup and debug a cronjob in macOS

I was surprised to see that macOS comes with cron pre-installed.
A cron job is basically a recurring scheduled task, e.g. a script or command that is run every Tuesday on 2pm.
To check if there any existing cron jobs run:

```sh
$ crontab -l
crontab: no crontab for alinz - using an empty one
crontab: no changes made to crontab
```

Let's say I want to commit my [Obsidian] notes once a day.
First I write a small script that contains the commit logic before I setup the cron job.

```bash
$ cat <<"EOD">~/.local/bin/commit-obsidian-notes.sh && chmod +x ~/.local/bin/commit-obsidian-notes.sh
#!/usr/bin/env bash

set -eu

pushd ~/Documents/Obsidian
git add --all
git commit -m "Obsidian notes from $(date -Idate)"
popd
EOD
```

Note that the script above contains quite a bit of bash magic.
It's perfectly fine to write the script file and set the executable flag in two separate commands.
Also, if you quote the delimiter in a `HEREDOC` statement, like `"EOD"` in the example above, then no evaluation happens in the content.
Otherwise the `$(date -Idate)` would've been evaluated to 2023-04-23 in the script.

Since we have the script our only remaining step is to setup the cron job.
Cron's syntax is a bit intricate and I'd recommend to use an online tool like [crontab.guru] to define the desired cron schedule expression.
Running the job every day at 8pm can be expressed as [`0 20 * * *`](https://crontab.guru/#0_20_*_*_*).

To define the job run `crontab -e` and enter `0 20 * * * ~/.local/bin/commit-obsidian-notes.sh`.
But there's something missing which will make the cron job fail.
Cron job errors will be reported to your local mail account, yes there's a local mail account for your user account.
To read those mail just run `mail` in a terminal.
You'll see the following error:

```
~/Documents/Obsidian ~
fatal: Unable to read current working directory: Operation not permitted
```

We need to give `/usr/sbin/cron` full disk access in order to fix the issue.
Please let me know if there's a way to restrict access of `cron` just to a particular directory.
Open `system settings.app`, then _Privacy & Security_ and under _Full Disk Access_ click the `+` button at the bottom to add `/usr/sbin/cron`.
Note that you can use `Cmd+Shift+G` to enter a particular location in the file select dialog (this also works in Finder).

Et voila, you now know how to setup and debug a cron job on macOS.

PS: If your git is configured to automatically sign a commit add `--no-gpg-sign` to the `git commit` call or otherwise the cron job will fail because it cannot unlock your signing key.

[Obsidian]: https://obsidian.md
[crontab.guru]: https://crontab.guru