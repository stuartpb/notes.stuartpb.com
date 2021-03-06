# Softly-encrypting secret content

This incorporates both the Crypto Tech I Recommend and my own Components like Hashsplat and Alphabi

I was on [The Shadow Curtain](wm7ba-3ycgc-wn8h2-pjnsv-xny45) thinking about how a private notebook might interface with the public one

One idea that came to mind is "airlocks", for content I want to keep "private", but accessible if I don't have access to my private notebook (like thoughts on )

These could be done as Base64-encoded blocks that have content hidden behind a given tweetnacl-compatible-or-whatever password

You'd hint at the password through context, and then the private notebook can just spell out the password outright

You could also come up with a password from a hashsplat of the page's ID or title or a phrase or something

a few Alphabinary characters of the password hash (which is what's used to encrypt the data) can help make the password recoverable, by reducing the attack space?

(the private notebook can contain similar blocks, with the passwords stored in the secret notebook, but you don't want to jump a [Metasecrecy Level](8z894-g3abt-an9je-erjxy-kqcm2) and keep secret-notebook-protected data in the public notebook)

In the absence of editor tooling, you'd copy these to the TweetNacl Secret Box demo page, or something like that with nice Markdown editing support, and then paste in a new version of the encrypted section

this is also somewhat like the technique of using Thanked Names as discovery-hash-like for secret known identities - such Thanked Names might also be used to obfuscate titles

indeed, Thanked Names can be thought of as "an encryption technique for where the data being encrypted is the password used to encrypt it" (more on this thought [here](hp6s1-m7xec-wp9jq-4ap80-kq2rk))

Hmm, could this be done... word-by-word? Each word is replaced with a  Would that make typo/wording changes more

Gotta remember the attack space, though, you'd probably want to password-strong-and-salted has each word which'd be tough perf-wise...

I was thinking "you could salt the words by position!" but then you lose that

Ooh, if you salted words by *what the preceding word is*, you'd basically have resynchronization - maybe you mark your word spans somehow so you can unify common gramattical constructs?

Oh my god, one, is this how encrypted search indexes work, and two, is this now a way to write unambiguous character-count-equivalent encryption techniques, where each word is picked from a dictionary by hash, which can be decrypted using the last word, and the first one is a password? (And you can do it by a predictive model in which words are ranked by expectedness of following the last one, so you only need a few bits to express the most expected values?) Is this already what alphabi on compressed text is like?

Oh crap, is this another model for "charging" words rather than distance-from-the-center-of-the-alphabet?

another discourse sprung out from here on [Flexible / Strategically-Weak Keys](qe776-vswkp-c8av1-5r2hr-ac94q), which this train of thought starts to drift into
