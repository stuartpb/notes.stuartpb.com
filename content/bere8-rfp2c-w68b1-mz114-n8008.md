# key model / federated public verified identity

popping off from thoughts on session-user accounts in [accouch](3bgmz-ptkas-baa2b-w9a4z-kmm7f)

or: stuart tries to reinvent Signal/Wire at federation scale

(also, do I understand ratcheting window in that the token must be refreshed within a certain time period, and eventually the token expires?)

and are JWTs a valid thing here - you'd issue a signed token with the document, and then when they come back reusing their session token/cookie to query, you check to see if you have a record for that user, that their access wasn't revoked, and then the thing you trust is that the existence of the timestamps in signed time verify that it's been properly renewed

and is there a keypair thing going on here

I mean, yeah, do they have public keys for each endpoint on file for each refresh

right, okay, so I get the concept of "safety number" with Signal now, it's the device that authorizes all devices, a private key on it (that is just encrypted with the system's credentials? I think it has full-disk encryption?) is authorized for that "new device trusted without needing to disclose it to contacts" functionality (and... I think that's all there is to Signal? hence why PIN jacking is trivial but would get tripped up on Signal, the PIN is sent from Signal to authorize that you want to trust a new "trusted host" that is encrypted somehow)

oh shit, is this what GPG's web of trust is? it's users saying "yeah, I know the user who had to change their safety number, it's legit" - because you need that for the people who built your program, but you don't need to know the person personally?

and they're registered with a central authority? I guess, alongside this whole device-centric-web-of-trust thing

## migration and adding devices

I guess another thing about this is that you'd have to have a "migrate key" function - like, you can have your "migration password" - is this cryptographically possible? you have a password that discloses the private key that displays the key for transfer to another device with a different device-password keyset

or, I guess that brings us around to the web of trust, this is what those "new key added" notifications are - it's people saying "yes, they really did get a new laptop"

oh, right, okay, this is what "have the devices approve each other to get consensus/quorum for authorization as multiple factors for trust" was all about...

but this doesn't protect against a password-breach-and-raid situation.

and that's the threat model where you need social verification to add new devices. you can let one device send out a bulletin that your threat model has been raised (and get signed acknowledgement from the message-broker), and it takes three (or just, like, three Chromebooks, or one desktop and your phone) to deescalate the alarm for convenience

the idea in Signal is just "the user's phone will never be breached", I think?

okay so what you could reasonably do is have the same kind of notification for "user's safety number changed" where there's one "authoritative keyholder device" that is expected to have its own encryption on (like a password), and it can authorize or deauthorize devices

but also, if it's been too long you have to re-enter your password

i guess this is to avoid having the original smothered and body-snatchered away after the attacker is able to break the device's passcode - they might be able to see historic messages which sucks but we knew they would, but they won't be able to get at anything that wasn't replicated to the device, because its key expired in the time it took to break the encryption


so, like, when you join Twitter, you can have users to vouch for your
