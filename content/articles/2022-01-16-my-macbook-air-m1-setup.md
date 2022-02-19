```json
{
  "author": "Andreas Linz",
  "title": "My MacBook Air M1 setup",
  "description": "I recently made myself a present and bought a MacBook Air M1.  This is my first macOS machine and in this article I will let you know which productivity tools I use, what other software and how my basic configuration looks like.",
  "created_at": "2022-01-16"
}
```

# Preface

It was in the very early nineties when I first used a computer.  It must have been the 286 of my neighbor or the Amiga of my cousin.  A couple of years later, I think when I was in 4th grade, I got my first own computer.  A 486DX2-66 with 8 megs of RAM and a horribly slow 1GB SCSI hard drive, connected through an Adaptec ISA SCSI Controller card.  Not only was the hard drive slow as hell, maybe 1 MB/s at tops, it was also very noisy.  Annoyingly loud to be honest, like if it wrote every bit with a chisel into stone.  This machine ran MS-DOS and so I learned how to remove unnecessary drivers from `autoexec.bat`, like the mouse driver, in order to free up a couple kilobytes of RAM so that I can play DOOM or whatever.

Anyways, couple years later I got my first Windows PC, an AMD k6-2 with 333MHz that was equipped with the famous IBM Deskstar hard drive.  This hard disk was even noisier as my old SCSI drive while also being very unreliable, hence they were also known as IBM Deathstar.  Of course you can imagine how funny it was to ship the broken drive to IBM's service center somewhere in Great Britain, waiting for it to be returned so that I have a working PC again.  It took them over a month, or longer, to return the drive which meant that I had no computer in the meantime.  Actually, I didn't even know if they had received the drive or if it got lost in the english channel.  You just had to wait patiently in the good ol' days.  Unimaginable in today's world, people would get mad if they didn't receive an update every couple of hours.  Somehow people still knew how to be patient back then, and so was I, which meant that I had to spent my time with other things.  Internet was still something new and the time you were online could be counted in minutes a day.  This is probably one of the reasons for people being patient.  Having no smartphone meant that the ordinary folk still had an attention spans longer than a fart lasts, which means they could read a book or watch a movie without being distracted.  Hell, they even managed to meet each other to talk and have some beers (I do miss that since COVID).

Sorry, I was drifting away, thing is, I wanted to tell that I was a Windows user until I began to study computer science in 2010.  I slowly moved over to Linux then and used it ever since, which means I am a ~~penguin~~ Linux user for more than 9 years now[^1].  However, I always missed my favorite audio software in Linux, even though it got better in recent years with Wine, [yabridge], [Bitwig], and [pipewire].  Still, it isn't a lot of fun because there is so much configuration involved.  But not only that, every so often something breaks and after ten minutes fixing whatever has failed you give up and do something else, which is not music making.

For quite some time now I've been arguing with myself if I can justify to get a Mac and be done with it.  Some years ago this would have been a serious financial decision but now I earn enough so that I can just buy one if I want one.  This is a privilege and I'm well aware of that.  But trust me, I still know way to well what it means to have almost no spare money.

Maybe it's only me but computers should just work.  I happily pay the additional price if it means that I don't have to deal with bug trackers in my rare free time.  The mac solves exactly that for me.  It just works and I have (almost) the best of both worlds, all my favorite audio software as well as all the developer tools I need.  Except that docker sucks really big on Apple, but that's okay for me.  With the introduction of Apple silicon there is also less reason to keep on x86.  The M1 chips are the biggest performance impact since SSDs entered the home computer market.  I received an M1 MacBook Pro from my employee, the 2020 model not the M1 Pro, and the machine is great and almost as fast as my Ryzen 3700x desktop.  Of course, the touch bar is garbage and I understand why they ditched it with the new models.  Eventually I decided to buy myself a present and get the better and slightly cheaper alternative to the 2020 MacBook Pro, the fan-less MacBook Air.  This thing is fast, battery life feels endless it is always dead silent.  The fan in the Pro model is also off almost all of the time, but knowing that there isn't even fan inside is just mind blowing, regarding how noisy laptops commonly are.  For now I can just say that I 💚 this thing.

# My MacBook Air M1 Setup

Here's the list of things I installed on the MacBook Air:

First thing I did was to install Firefox with two extensions [multi-account containers] and [ublock origin] of course.  It should be prohibited to use a web browser without ad blocker.

Now it was time to get [Obsidian] so that I can take notes.  As for most tools I just use the default configuration, except that I have activated the daily notes extension.  This extension allows to easily create a page from a template for each day, where my template looks like this:

```md
# {{date}}

- placeholder

## Tasks

- [ ] a task
```

Another indispensable tool is [KeePassXC].  Honestly, I don't know most of my passwords and I don't care to since KeePass does that for me.

Having a password manager installed meant that I can lookup my Apple password to sign in into my Apple account.  Otherwise I could not install anything from the app store.  The mac app store is the same garbage as all the other ones and I think they are a bad idea.

Let's go on with another Mozilla tool that I use daily and this is [Thunderbird].  I could use Apple Mail but there is no way to only accept text mails with this fancy app, so I just stick with the ⚡️🕊.  Once in a while I send a GPG encrypted email, which is also a no go for Apples hipster mail client.

What really amazed me after years of Linux desktop usage was that CalDav (calendar) and CardDav (address book) is very well integrated into the system and it took me like 30s to connect it to my [Fastmail] account.  The calendar app is fine by the way.

Despite being no flat-earther I do like [Telegram] for it's thin desktop client.  They still know how to build a cross platform app that doesn't require to load a browser in the background.

Don't worry my dear reader, I also use [Signal], one does not rule out the other.

Admittedly, window management is very primitive on macOS.  I have tried tiling window managers on Linux but I didn't like them too much, probably because they required days of configuration until they were usable.  I'm simple in that regards and just wanted to have something like Aero Snap on Windows, that is tiling windows by dragging them to a screen border.  For macOS there was an app called Hookshot that was recently renamed to [Rectangle].  I payed for Rectangle App but I only use windows snapping.  Renaming an app from something easy to google to a generic term like rectangle is a questionable decision to begin with, but if you have already installed Hookshot and want to upgrade to Rectangle I recommend to uninstall it and power cycle the machine.  This is no joke, window snapping still didn't work after a reboot but after I powered the laptop on and off.  Don't ask, I would like to know as well why this was necessary (I don't refer to the renaming here 😅).

Another thing which annoyed me on macOS is that you cannot switch between windows of the same app using `<cmd>+<tab>`.  Some expert had the fantastic idea to hide this functionality behind `<cmd>+~`, hope that whoever did that is happy now.  Gnome replicated the same idiotic behavior but at least you could configure it.  Well, there is a great open source app called [alt-tab] for macOS which binds by default to `<option>+<tab>` and provides a sensible application switcher.

Time to install a code editor.  My weapon of choice is ~~GNU Emacs~~ [VS Code].  I try to keep the number of extensions minimal and use whatever is the default suggestion for a file type.  There is one additional setup to do on macOS that is to run `Shell Command: Install 'code' command in PATH` with `<ctrl>+P` so that I can start VS Code from within a terminal.

[Go] go go!

It breaks my heart but I also need to install [JetBrains IDEA] in the community edition because there still is [a Java repository I need to work on](https://github.com/klingtnet/bitwig-extensions).

I am a [IBM Plex] convert and now use [JetBrains Mono] as my monospace font of choice.

[Spotify], the billion dollar company that is unable to distinguish two artists with the same name.  The day I figure out how to properly handle ID3 tags with Go will be the day I cancel my Spotify subscription and remove their electron monstrosity.  Till then I will use their app and live with the limitation of 10k songs per playlist.  Oh, one additional thing, make sure to disable _Normalize Volume_, all it does is to make everything half as loud which is not what I understand of volume normalization but what do I know.

Once in a while I need to create or work with vector graphics and so I bought [Affinity Designer] while it was on x-mas sale two years ago.  I am by far no expert in this domain, but let me tell you, I would prefer this to Adobe Illustrator any time of the day.  Even without a discount, 55€ is more than fair for the software you get.  You could also buy a tube of [Supreme Toothpaste](https://www.supremenewyork.com/previews/fallwinter2020/accessories/supreme-colgate-toothpaste) for that money, depends on if you already gave up or not.

[MonitorControl] is another great piece of software.  Controlling the brightness of my external display like for the integrated one is a feature I always wanted.  For Linux there is an alternative called [ddccontrol](https://github.com/ddccontrol/ddccontrol) but it's not as neatly integrated as MonitorControl is.

[Rust], once my favorite programming language, is also something I use regularly, even though not as often as I should.  The language toolchain is best installed using [rustup].

VPN providers are often a bit shady, the one exception for me is [Mullvad].  They [support the development of Wireguard](https://mullvad.net/de/blog/2017/7/13/mullvad-donates-wireguard/) and they their client is open source.  Also, they go a long way in trying to use as less data as possible from you.  What other service provider allows you to register without entering any personal information?  I know none.

## Shell Setup

On Linux I used [alacritty] as my terminal emulator of choice but they don't notarize their binaries for macOS, which I can understand somehow.  Luckily there is [iterm2] which feels like a proper replacement.

I haven't adapted my [dotfiles](https://github.com/klingtnet/dotfiles) repository yet and decided to start from scratch with this system.  My `.gitconfig` is rather simple:

```ini
[pull]
	ff = only
[user]
	email = <redacted>
	name = Andreas Linz
	signingkey = <redacted>
[core]
	editor = vim

[alias]
	p = push
	co = checkout
	s = status
	c = commit
	a = add
	hash = rev-parse HEAD
	todos = ! git diff --name-only "$1" | xargs rg --no-heading --trim TODO
[init]
	defaultBranch = master

[url "git@github.com:"]
	  insteadOf = gh:
[commit]
	gpgsign = true
```

Coming from Linux the thing I miss most is a package manager, especially when you're used to something like `pacman` from Arch Linux.  The situation on macOS is not as bad as on Windows but at least we have [homebrew] and [MacPorts].  I went with homebrew because that's what everyone is using and despite its terminology where everything is named after thins in a brewery or bar.  Since GUI applications usually come with an auto updater I decided to only install CLI tools and libraries with `brew`.

With the URL rewrite from above's gitconfig I can easily clone repositories from GitHub by doing `git clone gh:org/repo`.  But there is [GitHub CLI] that can do much more, like managing issues, pull requests or watch running actions.  That's the first tool I've installed using `brew`, not because I use it that often, just because it was the first tool that came to my mind.

Shameless plug, [netdrop] is a tool that proved to be helpful.  It allows me to send a pipe over my local network, or just a file.  Most of my config files were copied like that.

My shell setup is pretty simple since a while.  On Arch Linux is used plain bash with a minimal configuration and a custom [Rust prompt renderer](https://github.com/klingtnet/dotfiles/tree/master/tools/rusty-prompt).  The default shell on macOS is `zsh` so I went with that.  Instead of my own prompt renderer I now use [starship] which is pretty powerful out of the box and requires almost no configuration.  This is how my `~/.zshrc` looks like:

```bash
HISTFILE=~/.zsh_history
HISTSIZE=999999999
SAVEHIST=$HISTSIZE

# setopt inc_append_history
setopt share_history
setopt hist_ignore_dups
setopt hist_ignore_space

alias ll='ls -alh'
alias cdtemp='cd $(mktemp -d)'

# autocompletion
# https://stackoverflow.com/a/58517668
autoload -Uz compinit && compinit -u

# https://stackoverflow.com/a/12403798
bindkey "^[[1;3C" forward-word
bindkey "^[[1;3D" backward-word

# ... shell completions were omitted.

eval "$(starship init zsh)"
```

Before I switched to [alacritty] I was using [tilix] [^2] because it supported terminal splitting, tabs and so on.  The reason for using alacritty was that it used the GPU for rendering and was much smoother.  The downside of alacritty is that it has almost no features (which some might see as a feature itself).  However, I now needed a terminal multiplexer and there is one obvious choice, that is `tmux`.  Learning tmux was pretty easy since I only needed to know a couple of keyboard shortcuts and it allowed me to use terminal multiplexing independently from the terminal emulator.  That's the reason I continue using it, even though `iterm` supports terminal multiplexing as well.

With the advent of Rust and Go a couple of modern alternatives for common shell tools came along.  One of those is [ripgrep], a replacement for `grep`, that is pretty fast also quite ergonomic since I save two characters each time I've to _grep_ for something.  Every time I used `find` I wondered if this tool was implemented in complete isolation because it CLI just feels weird, e.g. the single dash flags like `-name` and that the path to search in is the first argument, even before the flags.  An alternative is `fd`, which comes with an easy to use CLI and should provide all the features of `find`.  I didn't miss anything at least.  There is one more tool I want to tell you about and this is [fzf].  A fuzzy search tool that I mainly use to reverse search in my `zsh` history.

We are not finished here, so stay with me for two essential tools in my setup, [pandoc] and [imagemagick].  Whenever you need to convert from some document format into another, e.g. from `.md` into `.docx`, `pandoc` has you covered.  You can also mix document formats, it was a great relief to write [my masters thesis](https://github.com/klingtnet/master-thesis) in Markdown with some inline LaTeX here and there and let pandoc deal with the conversion into the obnoxious LaTeX syntax.  ImageMagick 🧙🏻‍♂️ comes into play when I need to convert between image file formats or I need to resize a picture.  Great tool but let me give you a tip, never expose it (or ghostscript) to the internet.  I heard the people do that, sigh.

The end is near, not of the world but of this article.  I am stubborn and refuse to deal with git integrations of any editor or IDE, instead I rebase, cherry-pick, commit and whatnot in the shell.  For this I need a proper editor and the obvious choice is ~~Emacs~~ vim, of course.  The older I get the more my configuration files are converging into standard settings.  My `.vim/vimrc` is only a couple of lines long:

```viml
" :help vimrc
" :options
" to unset use :no<option>
syntax on
set number		" set line numbers
set cursorline		" highlight line of cursor
set smartindent
set ttyfast		" indicate fast TTY connection, should improve scrolling behavior
set hlsearch		" highlight last searched word
set spell		" enable spellcheck
set ruler		" show cursor position in status line
set t_Co=256
filetype plugin on
filetype indent on
colorscheme gruvbox
```

I hope that was self explanatory.  A pleasant surprise was that `vim` has its own package manager since version 8.0[^3].  This means that plugin installation is dead easy now, just create a folder like `~/.vim/pack/personal/start` and clone all the plugins you need below the `start` directory.  In my case those plugins are:

- [ale] for language server protocol support
- [ctrlp] to (fuzzy) find files
- [vim-airline] for a nice status bar
- [vim-surround] to easily wrap strings in quotes
- [gruvbox] for some eye candy

The last thing I did was to setup GPG (PGP?).  I haven't used [sequoia] yet but I do hope that it comes with a sensible interface, in contrast to GPG.  Every time my key, and its subkeys, is about to expire I need to lookup how I can renew it.  Each time I do wonder why I need to _expire_ the key to renew it.  Anyways, I do sign my git commits and once in a while send and receive encrypted email, hence I need to live with GPGs peculiarities.  For [Thunderbird] I needed to export my private key into a file, otherwise it couldn't import.  Exporting a private key can be done with:

```sh
$ gpg --output private.pgp --armor --export-secret-key <fingerprint>
```

Make sure to delete the file afterwards.  A bit more complicated was to setup commit signing.  I found a bunch of articles that describe how to achieve that on macOS but they all had some problems so I will share my setup instructions below:

```bash
$ brew install gnupg pinentry-mac
$ git config --global user.signingkey <fingerprint>
$ git config --global commit.gpgsign true
$ grep pinentry-program ~/.gnupg/gpg-agent.conf
pinentry-program /opt/homebrew/bin/pinentry-mac
$ gpgconf --kill gpg-agent
```

## Audio Applications

I will tell you more about my audio software setup in part 2 of this article, the article is already long enough.

[MonitorControl]: https://github.com/MonitorControl/MonitorControl/
[yabridge]: https://github.com/robbert-vdh/yabridge
[Bitwig]: https://www.bitwig.com/
[pipewire]: https://pipewire.org/
[multi-account containers]: https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/
[ublock origin]: https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search
[Obsidian]: https://obsidian.md/
[KeePassXC]: https://keepassxc.org/
[Thunderbird]: https://www.thunderbird.net/en-US/
[Fastmail]: https://www.fastmail.com/
[Telegram]: https://telegram.org/
[Signal]: https://signal.org/
[Rectangle]: https://rectangleapp.com/pro
[alt-tab]: https://alt-tab-macos.netlify.app
[VS Code]: https://code.visualstudio.com/
[Go]: https://go.dev/
[JetBrains IDEA]: https://www.jetbrains.com/idea/
[IBM Plex]: https://www.ibm.com/plex/
[JetBrains Mono]: https://www.jetbrains.com/lp/mono/
[Spotify]: https://www.spotify.com/de/
[Affinity Designer]: https://affinity.serif.com/en-us/designer/
[Rust]: https://rust-lang.org
[rustup]: https://rustup.rs/
[Mullvad]: https://mullvad.net/en/
[homebrew]: https://brew.sh/
[MacPorts]: https://www.macports.org/
[alacritty]: https://github.com/alacritty/alacritty/issues/4673
[iterm2]: https://iterm2.com/
[GitHub CLI]: https://github.com/cli/cli
[netdrop]: https://github.com/klingtnet/netdrop
[starship]: https://starship.rs/
[tilix]: https://github.com/gnunn1/tilix
[ripgrep]: https://github.com/BurntSushi/ripgrep
[fd]: https://github.com/sharkdp/fd
[fzf]: https://github.com/junegunn/fzf
[panodc]: https://pandoc.org/
[imagemagick]: https://imagemagick.org/index.php
[ale]: https://github.com/dense-analysis/ale
[ctrlp]: https://github.com/kien/ctrlp.vim
[vim-airline]: https://github.com/vim-airline/vim-airline
[vim-surround]: https://github.com/tpope/vim-surround
[gruvbox]: https://github.com/morhetz/gruvbox
[sequoia]: https://sequoia-pgp.org/

[^1]: Time flies.
[^2]: Also the only popular D application I know.
[^3]: You can find the documentation by running `:help packages` in vim.