# reverse-lookup model for reachability?

like, couldn't I just make a tool that, like bagtent-orphans, doesn't do fancy mdast parsing, it just does a grep on the noteball to see if any files have a reference to it... and then it does that recursively until it hits the root

(speaking of bagtent-orphans, and a name for this, I did a little brainstorming [here](1rae1-g9209-kk836-5xb3d-w38sq))

what, and each grep searches for "every UUID that was included in this file"?

the grep probably wouldn't be too bad if you grouped them by first byte, and maybe invoked 16 processes

and I guess you'd, like, maintain a list of files whose inbound link filenames have been grepped for, and only grep files that are linking we hadn't already reached

also, I really should introduce a proper linter that checks link viability on a selection of files

Should I just use xargs?
