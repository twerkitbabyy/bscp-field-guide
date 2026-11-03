# 05. Exam day: logistics and gotchas

The exam itself is well built. Almost all the pain on the day comes from the setup around it, the stuff nobody tells you. Here is everything I wish I'd known walking in, so you don't lose time or nerve to avoidable surprises.

## Check-in and proctoring

I expected a live camera ID check, because that's what people seemed to imply. It's simpler than that. When you open the proctoring panel it just asks you to upload a photo of an ID document. In my case, the front of my national ID. There is no room scan, no "show me your desk, show me behind you" walkthrough. You upload the photo, and then it asks you to turn on your camera, screen share and microphone.

The proctoring software is called **Snoopervisor**. Worth knowing the name so you can read up on it before the day.

There is no explicit guidance on whether you can get up or take breaks. All you get is the usual FAQ: what counts as cheating, the exam scope, the standard stuff. Nothing either way about leaving your seat.

The one thing that genuinely rattled me on my first attempt: **there is no "finish exam" button.** When I had 30 minutes left and knew I had no shot at the second app, there was no clean way to end or hand in. And because the rules don't say what happens if you close the proctoring software, I had no idea whether shutting it down early would be read as cheating. I ended up leaving the room for half an hour and letting the clock run out, because I wasn't willing to risk it. It's bad design, and it's worth knowing in advance so it doesn't throw you the way it threw me. If it matters to you, clear it up with PortSwigger support before the day rather than guessing under pressure.

## The Burp meltdown, and how to avoid it

This is the one that nearly cost me the exam, and it is completely avoidable.

I was using one browser for everything, with FoxyProxy sending traffic to Burp. The moment I captured the very first exam request with the proctoring software open, Burp shot up to around 17 GB of RAM with the CPU pinned at 100%. The strange part: HTTP history only showed the exam request, nothing from the proctoring tool, and turning FoxyProxy back off didn't fix it. Once Burp had ingested that traffic with the proctoring running, it kept choking the machine until I fully closed and reopened it, even though I was done intercepting. That cost me about 20 minutes.

Two ways to make sure it never happens:

- **Best option, separate browsers.** Run the labs in one browser with FoxyProxy pointed at Burp, and the proctoring dashboard in a completely different browser with no proxy. Burp then never sees a single packet from the proctoring tool. This is the set-and-forget fix.
- **Or scope FoxyProxy tightly.** Create a FoxyProxy rule that only routes the PortSwigger lab domain to Burp, so proctoring traffic bypasses it entirely. This is what saved me once I understood the problem.

Either way, set this up before you start, not after Burp has already melted.

## Managing the four hours

I tend to solve the labs relatively fast, so the clock was never really my enemy. On my successful attempt I got the first app in about an hour and the second in around 30 minutes. For reference on tooling, I only reached for Burp's scanner about three times the whole exam, almost as a formality. The vulnerabilities were either obvious or signposted enough that recognition (see the [playbook](./03-recognition-playbook.md)) did most of the work.

The one piece of timing advice that matters: **if you've been stuck on something for 30 minutes, switch to the other app and come back later with a fresh head.** On my first attempt I burned two hours going in circles on one thing before I gave up. Rotating out and back in resets your tunnel vision, and it's the cheapest way to unstick yourself.

(I also split my screen into two windows and worked both apps at once on my second go. That is not a recommendation, I'm just wired that way. Do one thing at a time unless you know chaos suits you.)

## My setup

Nothing fancy, and that is the point:

- **OS:** Arch with Hyprland.
- **Browser:** Brave.
- **Burp:** Burp Pro, on the 30-day trial. Yes, the trial is valid for the exam, so you don't have to buy a licence just to sit it.
- **Extensions:** honestly just FoxyProxy on the day. The detection extensions you lean on during prep barely came out. Don't overcomplicate your exam-day setup.

Other than the proctoring incident, the exam felt fluid and well built. A genuine 10 out of 10 on the platform itself.

## Odds and ends

- **The AI rule is ambiguous.** The rules never actually state whether AI tools are allowed. They don't mention it at all. For an exam sat in 2026 that's a gap they should close. I'd confirm it with support rather than assume, either way.
- **There's very little public info about the exam.** People are tight-lipped about the BSCP compared to, say, the OffSec certs, which is part of why I wrote this guide. Going in with a clear picture of the logistics is half the battle.
