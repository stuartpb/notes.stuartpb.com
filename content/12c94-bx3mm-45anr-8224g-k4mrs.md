# Semantic Markdown Structure as Data Schema

spawned from [Thread Jam](5e1y8-03bex-6w9e6-7pq56-78ece)

I guess this is something you pretty much develop by just writing transformations on an mdast tree

what was XML Translations like? Succinct?

Oh, what about JQ format? or Go's template language?

But I thought I had a page about how there'd be a neat natural-mapping-base from Markdown to trees

you could use some kind of selector like "kind"="heading"::nth-child... I guess that's kind of just CSS, huh

Oh snap, am I ultimately just describing a tidy HTML data description mapping? Can it be two-way?

Oh dog, can this be the basis for a usage of custom elements as a serialization construct

so you could have the "just html" serialization layer as a fallback (read: what's initially transmitted to the client on render), then have a two-way "custom-element-to-HTML" mapping (which converts them to "native data")

and then you don't need to repeat yourself on the data, you're transmitting the initial state as part of the page

this is probably stuff React has? but, like, I don't know if "round trip Markdown serialization" is something I hear all that much

(this stuff can probably merge over to that page, the markdown-to-trees one - or at least link to what this section is becoming)

[Semantic Markdown](c0g5q-c0kq9-tgaaw-avh15-004rm) is I think what I was looking for here

starts to overlap with a call for a [jq-like Markdown parser](e14dn-31jvn-y9a2g-7ch5c-v5qbk)
