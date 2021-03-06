# grarg

https://github.com/stuartpb/grarg

Extension plans:

- Passing the parameters to the callback as a third argument, allowing for tricks like `end:0` to be set.
- A mutable state to pass to the callback as a fourth argument, that will be used as the return value for the function.
  - This will also allow the callback to be optional, defaulting to a function that collects pairs as a two-dimensional array, ie. for use passing to `new Map`.

## Thoughts on params.short

I'm glad I didn't make this a default like I'd been planning.

Short opts are really a special-case of parsing, and you might as well have special support for them in valid positions anyway.

## A better way

This should be implemented as an Express-style "use filter" parser.

Callbacks can return an array (or a Thenable that resolves with an array) to process args:

- Returning `[]` will remove the pair from the stream.
- Returning `[[a,b]]` will pass the pair along unchanged.
- Returning `[[a,null],[null,b]]` will split the param into two options.
- Returning nothing is only allowed if you're the last consumer.

Also, this'd let `short` and `end` be insertable filters.

Also, to avoid invoking Zalgo, there should be sync/async variants that disallow and enforce Promises, respectively. (I'm thinking the async can be avariant and the core can be )

`end` would need some way to signal "stop parsing equals as a splitter", which suggests that should be a separate option as well?

## The underlying primitive

A function that takes an array and an array of functions, then, for each item in the input array, calls each function in the filter array, calling the next items in the

It's a series of frames for a state. Is `zoetrope` taken? What about `bowler`? Or `crank` (because it's like how you an insert or remove frames by over/undercranking)?

Engine? Intestine? (Bowel / boweler?)

Taken:

- bowl
- alley

## In fact

I'm realizing now: this is essentially just Express routing, but sync. You can do opt parsing with `key` and `val` just like `req` and `res` (taking state env in as a third parameter, instead of on `req` or `res` like it's passed along in Express), but the third (or maybe even first) argument is the callback to continue to the next step

this also makes the difference from if you want to be an option parser, or a command system. Or switch it up and be either in different contexts, only going down some parse trees (never to return) when a certain state is hit (like X )

and if you don't tail-call into something else (...ooh, is that a potential issue, and why Promise just does wrappers around functions returning sync values?), the invocation of your function (if you choose it to work like an opt parser) is whatever the last function to return a non-undefined value returned

the idea of doing this thing where the return can be called (and maybe that'll just be the case anyway - really, what we do is we just have the "constructor" work like a special function if it wasn't called with new)

wait, shit, was this a thing with old-functions? and why they didn't

i'm wondering if something can be done with the "return the value of the next step versus don't"

okay so the generalization that I think I'm into with this would be that it's just one thing (and maybe an ongoing environment input) as return values

and for grarg-style parsing

okay right this is the reason why I wanted to do it as continuation callbacks

oh, and I get why request had "req, res, don't take next" as a way of defining a route

okay so this goes back to the idea of having a function that takes either a function or another value (like an array)

but, no, wait, using the return value doesn't work, because I want it to be able to split `[unrelated,words]` into `[null, unrelated]` followed by `[null, words]`

this "callable switching to take a function as an alias for `.use` idea" I think might be overkill a bit

anyway, you'd be able to pass in a handler constructed as `command('whatever', (opts)=>{})` that will send the rest of the parsing down

can the return from `command()` have the same signature as an instance of grargs? I think the base just needs to be a converter, but that's all it does, and then command parsers are expected to take in the rest of the arguments of the string as grargs-style pairs (and parse them according to whatever grarg-extension logic), then call the callback?

so the pipeline goes "function that pairs double-hyphen-followed-by-nonhyphen", then "function that splits equals values out of the first section", and then you could have a composite function from some kind of yargs-style "this key is this type" mapping that does the

okay, so one thing, you'd need to have a way to define it so that it'd only bail out to the hyphen-hyphen-stops-pairing path before continuing to the end...

yeah I'm not certain there's a safe way to say "this function has run its course from this data", is it just to pass `null` (meaning "I have decided to end the array here, as far as anybody after me is concerned")?

this is also starting to feel like plushu/plumbus streams, where not bothering to `cat` your input when it doesn't apply effectively cancels the consecutive filters (which is a cool way to do priority)

oh man... is an alternative to copying input for `plushu-event` that it'll just buffer a line of the file in memory, and then call all scripts? so that's a way to "batch" events?

I feel like this might have come up at some point in Plushu's issue history

(this prompted me to consider an app that can convert all of my/anyone's issues and comments in a repo/organization into Markdown, maybe a CLI)

(also prompted me to go back and make a note of this on the Module Tree project page)

I think the "returns an array of calls to the next part to make" (allowing no return for the last function) design is still the one that makes a lot of sense. Back to the `bowel` design.

(somewhere in this section I wrote [an aside about chain notation](6rkww-rq668-32adk-p7ev6-st7cn))

whatever I said about needing tail-call optimization was wrong - remember, the function always cedes its stack space to its return value, and that that is how it notates its returning it to the next step (by taking a callback that will take the returns from the previous step) is fine

its just that this means the previous function... does have to return? I think?

## anyway

one kind of short-arg-parser that might be cool to implement - and why I think arg parsing should be malleable like this - is Pacman-style "capital short arg defines the command with that first letter"
