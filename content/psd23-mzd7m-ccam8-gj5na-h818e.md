# A spec for a 3D printing pipeline

The CSG graph for the tree of the whole functional part should be hashed, then differenced with a readable embossing of the first 7 characters or so of that hash.

The code that produced that hash is committed to version control, and this embossed CSG hash is kept as a ref in CI, and attached to artifacts like STL being sliced

We could maybe also have a hash level for settings, but eh, I think by that point you're better off just having a (random or dated-and-logged-by-printer) serial number generation be part of your slicing

[Modular Slicer System](h6y0k-2wkws-8zavg-ndx1s-mc0mr) thought: what if "make a textured serial number layer based on the hash of the model" were a feature of the slicer?
