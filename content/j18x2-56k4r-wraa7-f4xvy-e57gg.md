# a giant-ass tree about redesigning bagtent-rig and company

continuing from having [rethought my original instincts around footlinks](h3jma-ap4ef-01a3j-hfdjq-d31jq):

- and this brings me onto the subject of redesigning bagtent-top-links:
  - it should really just show files matching the input term in links to them by how many references are pointing to the file
  - for square-square references, it's fine to count the left-side as a (presentationally) "parenthesized" match
  - and you can match the right-hand-side by looking for its URL definition line and displaying matches in those references as square-braced
  - and so a link that matches on both sides just counts twice, which is fine because it's double-appropriate
  - It's important to note that square-referenced (ie. ending `][]`) links will count as *square* matches
    - their left hand side isn't considered a candidate for a page point - they "don't specify" what page they match
      - I feel like they should still have a "verify this reference is used" token made, though
        - to be checked when encountering define lines,
        - for lint's sake if nothing else
        - like, an unreferenced link, or an unrigged one, should definitely warn, and maybe abort calculation altogether
        - or, you know... invoke rigging... which would be what you'd also do to conjure new (searching the left-hand side, or your missing right-hand token)
          - okay so that's why rig is a complex subscript that might involve node, or needs to be dumber
          - or, okay, rig (or some other script with a more complex name like Git's internals) is plumbing, for each file:
            - (note, this would be something underpinning, and probably using parts from, bagtent-top-links)
            - it outputs all parenthesized link lines
            - it can hold links that are specified as square in memory (a token list in Bash), but not a square that's been encountered and defined, for if that square comes around, what page to link to
            - Needs to have a behavior definable for what happens on multiple definition of a symbol (if different)
            - anyway, once the square is matched, it outputs any stored left-hand-sides as pointing to what the symbol pointed to
            - it only
            - definition lines that are never referenced can be the one instance where it will print a definition line
              - so lint can detect them,
              - garbage-collection can purge them,
              - scoring can maybe half-count them (though that sounds like a bad idea),
              - some other tool can ask where you'd like to insert a link to it in the document (which section/list)
                - this would work for tools that "spawn" pages, but don't sprout them from a hooked-up place
          - okay, so, rig is a more complex script, that lets you interactively pick from the top-ranking / best matching
            - (a second pass can present the contexts that contributed each match)
            - and if you want to rewrite your right-hand-reference
              - or maybe even your left-hand,
              - or, hell, maybe just drop you to that position in an editor because it turns out you were wrong about
                - this could be tricky if editing a line with multiple links? or what if the section changes enough?
                - I think that might be why you'd want the "surgical change without invoking an editor" command options
              - Maybe also... hmm, okay, so, use case for invoking an editor near the link position
                - in a VS Code context, opening the origin editor can be useful for cutting a section
              - maybe in ambiguous cases, it presents closest links to where the diff thinks the old link is by diffinw within the line,
                - and you pick specifically which of the ones you changed is the one to rig
                - and you drop back into the editor
              - another case where it might be ambiguous: I changed the original link to not match
                - and just to complicate the matter, say I added a new link that does match
                  - which is to say, I "moved the match", in a sense
                  - or did I just rephrase my search query to be more appropriate for reading?
                    - in this case, I'd probably want to take a suggestion through the interface for "Change This Right (or Left) Side Reference Token for Readability"
                      - that only presents the editor as a last-case scenario
                      - after displaying all the matching terms for the search
                      - and if there were none
                      - this can also rerun the search for rigging?
                      - what to do if there was a line like `[Linkup Name]:` and no path?
                        - I guess this is detected in the rigging process,
                          - (ie. which links to skip because they're already rigged)
                          - in the filtered line presenter, this is just done by filtering out all the non-empty-or-special-hook-termed (by addon script?) parentheses links
                          - and you can opt to change the right-name from this line in all references
                            - either "accuracy", pulling title and possibly other reference names as a "menu"
                            - or "brevity", which maybe suggests "initials" or even, hell forbid, numbers
    - all square-tag references are evaluated by going to see if there are left-sized matches that referenced them by name, and adding the page to their "referenced as" score (as if they were parentheses)
    - see, this is because left-hand references that don't match the page aren't counted, and the square has to count for itself, so one of these double-links doesn't get the redundancy bonus of a match that uses the term on both sides of a square-brace-quartet (ie. 'foo' to `[foobar][foobaz]`)
    - because we know any matching square-reference, we let the match point go to the evaluation that will happen for the square-reference implicit "right hand side"
    - if you really want the double points, you gotta spell it out twice (lines that match the token on the left and have right-side data get counted as "paren" matches)
  - you know, this isn't strictly Bagtent... or at least, it could also be used in other documentation-tree-authoring-tool contexts
  - anyway this is definitely WAY off course as a "style" conversation, it's jumped into a full tool respecification

## whoof, I can't really revisit that tree right now, but

is there a note that

- I might just want to specify my link like `[Double or Doobley][DoD]`,
- and I just want it to insert a link to the latter,
  - using the left-side token to search
    - or maybe any other left-side reference to that symbol on the page
      - (ie. the search for `[DoD]` is a unified step across all references)
    - and to reuse any blank line starting with `[DoD]:`,
      - in case I picked a specific position,
    - or insert it at the end of the list after the next blank line
      - [as is recommended by practice](rmb77-qdmnq-828jq-ba8fx-xbe8k)?
