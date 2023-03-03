---

title: Safe Congestion Control
abbrev: Safe CC
docname: draft-mathis-tsvwg-safecc-latest
date: {DATE}
category: exp
submissiontype: IETF
ipr: trust200902
area: Transport
workgroup: tsvwg

number:

consensus: true
v: 3
area: AREA
workgroup: WG Working Group
keyword:
 - congestion control
venue:
  group: TSVWG
  mail: tsvwg@ietf.org
  github: mattmathis/safeCC/

author:

 -
    fullname: Matt Mathis
    organization: Freelance, Measurement Lab
    abbrev: MLab
    email: mattmathis@measurementlab.net
    uri: "https://mattmathis.net/"

normative:

informative:


--- abstract

We present criteria for evaluating Congestion Control Algorithms for behaviors that have the potential to cause harm to Internet applications or users.

Although our primary focus is the safety of transport layer congestion control, many of these criteria should be applied to all protocol layers: entire stacks, libraries and applications themselves.

--- middle

# Preamble

This document is written in extra terse congestion control jargon, approximately one sentence per paragraph.

Editorial comments to authors are enclosed in [square brackets].  Outstanding action items may be tagged with @@@@@.

[Remove this section before publication]

# Introduction
We present criteria for evaluating Congestion Control Algorithms for behaviors that have the potential to cause harm to Internet applications or users.

Ideally we would cast these criteria as requirements; however such an effort is doomed to fail because many of them have technical exceptions that are unavoidable in ways that are not important. For an example of this issue see {{noCollapse}}

As an interim position: all implementations SHOULD comply with all criteria, and MUST document all exceptions and evaluate the risks associated with the exceptions. Under what circumstances and how severely they fail to comply what is the extent of the harm that they might cause?

To prove the criteria described in this note they should be used to evaluate current and legacy algorithms: we expect to find alignment between known implementation pathologies and failed criteria.  Discrepancies may suggest additional criteria or sharpen our understanding of how to decide if a failed criteria is material or not.

Indeed, Reno[Reno] and Cubic[Cubic] are known to fail the criteria presented here, and as a consequence exhibit pathologies including bufferbloat[bufferbloat] and poor scaling[Scaling].


# Conventions and Definitions

{::boilerplate bcp14-tagged}

**Exhaustion**
: a network overloaded to the extent that the average delivery rate is below one segment per flow per RTT.

**Material**
: failing a criteria in a manner that is likely to cause pathological behaviors under some conditions.

**Non-Material**
: technically failing some criteria, but unimportant, insignificant or otherwise unlikely to cause pathological behaviors.

**Under adverse conditions**
: refers to any changes in behavior when subject to any increase in any congestion signals (loss, delay, marks or reduced queue space or capacity) from any initial state.   For example introducing 1 Mb/s cross traffic to an otherwise ideal 10 Gb/s link is an adverse condition that SHOULD NOT trigger any of the misbehavior's indicated below.

# Tentative list of criteria

## Free from congestion collapse  {#noCollapse}

Adverse conditions do not cause increasing overhead, specifically do not cause duplicate data at the receiver.

Test: for a fixed work load, the overhead must be constant, independent of the network congestions across the entire operating range of the application or network

If there is packet loss, the retransmits must exactly match the packet losses 

Example of an application that can cause congestion collapse: a download engine that responds to transient network errors or persistent congestion by restarting downloads from the beginning. 

## Free from regenerative congestion {#noRegeneration}

Adverse conditions do not cause additional presented load.



## Bound steady state losses

Steady state bulk transport should not cause more than 2% loss over any unchanging network.

Any transport with some form of selective acknowledgements can easily run at a much higher loss rate.   The real problem is the harm caused to all single packet transactions, including DNS and connection establishment for nearly all protocols and services.   Single packet transactions generally can only use an RTO timer for recovery, typically without any preceding RTT measurement, thus they often take several orders of magnitude more time than any selective acknowledgment based recovery.

The question at hand is really how much harm do we want to permit transport to inflict on all other protocols (and not transport vs transport).

For example, are we ok with happy eyeballs getting the wrong answer 2% of the time, because the IPv6 connection establishment failed on the first message?

BTW I consider 2% to be a bit high: 1% or 0.1% would be better, however Reno and Cubic can both easily cause much more loss.

We need some studies to decide the appropriate value for the final document.

## Bound slowstart duration and loss
Slowstart into a droptail queue should not
cause more than one RTT of loss nor cause more than 50% loss for that RTT.   e.g. Provisional window/rate reductions should start when losses/disorder is first detected, even before the loss recovery can decide if the missing segments are due to reordering or loss.

## Bound losses on link changes
Step changes in link properties (RTT, bandwidth or queue size) or cross traffic should not cause losses that are larger than the change in maximum flight size supported by the link. Specifically, during loss recovery the transport is not permitted to send more data than reported at the receiver.  (Conservative property from PRR.)

## No unnecessary slowstarts

All application stacks must use connection caching, CC state caching or some other mechanism such that application workloads are prevented from causing persistent or repeated overlapping slowstarts.


## Freedom from starvation

(need a new strong definition).   Flows below some resource threshold (data rate, window size, ConEx marks, etc) will successfully search upwards, as long as there is either idle capacity or other flows above the same(?) threshold.

## Bound standing queue

Do not create steady state standing queues larger than k\*minRTT\*maxBW, for some prescribed k, to be defined.

## Maintain queue headroom

Individual flows do not keep queues pegged at full even if the queues are substantially smaller than minRTT\*maxBW.  When there is queue full, CC should reduce its window enough to create some small headroom to prevent locking out new flows

## Bound control frequency

Control frequency scales with 1/rtt but is insensitive to data rate.

## Monotonic response
The CCA should have monotonic response to all congestion signals that it responds to (loss, marks, delay, etc) otherwise it will have multiple stable operating points for the same network conditions.  It would be likely to exhibit stable pathologies such as latecomer (dis)advantage.

## Balanced probe size

Balance the worst case queue backlog against the need to trigger mode shifting in links that batch data.   This should become a global (policy) parameter of the Internet, because the queue backlogs force jitter on flows trying to do realtime without QoS.

## Self scaling

at all layers.  If the network is too slow, the application must also slow down to avoid "stacking" requests.


# Security Considerations

TODO Security

This document provides evaluation criteria for Congestion control and other implementation or algorithms that might be deployed on the internet.   It has no direct security considerations of its own.

Over the long haul it is expected to increase the overall robustness of the Internet by helping to eliminate pathological congestion behaviors that have the potential cause the Internet to be fragile under some conditions.

# IANA Considerations

This document has no IANA actions.


--- back

# Estimating min RTT
This is known to be hard@@@

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
