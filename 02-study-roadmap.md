# 02. Study roadmap

This is the exact path I took, and I wouldn't change it. I came into the BSCP off the back of the OSCP, where I'd learned the hard way what wasting time looks like, so I went in with a plan from day one. Here it is.

## The order

1. **A full pass through every in-scope lab, by topic.** Work the Web Security Academy like a checklist. For every lab, take a note, grab the solve, and fold it into your own cheatsheet. The point isn't to collect solves, it's to build the reference you'll actually trust later.
2. **Around 100 mystery labs** to forge recognition (more on this below).
3. **The practice exams.**
4. **The real exam.**
5. **After passing, the remaining labs** to finish the Academy at 100%, because why not.

You don't really need to pick topics to prioritise up front. You work out your own weak spots as you go. The one thing I'd stress: **put your depth into the stage-1 style vulnerabilities.** Getting that first foothold is the hardest and broadest part. The further up the stages you go, the narrower and less complex the scope tends to get, and the easier it is to escalate. Front-load the hard part.

## Theory, or straight to the labs

This one is personal. I barely touched the written theory and went straight into the labs, because I learn by catching patterns while getting my hands dirty. I won't sugarcoat it: the first few days are *ultra* frustrating. You get taken apart from every angle. But you start to find your footing, and for me that pain is the fastest way to learn. If you're wired like that, dive in. If you genuinely learn better reading first, read first. Just know the labs are where it actually sticks.

## The mystery labs, and the one trick that saved me

I did 100, mostly to hit a round number, at roughly 6 a day. Honestly, somewhere around 70 I noticed the labs and categories starting to repeat a lot, even though I'd filtered to only completed, Practitioner-level labs (around 180 of them). Whether that's luck or a hidden filter trimming the rarest ones per category, I can't say, but don't be surprised if you hit that wall too. By 100 my eye was trained enough that I could often call the vulnerability family just from the shape of the app.

Here is the single most useful thing I did, and I'll be upfront that some people might call it cheating. It isn't, it's your own practice, but you decide: **reveal the lab's objective, not the lab.**

The problem with mystery labs is this. They're Academy labs with the name hidden. You can look at an app, instantly clock that it's XSS, and then still lose half an hour going "great, but what exactly do I have to do to flip this to solved?" Is it an `alert()`, a `print()`, exfiltrating `document.cookie`? You identified the vuln in seconds and then burned 30 minutes aiming at the wrong goal.

So I'd reveal just the objective. For an XSS lab, that stops you wasting your life. For others it might simply say "delete user carlos", and now you know exactly what you're hunting. You keep training the part that matters, recognition, and you stop bleeding time guessing the goal. This worked extremely well for me.

**When are you ready?** When the structure of the app tells you the vulnerability family before you've really tested anything. That's the signal, not a lab count.

## The practice exams

There are two, and they're nearly identical to each other. A few things nobody tells you:

- **They don't mirror the real exam format.** A practice exam is a single app of three stages, not the two apps of three stages you get on the day. So they undersell the scale a little.
- **They're on the easy side.** You get two hours, but I finished comfortably in about one. I did them after the mystery labs.
- **Do them anyway, and do them for the format.** Their real value is that they're the first time you actually experience the three-stage structure. Until you've seen that, the exam feels abstract, and that uncertainty is half of what makes people nervous. I felt ready once I'd done them.

## The gap nobody warns you about

The thing that eats the most time on the real exam usually isn't *identifying* the vulnerability. It's **obfuscation.** You find the bug, and then you have to engineer the payload so a WAF doesn't eat it. The mystery labs don't train this at all. You never touch filter evasion there, so it's a blind spot waiting to happen. Set aside time to practise encoding and bypassing filters specifically, because recognition alone won't carry you on the day.
