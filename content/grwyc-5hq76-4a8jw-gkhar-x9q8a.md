# A Remote Control Thing

So, the idea here is, you have a JS client that implements a touchpad controller, and a desktop service that receives commands like "move the cursor 5 pixels left and 3 pixels up" via websocket.

And the JS stuff would be populated on the page using declarative data attributes, so you could, like, design a nice UI mostly in Inkscape

one of the coolest things to do is set this up alongside another library that can, say, put a visualization on a canvas - like the background shaders in that Shader Editor app. You can make your touchpad work like a mood ring, or a rippling pond, or whatever, using shaders or something

This'd probably also use [that Let's-Encrypt-Direct thing][LE-direct] to establish an HTTPS connection to the host. (Maybe the token can be generated within some kind of handshake, like each client has its own hostname it connects to the local machine via.)

[LE-direct]: nxgz4-vt82d-4k9am-6c0e6-yepc5

Also, unlike KDE Connect, this would allow for Steam-Controller-esque trackball intertia, one of my most deparately-missed touchpad features

Ooh: could this be put on something with a USB client interface that can emulate a mouse device? That'd be REAL cool, putting it on something like an RPi Zero that could be connected to via WiFi, but plugged into any PC - or even just plugged into a wall socket / battery pack and connecting to a remote device via Bluetooth!
