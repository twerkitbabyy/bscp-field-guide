# 04. Burp setup for the exam

You don't need a complicated setup. You need Burp, five extensions, and a proxy that doesn't blow up in your face. Here's the lot.

## Extensions

These five are the essentials, and I mean essentials. They map directly onto the vulnerability families you'll face, and anything beyond them is overkill for the BSCP:

- **JWT Editor:** decode, edit and sign JWTs, and pull off the JWT attacks (alg confusion, jwk and jku injection, weak-key work).
- **Hackvertor:** encode, decode and transform data inline with tags. This is your friend for obfuscation and squeezing payloads past a WAF.
- **Param Miner:** discover hidden and unkeyed headers, cookies and parameters. Essential for cache poisoning and anything with a hidden input.
- **HTTP Request Smuggler:** detect and help exploit request smuggling.
- **Freddy:** detect and help exploit insecure deserialization.

Install those and stop there. You don't need a BApp Store spree.

## Pro or Community

You can do most of the prep on Community, but know the two gaps:

- **Intruder is throttled** on Community. For any brute forcing, just use **ffuf** instead. It's faster and doesn't nag you.
- **Out-of-band labs need Collaborator**, which is Pro only, and you can't fake it on the Academy. PortSwigger only allows OOB interactions to Collaborator domains for safety, so you can't point those labs at your own OOB server. The OOB labs simply wait for Pro.

For the exam itself, don't drop 600 on the yearly pack just for Collaborator. **The Burp Pro 30-day trial is free and valid for the exam.** Start it before the day and you've got everything you need.

## Scope and proxy

FoxyProxy with its default setup is fine. I didn't do anything clever with it. The one thing I'd change, and I learned this the hard way: **scope it to the PortSwigger lab domain only.** On exam day, if Burp starts swallowing traffic from the proctoring software, it will eat your machine alive (full story in the [exam-day section](./05-exam-day.md)). Pointing FoxyProxy at the lab domain and nothing else keeps that from ever happening.

## Workflow

Honestly, I have no system here. I work dirty and it's never been a problem. I finished the real exam with something like 30 Repeater tabs open and no organisation to speak of. If a tidy workspace helps you think, tidy up. If not, don't let anyone tell you that you need colour-coded tabs to pass. A bit of mud is fine.
