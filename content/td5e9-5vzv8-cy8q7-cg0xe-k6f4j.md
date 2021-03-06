# How I'll Refactor Plushu

see also [Refactoring plan](28zkr-y787j-q3a05-82cbq-d7fe1)

the git content will be forked to module-tree/plumbus

plushu, the pluggable shell utility, will keep its current organization, but will only keep the help plugins and mushu modules

the plushu repo will stay in the plushu org, which will only contain a few repos dedicated specifically to the plushu tool (like plushu-commands, to call commands at the end of a plushu cascade)

plushu-plugins-plugin will become mushu-modules (a Module Tree module compatible with Plushu that adds the repo-pulling functionality also used by Plusku)

the old hacky cut-a-docker-image-for-each-release system for running apps will be taken out to its own little "pit" (for all the "container orchestration" it did) and left to rot

really, I think all the app-management stuff should got in Pit, since this level of app management...

can it be plugged into helm? I mean, not really, because a lot of this design is predicated on files in the filesystem representing the app state...

maybe they'll become the "pit modules" for implementing a Heroku paradigm in the local filesystem, and then plusku / herokube

I'm thinking the proper plusku can work with "herokube" as some kind of adapter

but I'm thinking the plusku core will be the pit-api-compatible interface to Kubernetes, for hooking into from the Git repo

the nginx configuration related stuff, I think, if it can be factored out cleanly, deserves to be its own kind of module (one that could even power some kind of ingress server implementation!) - maybe it's just the "takes the location of an app and adds a route to it to nginx"

new implementation for plumbus-stream is, taking in the list of files, `bash -c $(printf '%q |' $(sorted-by-filename-wildcard))"' cat'`

also, for plushu, I've decided that "subcommands" will die. commands just have a flat namespace. (if windows can't handle colons, you can implement some kind of substitution step.) the git receiver only ever implemented two commands.

- dumb idea, ignore this
  - and they can be separated by a level of priority-prefixed directories
- if the user's installed two conflicting instances of a command, that's on them to resolve by choosing to number-prefix the fixed-high-priority ones
- mushu can even check and warn about this
- if you want something with conditional/fall-through priority, you do that with a plumbus stream, behind an interface defined by 1 command/module
  - though this is like the one kind of Module Tree application that can decide to conditionally override modules
    - maybe we can have a rule that, for the purposes of this (which I guess is still Plushu), modules *are* allowed to inspect other modules to make decisions
    - however, they MUST NOT make their decision based on the name of the script, it MUST be by content
    - so this can be for like "disable anything that would need Ruby"
    - or... can it insert alternate processors by dropping them into the cascade

maybe they can be "bin" instead of "commands", to underscore how they work a little better (and what plushu really is)

okay, right, I was thinking this should be a class of plumbus line, and "plushu" would just be one invocation of it

maybe this is "plumbus-cascade", and it operates on numbered "steps"?

or, rather, I think "relays" makes more sense, since that captures the "must hand off execution to next step" aspect

I feel like this is a much cooler way to handle input

note, I like "plumbus-line" better than "plumbus-event", especially if it's now defined to echo each line to each file

was my "take app name early parameter" thing a good idea? can SSH not set env vars?

oh man, a fucking insane idea for how to handle early vars: a series of preprocessor-scripts where each one takes the name of all the next scripts to run, and then *execs into* the next one, so that's how we set env vars and get back where we started to run the command

- so preprocessing is done by searching for the PLUSHU_COMMAND (ie $0) in $@, and then you can rewrite whatever you want
  - the presence of PLUSHU_COMMAND in the environment is taken to mean that the first arg is the command
  - you know what? this can be implemented by each module implementing a numbered hook under something like the `commands` interface Dokku had
  - and they can figure out their own command-or-argument-parsing-or-stripping logic
  - and that's the level `plushu` calls (maybe the directory is called `plushu` because they're the ones that govern core function), and it has its own `99_find-commands`
  - maybe it also ends everything with `true` so ultimately an endless-exec case will just gracefully finish
    - and the "plushu command handler" level can strip that off?
    - the thing is, all commands need a way to know where the "args" start
    - is that where you insert the bare `true`, because you know true will safely ignore its arguments?
    - and then that way Plushu doesn't need to
    - (yes, remember, numeric prefixes in Module Tree are sorted numerically)
- maybe that's just the gig, if you're a command handler then work like a shell script that ends with `exec $@`

this also gets us a nice simple module to remove the added `-c` when called via ssh instead of the wrapper script, I think

oh man, I'm thinking now, since this is now basically just a shim for $PATH...

no, that's a terrible idea, you don't want to jerk with the core command namespace like that, don't want that environment getting handed down

it's important to note that, for hooks/filters, there is NO expectation of script name uniqueness across modules (implementations MUST NOT assume uniqueness) - filenames beyond the number are to be treated as comments, and the same number can have the same comment for two implementations, that's fine (they didn't even have filenames, in the early days of Plushu development)
