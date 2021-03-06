# Web-Based Video Renderer

This is a thing I've thought about for a while, it's weird that none of my searches for "NLVE" or "Video editor" turned up results.

Anyway, the idea is, you should be able to do video editing, and composition, in-browser.

This seems easiest to describe in terms of functionality that we'd pull in:

- A model / base library that renders to canvas
  - I'm thinking D3.js makes sense here
    - Already has a transition library for animation
    - Data-oriented, so making this all map to a declarative tree would be straightforward
  - THREE.js could also be a possibility?
    - Does it have a model that'd be suited to the Jon Bois Google Earth composition style?
    - Maybe you can have a separate little thing in the THREE environment for "Render from a canvas" and that can take its input from a D3 subenvironment
- You'd also need something for audio rendering
  - It might still just be possible to do this
- A timeline (infinitely nestable would be cool)
  - possibly using [something from this page](0qjpt-5tj61-rkaas-hg7xx-mvt80)

## caption track idea

- a track for, like, speaker notes. Closed captioning prompts for recording a narration track. Some way to have a layer on playback in-editor that doesn't render to the video.
  - Couldn't you just do this with a layer that you hide before export?
  - I guess it's more "actually, subs should be a custom export layer", since, you know, there are highly complex subtitle render layer formats.

## how the compositing would work

So, for the video, you'd mix to a canvas, and then the video compositing is done via [CCapture.js](https://github.com/spite/ccapture.js/)

We have the input piped to ffmpeg as PNG frames, which are immediately piped to ffmpeg for conversion to the appropriate video format. (Or we just land webp support in Firefox and use that as a WebM input, that'd be cool too - are they both reasonably easy?)

This'd be like [ffmpegserver.js](https://github.com/greggman/ffmpegserver.js) - only, instead of rendering frames and then sending them over the network, you'd send them to an in-process instance of [ffmpeg.js](https://github.com/Kagami/ffmpeg.js)

(is that build current? TODO: look at https://github.com/BrianJFeldman/ffmpeg.js.wasm and https://paul.kinlan.me/running-ffmpeg-with-wasm-in-a-web-worker/)

see also https://phoboslab.org/files/ffmpeg-mt/

you'd stream out to the disk with [StreamSaver](https://github.com/jimmywarting/StreamSaver.js)

Anyway, you could do this by having the user select all their sources via file dialog, and then the system reocgnizes it has these files available on-disk (it does something like a hashsum to compare), and also (if supported) starts uploading to the server.

if all your sources are uploaded to the server, you can have the render run on the server. Doing the ffmpeg in-process using something like headless Chrome makes the most sense to me imo - it'd need to be a micro-services deal

if in headless Chrome the [WebM thing](https://github.com/thenickdude/webm-writer-js/) becomes a more feasible option

## more thoughts on this

This'd be a great example of the whole "providing your compute resources to the cluster for credit" idea. You can provide unused cycles to stuff like a microservice that renders this stuff, and you get credit as long as your node is functional for all the cmopute it's offering, proportional to demand, which you could then redeem when you, personally, need to use more compute - momentarily (like, for a render) than you ever outptu at one time

## hmm tho

Didn't I have a whole thing for "a tool where the base data format is inputs to ffmpeg"? How much input transformation can ffmpeg do?

## Goals

It'd be cool to make videos as easily as Jon Bois makes them with, what, Google Earth and Microsoft Excel?

- Would you maybe just use A-Frame / Spoke as the scene graph?
  - Like, would it maybe make sense to build this out of / integrate into a whole Unity-style 3D scene scipting environment / editor?
