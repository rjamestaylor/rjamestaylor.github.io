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
  <img src="{{ '/assets/images/stolen-iphone/hong-kong-appear.jpg' | relative_url }}"
       alt="Find My showing stolen iPhone RoboMobo near Hung To Road in Kwun Tong, Hong Kong"
       style="max-width: 100%; border-radius: 4px;" />
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

A tighter view made the neighborhood clearer.

<p style="text-align: center;">
  <img src="{{ '/assets/images/stolen-iphone/robomobo-shenzhen-wide.png' | relative_url }}"
       alt="Tighter Find My map showing the location of stolen iPhone RoboMobo in Shenzhen"
       style="max-width: 100%; border-radius: 4px;" />
</p>

Hong Kong to Shenzhen is not exactly a surprising direction of travel for consumer electronics, although that fact does not tell me what was happening to this particular one. The phone might have been destined for resale, repair, refurbishment, parts recovery or some combination of those; I also have no reason to assume the person sending the messages was physically standing next to it.

What is visible is an object moving into an ecosystem exceptionally good at moving, repairing, disassembling, recombining and reselling electronics. In that respect a supply chain begins to look remarkably like the production systems I work on: loosely coupled services, intermediaries, queues, brokers, retries, undocumented dependencies and a resilience which is often much easier to admire when it isn't working against you.

Which is probably why I recognized it.

## Security Is Economics

Activation Lock does not make stealing an iPhone physically impossible. What it does is alter the economics after the theft.

That distinction matters because much of practical security works this way. We rarely make an undesirable action metaphysically impossible; instead, we make it more expensive, less profitable, more detectable, or inconvenient enough that another target becomes preferable.

A locked iPhone can still be transported, dismantled or sold to somebody who fails to inspect it carefully. It may still have parts value. What it cannot easily become is an ordinary used iPhone which somebody wipes, activates and resells as though nothing unusual happened.

Apple has pushed that model further by extending Activation Lock protections to supported parts from locked devices, reducing the usefulness of some components when the system identifies them as coming from an iPhone still protected by Activation Lock or Lost Mode.

<p style="text-align: center;">
  <img src="{{ '/assets/images/stolen-iphone/davidthefreeze.png' | relative_url }}"
       alt="reply from @davidthefreeze: 'They are more desperate for people to fall for this now too because Activation Lock extends to actual device parts now, not just the device itself. You can’t setup the parts for use in another device if Activation Lock is active on the device they are grabbing them from.'"
       style="max-width: 100%; border-radius: 4px;" />
</p>

<p style="text-align: center; font-size: 0.9em;">
  Thanks to Threads user <a href="https://www.threads.com/@davidthefreeze">@davidthefreeze</a> who <a href="https://www.threads.com/share/Py0KOdfo9/">pointed out the extended reach of Apple's Activation Lock</a>
</p>

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
