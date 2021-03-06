# Open X-Ray

not to be confused with [AWS X-Ray](https://aws.amazon.com/xray/faqs/)

So this'd be both an open database of (curated / mass-submitted-in-bulk-in-a-big-bag-of-kidenys-we-dont-know-from-where-and-we-dont-ask-how) shot-for-shot scene-for-scene cast-to-frame-position data, the tooling needed to generate that data, and the common specification that connects them

## prior art: amazon

https://blog.aboutamazon.com/entertainment/behind-the-scenes-with-x-ray lists the feature set

sounds like it's some proprietary thing they haven't written white papers on - no open source, no standard structure

- https://www.quora.com/How-does-Amazon-IMDB%E2%80%99s-X-Ray-work
- https://www.wired.com/2015/04/amazon-xray-fire-tv/

and of course you can [lift the X-Ray data from Amazon directly](https://blog.revolutionanalytics.com/2016/06/amazon-x-ray-data-provides-insight-into-movie-characters.html)

## end user vision

regardless of how Amazon implements it for their video library, this is how the end user can do it for theirs for whatever their desired level of quality / labor tradeoff, where you can still get good-enough YGWYPF at the "zero sweat" price point, and for a little bit more you can contribute back massively

Like, ideally you could even have some kind of "consensus model" accepting contributed automated/curated (with metadata for "by what method / level of rigor") "X-Ray Tracks"

## Generating

So, you'd take in some movie-cast-data source (Wikidata, Google, openmoviedb, illicit IMDB dump, whatever) for the cast

and you would take a general-purpose facial-recognition model, and train it on the actor as the character in the movie

- you'd seed this from results from a Google Image search or whatever DuckDuckGo bullshit free alternative you can get
- and of course the user can provide curation / selection from their own video source

you'd also want to have room for the extension to distinguish between the same character played by different actors, and different actors playing the same character. Also, "unknown character / actor" situations

don't forget that some of the most popular movies in American history require you to recognize R2-D2 and BB-8 - make sure your system isn't incapable of handling Homeward Bound

## data model thought

This is a timeseries, right? might want to look into best practices for that?

anyway, you'd allow for just defining who is in a "scene" defined by a large region of time, then options to drill down into shot-for-shot who is on-screen (at what position, within a square bounding box based on face, with body being an optional extension)

you could maybe have shot-for-shot presence data but no positional data? but honestly, if you're gonna collect it you might as well

## to recap

- level 0: basic metadata (commonly available elsewhere)
  - such as:
    - who's in the movie, playing who
    - year and any other disambiguation info
      - I believe deferring to Wikipedia is the right move here
    - RT score, box office performance
      - at this point you're just getting to the point where you should open up another website ya nerd
        - having this in-client isn't delivering any real convenience
        - you're just doing the entertainment-client version of expanding the scope until it reads mail
      - although see "triviabook" format below
  - stuff at level 0 is used more by other parts of the stack
    - it's still worth considering how this data fits in with it, though
- level 1: basic scene-by-scene data
  - who's in the thing scene by scene
  - this is the lowest level that can do something about spoilers
- level 2: shot-for-shot position-on-screen and line-being-spoken
  - level 2.5 being karaoke-level syllable-by-syllable recognition
- level 3: it's in 3D and you can replace the lead actor with yourself

## future extensions

this could also extend to "Who's speaking", and integration with subtitle presentation (imagine putting them near their heads with different colors / fonts, like it's a LucasArts game)

Music recognition is good, but if I'm honest I've got that turned on for my phone anyway... it's another thing that'd be good to work in for autocompilation + curation, especially considering that you have the narrow search space of "tracks that are in the credits" to confirm against

## Other X-Ray stuff

X-Ray has trivia: you could maybe have something like a VH1 Pop-Up format, also akin to YouTube's Notes I guess, where each entry can have as few fields as possible (except "fact body"), and the presentation host can display them as simply as Amazon's X-Ray

anyway, creating bodies of these "commentary tracks" is out of scope for this document

ooh, but then I just wrote "triviabook" above, and I'm picturing it having a Jupyter/Eleventy "Grand Unified Document" style, where the files are primarily Bagtent... now I kind of want to write that document instead

It's also how they surface bonus material, which, like, I guess you could probably do in the bagtent "metadata plus a file" "attachment" model

## nerd shit

anti-spoiler mode allow for character names to be hidden before they're revealed / masked at a certain time (so like, don't say Tim Curry is "Wadsworth/Mr. Boddy", have him be Wadsworth and only Mr. Boddy in the period of the ending where that's who he is)

## Other applications

This could be implemented as a Netflix extension for Chrome (and probably violate some fucking patents)

https://decider.com/2019/01/30/amazon-prime-video-xray-feature/

## overlaps

Remember this needs timecode cuing and stuff. It overlaps with latertime

and of course this is meant as part of the "beat capitalism at every feature of its own game" suite

## what kicked this off, anyway?

[Functions for a B Key](8dnzc-xxbaw-g18zm-66z62-ee1re)
