# roughing it in the Notes Workspace

Continuing from yesterday, in the new notes workspace.

worth noting I sent the wedding postponement email today so I'm not procrastinating on anything by working on this

Wanted to make this page, so I ran `. bin/setup-shell`, then realized, oh right, I need bagtent as a sibling.

Ducked out and did `git clone https://stuartpb@github.com/stuartpb/bagtent.git`, then redid the setup-shell

Running `bagtent-conjure --open` gives me this error:

```
Unable to create directory /home/coder/.local/share/nano/: Permission denied
It is required for saving/loading search history or cursor positions.

Press Enter to continue
```

And then opens the new file in nano, then prints the filename after exiting.

Oh. Duh. Right. There's no `code` binary here.

## doing without

I just use the auto-touch behavior of `bagtent-conjure` (which really should be moved out to `--touch`, which is not mutually exclusive with `--open`, which really should be called `--edit` where `--open` can be reserved for "specify the app name")

## well that's not great

trying out

## also woah wait what?

One of the UUIDs in this commit (oh shit, it's the one for this page!) isn't a UUID at all?

this wouldn't break the Bagtent model (like Couch, it treats names as arbitrary and is okay with whatever mixed-up schemes you want), but it *would* be weird to *leave* committed. Moreover, it's worth figuring out how this happened (did I accidentally backspace something?)

oh snap, I definitely did...

let me look at where this is linked

hmm, it's missing from there, too... I'm just gonna assume the missing character was "a", fix the link, and then

`mv content/1558a75c-b543-452d-119-d584595479ab.md 2ncae-q5n8d-9da26-enghc-n8ydb`

pretty sure this was a propagated typo... but it might be worth exploring if my mkuuid doesn't have a bug... can the `rand()` in `substr("89ab",rand()*4,1)` ever resolve to 1? does this need to be `ceil(rand()*4)-1`?

TODO ensure no other files in this repo have that issue (in which case the missing letter should be "b")

hmm... it looks like `b` is significantly rarer in these filenames than the other 3? yeah definitely worth analysis at some point, that's a characteristic leakage vulnerability

## more on awk's rand

https://www.gnu.org/software/gawk/manual/html_node/Numeric-Functions.html#DOCF44

> The C version of rand() on many Unix systems is known to produce fairly poor sequences of random numbers. However, nothing requires that an awk implementation use the C rand() to implement the awk version of rand(). In fact, for many years, gawk used the BSD random() function, which is considerably better than rand(), to produce random numbers. From version 4.1.4, courtesy of Nelson H.F. Beebe, gawk uses the Bayes-Durham shuffle buffer algorithm which considerably extends the period of the random number generator, and eliminates short-range and long-range correlations that might exist in the original generator.

Well, let's just run `awk -W version 2>/dev/null` and see what we're dealing with here...

`mawk 1.3.3 Nov 1996, Copyright (C) Michael D. Brennan`

FUCKETY SHIT, DEBIAN.

All the more reason for us to bundle a proper `bagtent-mkuuid`: it can use `cat /proc/sys/kernel/random/uuid` as the UUID provider first, and then check for `mkuuid` (maybe we're on BSD or something), and *then* (while printing a warning) go for the `awk` version.

(It'd be nice if I could print a retraction for [my comment suggesting this buggy implementation](https://serverfault.com/questions/103359/how-to-create-a-uuid-in-bash#comment1210345_799198), or even just delete it)

## more random side thoughts

bagtent-uuid should absolutely be a component and the default

if a user doesn't want to put bagtent stuff in the PATH, they can set an explicit ID command (like core `mkuuid`) in .bagtentrc

## not a great sign

grepping for a basic string (`628b8`) in `content/*` took a really long time

doing it again is really fast, though, it finds the line I just wrote. maybe it's a buffer thing, weird whatever

I still shudder to imagine what running bin/validate would look like (is Node built into this image?)

## memory

I think the way I'm opening conjured files now resembles how I did it on C9

## trying to install the dependencies

running `sudo apt-get update` and now `sudo apt-get install nodejs npm`

```
coder@notes-code-server-6b744987d7-hfwln:~/notes/notes.stuartpb.com$ npm install
node: /usr/lib/code-server/bin/../lib/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by /usr/lib/x86_64-linux-gnu/libnode.so.64)
node: /usr/lib/code-server/bin/../lib/libstdc++.so.6: version `CXXABI_1.3.9' not found (required by /usr/lib/x86_64-linux-gnu/libnode.so.64)
```

ah. and `sudo apt-get upgrade` finds `0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.` even `sudo apt-get dist-upgrade` yields the same thing

https://github.com/cdr/code-server/issues/1742 hey yeah, that's weird, now that I look at it why does code-server bundle its own libs and put them in, what, `LD_LIBRARY_PATH`?

wow, the output from `env`:

```
SHELL=/bin/bash
KUBERNETES_SERVICE_PORT_HTTPS=443
NOTES_CODE_SERVER_SERVICE_HOST=10.43.215.189
COLORTERM=truecolor
KUBERNETES_SERVICE_PORT=443
TERM_PROGRAM_VERSION=1.45.1
HOSTNAME=notes-code-server-6b744987d7-hfwln
NODE_OPTIONS=  --max_old_space_size=2048
PWD=/home/coder/notes/notes.stuartpb.com
NOTES_CODE_SERVER_PORT_80_TCP_PORT=80
NOTES_CODE_SERVER_SERVICE_PORT_TCP_80_8080_8JNTT=80
NOTES_CODE_SERVER_PORT_80_TCP_ADDR=10.43.215.189
VSCODE_GIT_ASKPASS_NODE=/usr/lib/code-server/lib/node
HOME=/home/coder
LANG=en_US.UTF-8
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
NOTES_CODE_SERVER_PORT=tcp://10.43.215.189:80
NOTES_CODE_SERVER_PORT_80_TCP=tcp://10.43.215.189:80
GIT_ASKPASS=/usr/lib/code-server/lib/vscode/extensions/git/dist/askpass.sh
NOTES_CODE_SERVER_PORT_80_TCP_PROTO=tcp
TERM=xterm-256color
VSCODE_GIT_IPC_HANDLE=/tmp/vscode-git-a874623654.sock
AMD_ENTRYPOINT=vs/workbench/services/extensions/node/extensionHostProcess
NOTES_CODE_SERVER_SERVICE_PORT=80
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
LD_LIBRARY_PATH=/usr/lib/code-server/bin/../lib
VERBOSE_LOGGING=true
KUBERNETES_SERVICE_HOST=10.43.0.1
KUBERNETES_PORT=tcp://10.43.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
VSCODE_GIT_ASKPASS_MAIN=/usr/lib/code-server/lib/vscode/extensions/git/dist/askpass-main.js
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PIPE_LOGGING=true
OLDPWD=/home/coder/notes
TERM_PROGRAM=vscode
VSCODE_IPC_HOOK_CLI=/tmp/vscode-ipc-9ec7dec4-7382-4f07-af7b-4eff513f3602.sock
_=/usr/bin/env
```

so then:

```
coder@notes-code-server-6b744987d7-hfwln:~/notes/notes.stuartpb.com$ LD_LIBRARY_PATH= npm install
npm WARN npm npm does not support Node.js v10.19.0
npm WARN npm You should probably upgrade to a newer version of node as we
npm WARN npm can't make any promises that npm will work with this version.
npm WARN npm Supported releases of Node.js are the latest release of 4, 6, 7, 8, 9.
npm WARN npm You can find the latest version at https://nodejs.org/
npm notice created a lockfile as package-lock.json. You should commit this file.
up to date in 0.436s
```

oh right. the install needs to happen in bagtent. yadda yadda yadda, `added 49 packages from 37 contributors in 14.71s`

## tweak I wanted to make yesterday

changed the remote URL via editing the config file to https://stuartpb@github.com/stuartpb/notes.stuartpb.com.git

ought to figure out how I'll configure Git - maybe another ConfigMap?

## another thought

I might make an SSH key for myself in this cluster (and then maybe move the STPUF key to my PocketC.H.I.P for safekeeping), but I'd passphrase-protect it. That wouldn't be much of a usability improvement over HTTPS, but it'd be more secure (unlike including a non-passphrase-protected key) - effectively requiring two layers / factors of authentication

## anyway

I can validate with `LD_LIBRARY_PATH= bin/validate`, so I think I'm gonna hook up all my pages and cut this commit.

doing `git config user.name "Stuart P. Bentley"` and `git config user.email "s@stuartpb.com"`

also I've made some tweaks to Bagtent (reworking the conjure arg, implementing mkuuid), but I should have those pushed soon
