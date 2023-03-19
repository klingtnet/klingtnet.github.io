```json
{
  "author": "Andreas Linz",
  "title": "Use your SSH key to sign git object",
  "description": "Since git 2.34 one can use an SSH key to sign a git object.  In this post I explain how to configure git to use an SSH key to sign a git object.",
  "created_at": "2023-03-19"
}
```

# Use your SSH key to sign git object

[Git 2.34] added support for signing git objects, like commits, using an SSH key.
Previously you could only choose between GPG and a X.509 certificate.
Managing GPG keys is quite tedious in my experience, and every time I need to extend one my keys I cannot remember exactly how to do it.
Lowering the bar to set up and maintain a working signing configuration could drive the adoption of git object signing, I think. 
Authenticating against a remote repository via SSH is probably the most common option, so most people who use git already have an SSH key available.
Hence, let's use that to sign your git objects!

Configuration is pretty straightforward, all you need to know is the filename of the SSH public key you want to use for signing.
On Linux you can just run `ssh-add -L` to list the public keys with filename known to your SSH agent.
However, on macOS you won't see filenames in the output.
If you're unsure check your `~/.ssh/config` or the `*.pub` keys inside `~/.ssh`.

After you've figured out which SSH key to use for singing just issue the following commands and you're good to go:

```shell
$ git config --global gpg.format ssh
$ git config --global user.signingKey ~/.ssh/some_ssh_key.pub
```

Note that `~/.ssh/some_ssh_key.pub` must be adjusted to your setup, e.g. `~/.ssh/id_ed25519.pub`.
There's also a [GitHub Docs page] about the configuration process, in case you still have any questions.

Here's how I check that git object signing works:

```shell
$ cd $(mktemp -d)
$ git init
$ git commit --allow-empty --gpg-sign -m 'A test message'
```

You're good to go if `git commit` exits without an error.

Happy signing 🗝️


[Git 2.34]: https://github.blog/2021-11-15-highlights-from-git-2-34/
[GitHub Docs page]: https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key#telling-git-about-your-ssh-key