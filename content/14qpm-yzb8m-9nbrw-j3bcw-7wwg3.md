# A Better Scriptable Helm

I'm envisioning a GUI for manipulating a cluster definition via [Gibernetes](1ymee-cerd3-0nbgv-9chjh-n9ycj), and I'm realizing: there's no way to say "this value comes from this template" or "this subchart" or "this values definition"

Like, my tooling needs a way to know where to propagate a change back

I know Helm has its whole V3 Lua idea, but I'm unimpressed, and I'm not sure maintaining compatibility with Go's text-based templates is a good idea

Like, Helm-style templating, in general... is a bad idea

Templates should be rewritten like:

- a base document defining the static aspects of the resource
- a layer that does the name generation
- a layer that specifies the abstract data
- a layer that translates the abstract data into patches
