# Advanced Rendering for These Notes on GitLab Pages

(originally titled "Notes News Update 2019-03-08", retitled after still being relevant by 2019-05-21)

So, I'm thinking of moving this repo over to GitLab Pages, and writing my own rendering system.

One thing I'm thinking it'd be cool to add, if I could have my own system for this - so, one of the major perks of doing this would be that I'd get to do my own pug-based template renderer or whatever.

## Per-tree stylesheets

But another thing I'm thinking could be fun would be an "add these stylesheets for these pages" spec, like "everything linked from one of the two Lean Notes pages shoyld have this Lean Notes stylesheet". Kind of like the special rules around creating trees for notes that live somewhere around the plans for Bell TPC in the Unconvincing Truth notes.

It'd be acceptable to recognize a tree as not... extending (recursively) to any page that links to itself? Ie. that's a reasonable way to "avoid cycles" - if anything links to...

Nah, this is starting to feel overcomplicated

# Breadcrumb navigation

(TODO: give this its own page)

What if there was a "What links here" on the page?

So, like, another thing it's interesting to recall that my own build system would allow would be bundling the data for pages, so that "what links here" can be kept as a JSON file (single and giant if space permitting, or per-page if that'd be more performant), and then "What links here" can be a matter of clicking an "arrow" on either side of the "History" menu... could it be a menu?

I was thinking a (fixed-space per page name?) breadcrumb bar, with maybe a Left and Right which both open menus (left opening What Links To This Page Here, and Right opening a menu of What Links Are On This Page)

What Links Here and the menu items can use first-heading titles, whereas the links-on-this-page outline can use the text of the links

and then there can be dotted-line borders between them - dotted to connotate that you can click them to "cut" history at that point (or maybe this can happen automatically), and then the item right of the dotted line will become the leftmost on the bar (which also happens when you use "what links here" to jump to another part of the tree, the new page becomes leftmost)

anyway, a simpler What Links Here backwards-navigator could work

maybe it's page name, left-pointing triangle (listing other pages that point to this one, or greyed out if the one displayed is the only parent), dotted line, right-pointing triangle?

and then hitting the dotted line on a single-parent page would un-gray-out the back triangle?

maybe it's a dotted line *or* a back-triangle, and if you want to cut history for a page with multiple parents you have to go back to that origin and then back to where you were

the leftmost should always be the button for "top", which will look like a notebook cover maybe? like, a whole "composition book" aesthetic to this site would make sense

yeah, thing to remember, you can click the dotted line on an element to the right to just say "forget this forward history"

links are ordered by earliest-position-in-their-respective-files (ie. a file is ordered based on the position of the earliest link within that file / element-tree to this page), by this procedure (continuing to the next condition only in the event of a tie):

- Earliest line
  - an implementation that determines this data by using the output from the Markdown renderer,rather than using a hook into the parser, may count this as number of prior siblings to the top-level parent in the document tree, and I don't think that'll ever yield a different result
- Earliest character position in that line
  - Again, I think you can do this by summing the codepoints of prior nodes' textContent and you'll get the same result - there *might* be some differences if text is processed as UTF-16 or whatever JS's native string base is, versus the original file's UTF-8 and just counting code points? Anyway, I think there's some kind of ES6 extension to address that count
- Shortest text
- Origin with lowest UUID

so the data saved in parsing would be first-link data (line, position on line, text of link) in a map based on UUID of the originating document (this would probably be in the form of a map containing data for all pages' outbound links, from which the reverse mappings would be derived at runtime)

UPDATE: on [some reflection][], the first items in the list should be pages that link to the page that are also linked on the page,

[some reflection]: tnftn-va96n-cj8q1-83kky-t2rw3

By this logic, linking to a page that's left of a page via the rightward drop-down should cut history at its parent, since this is kind of a signal for "the page I originally came from wasn't the real child" - if this destroys a relevant backward trail... then I should log it as a user story and revisit this conception

## Breadcrumbs as a list

You could basically just take everything above, and rotate it 90 degrees (keep the icons on the right of a page's row, put left and right next to each other as up/down expanders, with their menus expanding indented and floating)

## CUT HERE, the stuff below is actually related to rendering, leave it here

(definitely make sure these two pages are linked though)

I'm thinking there can be a querystring based on the tree Git hash for signifying when the metadata needs to be refetched, ie. it's fetched as "metadata.json?tree=abc123(continued for 34 characters)"

this can probably be done as `<meta name="git-tree" value="">` baked into each page which can be queried when the script runs

I was thinking you could maybe have navigation as an opt-in expandable bar, and normally it's just the "what links here" list with [document.referrer](https://stackoverflow.com/questions/3528324/how-to-get-the-previous-url-in-javascript) highlighting "this is where you came in from"

alternately, "what links here" is baked into the static version, and is hidden by the breadcrumb bar

## Historic Version Renderer

A custom rendering system would also be able to point to "This page was deleted, see the commit that deleted it for directions toward where you should probably go", that would make it so I wouldn't have to leave so many stubs and stumps around

Ooh, maybe there can be an on-demand renderer that renders the tree at a certain point by refspec? hmm, idk if there's a suitable reflog upstream, like, how would it handle pages merged in from foreign trees that had a different version of the page... i don't know, it's weird, it's bridges I'd cross when I came to them

this could use the "?tree=" parameter used for cache magic above
