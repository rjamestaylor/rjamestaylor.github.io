---
layout: post
title: "ActivationLockException: Distributed Tracing My Stolen iPhone"
date: 2026-09-23 13:00:00 -0700
categories: [technology, reliability]
tags: [iphone, apple, find-my, activation-lock, security, reliability-engineering, observability, distributed-systems, social-engineering]
excerpt: "My stolen iPhone traveled from the United States to Hong Kong and then Shenzhen. Activation Lock accidentally gave me something resembling distributed tracing across an otherwise completely opaque global supply chain."
---

My stolen iPhone is in Shenzhen, which is not where I left it.

More precisely — because I am a reliability engineer, and apparently this condition follows me outside working hours — Apple's Find My most recently reported my stolen iPhone, **RoboMobo**, near Huafa South Road in Shenzhen, China. Before that it had appeared on Hung To Road in Kwun Tong, Hong Kong, and somewhere between those two observations somebody began sending me messages asking me to remove the phone from my Apple account.

I didn't.

Then the phone moved.

At that point RoboMobo became considerably more interesting to me than its remaining value as an old iPhone. I was watching a physical object stolen from me move through an international supply chain I could not otherwise see, while some participant in that system was trying to get me to change a piece of persistent state attached to it.

I had seen something very much like this before: at work.

## A Phone Reappears

For quite a while after the phone was stolen there wasn't much of a story. It showed a last known location outside of the building from which it was stolen — probably the place it was powered off to stop tracking. A stolen phone which stops reporting its location is mostly just a missing rectangle of glass, aluminum and regret, and eventually I stopped expecting to hear from it again.

Then RoboMobo appeared in Hong Kong.

<p style="text-align: center;">
  <blockquote class="text-post-media" data-text-post-permalink="https://www.threads.com/@robot.taylor/post/Db99uRjGwe1" data-text-post-version="0" id="ig-tp-Db99uRjGwe1" style=" background:#FFF; border-width: 1px; border-style: solid; border-color: #00000026; border-radius: 16px; max-width:650px; margin: 1px; min-width:270px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"> <a href="https://www.threads.com/@robot.taylor/post/Db99uRjGwe1" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%; font-family: -apple-system, BlinkMacSystemFont, sans-serif;" target="_blank"> <div style=" padding: 40px; display: flex; flex-direction: column; align-items: center;"><div style=" display:block; height:32px; width:32px; padding-bottom:20px;"> <svg aria-label="Threads" height="32px" role="img" viewBox="0 0 192 192" width="32px" xmlns="http://www.w3.org/2000/svg"> <path d="M141.537 88.9883C140.71 88.5919 139.87 88.2104 139.019 87.8451C137.537 60.5382 122.616 44.905 97.5619 44.745C97.4484 44.7443 97.3355 44.7443 97.222 44.7443C82.2364 44.7443 69.7731 51.1409 62.102 62.7807L75.881 72.2328C81.6116 63.5383 90.6052 61.6848 97.2286 61.6848C97.3051 61.6848 97.3819 61.6848 97.4576 61.6855C105.707 61.7381 111.932 64.1366 115.961 68.814C118.893 72.2193 120.854 76.925 121.825 82.8638C114.511 81.6207 106.601 81.2385 98.145 81.7233C74.3247 83.0954 59.0111 96.9879 60.0396 116.292C60.5615 126.084 65.4397 134.508 73.775 140.011C80.8224 144.663 89.899 146.938 99.3323 146.423C111.79 145.74 121.563 140.987 128.381 132.296C133.559 125.696 136.834 117.143 138.28 106.366C144.217 109.949 148.617 114.664 151.047 120.332C155.179 129.967 155.42 145.8 142.501 158.708C131.182 170.016 117.576 174.908 97.0135 175.059C74.2042 174.89 56.9538 167.575 45.7381 153.317C35.2355 139.966 29.8077 120.682 29.6052 96C29.8077 71.3178 35.2355 52.0336 45.7381 38.6827C56.9538 24.4249 74.2039 17.11 97.0132 16.9405C119.988 17.1113 137.539 24.4614 149.184 38.788C154.894 45.8136 159.199 54.6488 162.037 64.9503L178.184 60.6422C174.744 47.9622 169.331 37.0357 161.965 27.974C147.036 9.60668 125.202 0.195148 97.0695 0H96.9569C68.8816 0.19447 47.2921 9.6418 32.7883 28.0793C19.8819 44.4864 13.2244 67.3157 13.0007 95.9325L13 96L13.0007 96.0675C13.2244 124.684 19.8819 147.514 32.7883 163.921C47.2921 182.358 68.8816 191.806 96.9569 192H97.0695C122.03 191.827 139.624 185.292 154.118 170.811C173.081 151.866 172.51 128.119 166.26 113.541C161.776 103.087 153.227 94.5962 141.537 88.9883ZM98.4405 129.507C88.0005 130.095 77.1544 125.409 76.6196 115.372C76.2232 107.93 81.9158 99.626 99.0812 98.6368C101.047 98.5234 102.976 98.468 104.871 98.468C111.106 98.468 116.939 99.0737 122.242 100.233C120.264 124.935 108.662 128.946 98.4405 129.507Z" /></svg></div><div style=" font-size: 15px; line-height: 21px; color: #000000; font-weight: 600; "> View on Threads</div></div></a></blockquote>
<script async src="https://www.threads.com/embed.js"></script>
</p>

<p style="text-align: center; font-size: 0.9em;">
  RoboMobo checks in from Kwun Tong, Hong Kong. I did not authorize this change of venue.
</p>

It is worth being careful about what a Find My location actually tells me. The map gives me an estimated location for the device; it does not give me chain of custody, the identity of whoever is holding it, an invoice, a bill of lading or — regrettably — OpenTelemetry spans.

A location marker near a business does not mean that business has my phone, much less that its proprietor stole it. A device moving through the secondary electronics market can pass through thieves, aggregators, exporters, brokers, repair shops, parts buyers, resellers and people who merely bought something from the person before them. Some of those participants may know exactly what they are handling. Others may not.

I cannot see that path.

What I could see was that a phone stolen from me in the United States had somehow reached Hong Kong.

Then came the messages.

## Please Kindly Disable the Security Feature Which Is Inconveniencing Us

The messages came from an iCloud address — `testing.center.m138@icloud.com`, which has exactly the sort of reassuring institutional gravitas one hopes for when receiving unsolicited security advice — and warned that my personal information might be at risk.

The proposed remedy was wonderfully specific: open Find My, select the phone, and choose **REMOVE THIS DEVICE**.

<p style="text-align: center;">
  <img src="{{ '/assets/images/stolen-iphone/remove-device-message-1.png' | relative_url }}"
       alt="Message asking that the stolen iPhone be removed from Find My"
       style="max-width: 100%; border-radius: 4px;" />
</p>

<p style="text-align: center;">
  <img src="{{ '/assets/images/stolen-iphone/remove-device-message-2.png' | relative_url }}"
       alt="Follow-up social engineering message concerning the stolen iPhone"
       style="max-width: 100%; border-radius: 4px;" />
</p>

This is, of course, precisely what the owner of a stolen iPhone should **not** do.

Erasing an iPhone and removing it from an Apple account are different operations. Apple's Activation Lock can remain in force after a phone has been remotely erased, while removing the device from the owner's account clears the ownership constraint that prevents someone else from simply activating it as their own.

In other words, the security control was doing exactly what it had been designed to do. Whoever had reached this particular point in the phone's lifecycle had encountered a dependency they could not resolve locally.

The dependency was me. *Tee hee*.

## When the Attack Moves Up the Stack

There is a simple security lesson in this which becomes obvious once you watch it unfold: when a technical control cannot be defeated economically, the attack tends to migrate toward the person who operates it.

If authentication is difficult to bypass, attack account recovery. If multi-factor authentication works, convince the user to approve the prompt. If Activation Lock cannot be removed by whoever physically possesses the phone, persuade the legitimate owner to remove it voluntarily.

Technology can change an attack surface without removing the underlying incentive. In this case, the technical path had apparently become inconvenient enough that the process backed up until somebody tried another interface.

Me.

This is where the reliability engineer in me became interested, because what I was watching began to resemble a distributed transaction moving through a system whose internal components I could not observe.

## Apparently I Am the Observability Backend

A customer interacting with a large enterprise system usually experiences one operation where the engineers responsible for that system see dozens or hundreds.

The customer clicks **Buy**.

Behind that button, a request reaches an edge service, which calls an API, which publishes something to a queue, which wakes a worker, which queries a database, which calls a payment processor and perhaps publishes another event consumed by six more services maintained by four different teams — one of which, naturally, has not existed under that name since the 2023 reorganization.

To the customer, it remains one transaction.

For us to understand that transaction operationally, we need some way to preserve its identity as it passes from component to component. We propagate a trace ID and each participating service contributes a span describing the small piece of the journey it could see.

```text
trace_id: 7df3a91...

customer
   |
   +-- edge-api
         |
         +-- order-service
               |
               +-- inventory
               |
               +-- payment
               |
               +-- fulfillment
```

Without that shared context, troubleshooting a failure becomes archaeology: search one system's logs, guess a timestamp, jump into another system, discover the clocks disagree, revise the guess, repeat until enlightenment or retirement.

With a trace ID, at least we know which events belong to the same transaction.

My stolen iPhone seemed to have acquired something analogous.

Activation Lock is not literally distributed tracing. There is no `traceparent` header embedded in RoboMobo waiting for an electronics broker in Hong Kong to propagate it to the next participant, and I mention this mainly to discourage several engineers I know from composing corrective emails before finishing the paragraph.

But from my perspective, trying to observe an otherwise opaque system, the ownership state behaves remarkably like durable transaction context.

The physical phone can change hands repeatedly while one important fact persists:

```text
Activation Lock: Robert
```

The route itself is mostly invisible.

```text
United States
     |
     +-- ???
           |
           +-- ???
                 |
                 +-- Hong Kong
                       |
                       +-- ???
                             |
                             +-- Shenzhen
```

I cannot see the intermediate actors, how many there were, what each one believed about the device, or what economic transaction caused it to move from one to another. The ownership state, however, survives those handoffs, and every downstream participant eventually inherits whatever consequences come with it.

Somewhere in that pipeline, a workflow hit the unresolved dependency.

The transaction stopped.

And the exception bubbled backward until it reached the only service authorized to resolve it.

```text
ActivationLockException:
    PreviousOwnerRequired
```

That service was me. *YES*

The text message was telemetry leaking out of a distributed system whose internals I otherwise had no ability to observe.

Suddenly I had another span.

## Don't Overinterpret the Spans

Reliability engineering also teaches a less entertaining lesson: telemetry tells us what it tells us, not what we wish it told us.

Two messages do not prove there were two owners. A new Find My location does not prove the device was sold at that location, and a blue circle on an Apple map does not confer criminal liability on whichever electronics business happens to be closest to its center.

During incidents, a handful of correlated observations can very quickly become a wonderfully coherent story. The story may even be correct, but coherence is not evidence.

From my end I can establish only a small number of things with confidence: the phone was stolen; it remained tied to my Apple account; it later reported from Hong Kong; someone contacted me asking that I remove it from that account; I did not; and the phone subsequently appeared in Shenzhen.

That is enough to be interesting.

It is not enough to write an indictment.

## Crossing Into Shenzhen

After the messages, RoboMobo moved again. Find My next reported it around Huafa South Road in Shenzhen, roughly 6,900 miles from me.

<p style="text-align: center;">
  <img src="{{ '/assets/images/stolen-iphone/robomobo-shenzhen.jpg' | relative_url }}"
       alt="Find My showing stolen iPhone RoboMobo near Huafa South Road in Shenzhen"
       style="max-width: 100%; border-radius: 4px;" />
</p>

<p style="text-align: center; font-size: 0.9em;">
  Shenzhen. 6,904 miles away. Fourteen hours since the last report when I took the screenshot.
</p>

A wider view made the geography clearer.

<p style="text-align: center;">
  <img src="{{ '/assets/images/stolen-iphone/robomobo-shenzhen-wide.png' | relative_url }}"
       alt="Wider Find My map showing the location of stolen iPhone RoboMobo in Shenzhen"
       style="max-width: 100%; border-radius: 4px;" />
</p>

Hong Kong to Shenzhen is not exactly a surprising direction of travel for consumer electronics, although that fact does not tell me what was happening to this particular one. The phone might have been destined for resale, repair, refurbishment, parts recovery or some combination of those; I also have no reason to assume the person sending the messages was physically standing next to it.

What is visible is an object moving into an ecosystem exceptionally good at moving, repairing, disassembling, recombining and reselling electronics. In that respect a supply chain begins to look remarkably like the production systems I work on: loosely coupled services, intermediaries, queues, brokers, retries, undocumented dependencies and a resilience which is often much easier to admire when it isn't working against you.

Which is probably why I recognized it.

## Security Is Economics

Activation Lock does not make stealing an iPhone physically impossible. What it does is alter the economics after the theft.

That distinction matters because much of practical security works this way. We rarely make an undesirable action metaphysically impossible; instead, we make it more expensive, less profitable, more detectable, or inconvenient enough that another target becomes preferable.

A locked iPhone can still be transported, dismantled or sold to somebody who fails to inspect it carefully. It may still have parts value. What it cannot easily become is an ordinary used iPhone which somebody wipes, activates and resells as though nothing unusual happened.

Apple has pushed that model further by extending Activation Lock protections to supported parts from locked devices, reducing the usefulness of some components when the system identifies them as coming from an iPhone still protected by Activation Lock or Lost Mode. *(Thanks to Threads user [@davidthefreeze](https://www.threads.com/@davidthefreeze) who pointed out the extended reach of Apple's Activation Lock)*

<blockquote class="text-post-media" data-text-post-permalink="https://www.threads.com/@davidthefreeze/post/DdpA1g-D-Yu" data-text-post-version="0" id="ig-tp-DdpA1g-D-Yu" style=" background:#FFF; border-width: 1px; border-style: solid; border-color: #00000026; border-radius: 16px; max-width:650px; margin: 1px; min-width:270px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"> <a href="https://www.threads.com/@davidthefreeze/post/DdpA1g-D-Yu" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%; font-family: -apple-system, BlinkMacSystemFont, sans-serif;" target="_blank"> <div style=" padding: 40px; display: flex; flex-direction: column; align-items: center;"><div style=" display:block; height:32px; width:32px; padding-bottom:20px;"> <svg aria-label="Threads" height="32px" role="img" viewBox="0 0 192 192" width="32px" xmlns="http://www.w3.org/2000/svg"> <path d="M141.537 88.9883C140.71 88.5919 139.87 88.2104 139.019 87.8451C137.537 60.5382 122.616 44.905 97.5619 44.745C97.4484 44.7443 97.3355 44.7443 97.222 44.7443C82.2364 44.7443 69.7731 51.1409 62.102 62.7807L75.881 72.2328C81.6116 63.5383 90.6052 61.6848 97.2286 61.6848C97.3051 61.6848 97.3819 61.6848 97.4576 61.6855C105.707 61.7381 111.932 64.1366 115.961 68.814C118.893 72.2193 120.854 76.925 121.825 82.8638C114.511 81.6207 106.601 81.2385 98.145 81.7233C74.3247 83.0954 59.0111 96.9879 60.0396 116.292C60.5615 126.084 65.4397 134.508 73.775 140.011C80.8224 144.663 89.899 146.938 99.3323 146.423C111.79 145.74 121.563 140.987 128.381 132.296C133.559 125.696 136.834 117.143 138.28 106.366C144.217 109.949 148.617 114.664 151.047 120.332C155.179 129.967 155.42 145.8 142.501 158.708C131.182 170.016 117.576 174.908 97.0135 175.059C74.2042 174.89 56.9538 167.575 45.7381 153.317C35.2355 139.966 29.8077 120.682 29.6052 96C29.8077 71.3178 35.2355 52.0336 45.7381 38.6827C56.9538 24.4249 74.2039 17.11 97.0132 16.9405C119.988 17.1113 137.539 24.4614 149.184 38.788C154.894 45.8136 159.199 54.6488 162.037 64.9503L178.184 60.6422C174.744 47.9622 169.331 37.0357 161.965 27.974C147.036 9.60668 125.202 0.195148 97.0695 0H96.9569C68.8816 0.19447 47.2921 9.6418 32.7883 28.0793C19.8819 44.4864 13.2244 67.3157 13.0007 95.9325L13 96L13.0007 96.0675C13.2244 124.684 19.8819 147.514 32.7883 163.921C47.2921 182.358 68.8816 191.806 96.9569 192H97.0695C122.03 191.827 139.624 185.292 154.118 170.811C173.081 151.866 172.51 128.119 166.26 113.541C161.776 103.087 153.227 94.5962 141.537 88.9883ZM98.4405 129.507C88.0005 130.095 77.1544 125.409 76.6196 115.372C76.2232 107.93 81.9158 99.626 99.0812 98.6368C101.047 98.5234 102.976 98.468 104.871 98.468C111.106 98.468 116.939 99.0737 122.242 100.233C120.264 124.935 108.662 128.946 98.4405 129.507Z" /></svg></div><div style=" font-size: 15px; line-height: 21px; color: #000000; font-weight: 600; "> View on Threads</div></div></a></blockquote>
<script async src="https://www.threads.com/embed.js"></script>

The logic is straightforward. If the complete device becomes less profitable, theft shifts toward parts; if the parts become less useful as well, the incentive gets squeezed again.

Of course, no security architecture arrives without a corresponding power architecture.

The same machinery which can make a stolen component less useful also makes legitimate repair and reuse dependent on authorization maintained by a manufacturer's infrastructure. Once a system is capable of answering **who is authorized to use this thing?**, other questions immediately follow: who operates the authoritative database, what happens when ownership records are wrong, what happens when the owner dies, what happens when the manufacturer disappears, and how much control should a manufacturer retain over a physical object after it has been sold?

Those are not merely implementation details.

They are decisions about power dressed up as API responses.

In this particular case, I am delighted that Apple's infrastructure continues to insist the phone belongs to me, because it does. But the broader mechanism deserves scrutiny even when it happens to be acting in my favor.

## Possession Is No Longer Ownership

For most ordinary physical objects, possession remains a reasonably strong approximation of control. If someone has my chair, they can sit in it. If someone has my hammer, they can use it. If someone stole the Macintosh IIci I sold in 1989, they could probably turn it on today, assuming first they survived the search for an appropriate display adapter.

An iPhone is different because physical possession and operational authority have become separable.

Someone thousands of miles away can hold RoboMobo, move it across an international boundary, open it, sell it or smash it with a hammer (please don't; the telemetry has become interesting), while Apple's systems continue to recognize an ownership relationship which points back to me.

The object in their hand is therefore also an endpoint in a much larger distributed authorization system.

Increasingly, this is simply how the physical world works. Phones, cars, software-defined appliances, DRM-controlled media, cloud-managed networking equipment and even industrial machinery can remain dependent on remote identity and authorization services long after the purchaser has carried the object home.

Sometimes that dependence protects the owner, as it is doing here. Sometimes it means the manufacturer retains an uncomfortable degree of control over something the buyer reasonably regards as theirs.

Often it does both at once.

Distributed systems are inconsiderate that way.

## The Human Is Still an API

The part of this story I find most instructive is not that Apple's security system exists, but what happened once it worked.

There is substantial engineering behind Activation Lock: cryptographic identity, hardware-backed security, Apple's infrastructure, authentication systems and the Find My network. Smart people spent a great deal of effort making it difficult for unauthorized possession of an iPhone to become authorized ownership.

Eventually, though, all of that sophisticated machinery reduced the problem to a much older interface:

Maybe Robert will click the button.

There is something wonderfully humbling about this. We can build increasingly elaborate technical controls, but once they succeed, the attack often moves outward until it reaches a person with authority to override them.

That does not mean the technology failed. In this case, the social-engineering attempt exists precisely because the technical control appears to have succeeded well enough that somebody needed my cooperation.

If Activation Lock were trivial to bypass, nobody would need to send me anything.

The message was the exception bubbling all the way up the stack.

And the final dependency turned out to be a 59-year-old reliability engineer in California looking at Find My and thinking this was much more entertaining than losing an iPhone ought to be.

## No, I Am Not Removing It

I have no intention of removing RoboMobo from my Apple account. This is partly sensible security practice, partly principle and — I should admit — now at least a little bit spite.

Mostly, though, I am curious.

I started with a stolen phone. What I have now is a tiny, intermittently reporting probe inside a system to which I otherwise have almost no access. I cannot see the participants, the queues, the handoffs or the economic agreements moving the device from one place to another, and I certainly do not have anything resembling a service map.

What I do have is persistent state, a few location observations and the occasional moment when somebody else's interaction with that state leaks enough telemetry for me to see that the transaction is still progressing.

This is, frankly, better observability than I've had on some production systems. *I am not joking.*

For the moment, the transaction appears to be blocked on an upstream dependency.

That dependency is **me**.

The change request is ***_denied_***.

---
