# acebins idea

https://unusual.studio/projects/#598e8120-c062-43b0-a2d3-e645b62fd337

You have a space, and you can paste text into it, and you can pipe this text through nodes that will perform operations on it

it's kind of like a live Ed script

but, like, you can have smarter inputs. Like, you can paste in YAML/JSON/HTML/whatever as a source you're transforming from

This idea eventually turned into the [Abstract Pipeline Runner](amyvx-h7853-878ea-s47wv-a18p1) superclass

## prior art

Oh man... is Blockly the right UI for this? Does it have typing search to enter explicit / unknown tokens?

Does Blockly have wires? It must have Wires

Also kind of reminds me of Eleventy and its ilk a little? It's a similarity to consider

## text manipulation engines

JS, obviously

I honestly like Lua a lot though, and it'd be nice to have it (maybe via WASM?)

you could probably do other WASM scripting environments too, really

What'd be really cool would be to have the functions of these languages available as blocks with composable tokens

Ooh, what if captures produced an input with a line that could be plugged into a pipeline (ie. "Perform map lookup") to the replacement string!
