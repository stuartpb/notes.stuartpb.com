# old odd recommendation on footlink names in markdown

Note that this note is a relic, pruned off from [the original bagtent ramble](jgsdh-qj8br-mk8vj-z8xmy-xmhgs), where you may find a context where it makes more sense

It's something you can consider, but for Bagtent, this doesn't necessarily jibe with any current or planned tooling or structures.

Really, most Bagtent links these days don't even need

See [these thoughts on UUID alternatives](c4g5h-7tqnh-8j8gd-eaqvs-sxh8j) for better ways to construct this (unnecessary) namespace

## the idea

link names should be treated as more or less portable between documents, which is to say that there shouldn't be any words that are used consistently to refer to different documents in different contexts (like `[back]:` pointing to the "previous page", for some kind of sick cheap templating scheme). Doing stuff like that screws with the tooling heuristics, not to mention it makes it harder to move content around.

the one exception to this is links by number, which have all kinds of special handling in the tooling
