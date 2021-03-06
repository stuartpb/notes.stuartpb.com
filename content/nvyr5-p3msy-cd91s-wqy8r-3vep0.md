# The Boundaries of Bagtent

Exploring [Alternatives to UUIDs](c4g5h-7tqnh-8j8gd-eaqvs-sxh8j) made me realize that this "every page is plain old Markdown and there's no special tool-maintained structure keeping it running (outside the tools to make new pages or set up links)" design is a *core aspect* of Bagtent: if you have something goingg on that needs a process running to keep it working, or requires specific extensions to Markdown, I'm not so sure it's Bagtent any more.

Especially, at its core, Bagtent is about letting all content live at arbitrary nodes that are linked ad-hoc. If there's a namespace layer behind the links, even if the code is implemented by tooling and the Markdown is still plain, I think that's maybe Bagtent-esque, but it's *not Bagtent*.

I do like the idea of having links be based on heading names, but that still has the side effect of breaking inbound links if the heading changes.
