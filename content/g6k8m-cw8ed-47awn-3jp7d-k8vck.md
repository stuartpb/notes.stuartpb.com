# Markdown Data Format

Each first-level heading starts a new document.

Key: value mappings are line-based like this.

Values have their own spec.

By default, lines that take arrays may be comma-separated (working under CSV's quoting rules).

Paragraphs ending with colons with nothing foloowing them, immediately followed by a list, are interpreted as that list being the array.

Second-level headings can also be interpreted as keys, if their section content is to be read as a string (or list/array, if the section content is all a list).

Keys cannot have an odd number of double-quotes or other spanning construction (this allows colons to be in link names, quoted phrases, etc, much like the CSV quoting rule).

## TODO

Ensure this file's in with all the other "Semantic Markdown" notes
