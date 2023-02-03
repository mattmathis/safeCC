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
  group: tsvwg
  mail: tsvwg@ietf.org
  github: https://github.com/mattmathis/safeCC/

author:

 -
    fullname: Matt Mathis
    organization: Measurement Lab
    abbrev: MLab
    email: mattmathis@measurementlab.net

normative:

informative:


--- abstract

We present criteria for evaluating Congestion Control for behaviors that have the potential to cause harm to other Internet applications or users.

Although our primary focus is the safety of transport layer congestion control, many of these criteria need to be applied to all protocol layers: entire stacks, libraries and applications themselves.

--- middle

# Introduction

We present criteria for evaluating Congestion Control for behaviors that have the potential to cause harm to other Internet applications or users.  Although our primary focus is the safety of congestion control, many of these criteria need to be applied to all protocol layers: entire stacks, libraries and applications themselves.

Ideally we would like to cast these criteria as requirements; however such an effort will fail because many of them have known exceptions that don't seem to be important.

As an interim position: all implementations SHOULD comply with all criteria, and MUST document all exceptions: under what circumstances and how severely they fail to comply?

The open research question will be deciding which exceptions can be tolerated and which are grounds for preventing protocols or algorithms from progressing into full standards.

To prove the criteria described in the note they should be used to evaluate current and legacy algorithms: we expect to find alignment between known implementation pathologies and failed criteria.  Discrepancies may suggest additional criteria or sharpen our understanding of how to decide if a failed criteria is material or not.

The phrase "under adverse conditions" refers to any increase in any congestion signals (loss, delay, marks or reduced queue space or capacity) from any starting state.   For example introducing 1 Mb/s cross traffic to an otherwise ideal 10 Gb/s link is an adverse condition that SHOULD NOT trigger any of the misbehaviors indicated below.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Tentative list of criteria

1. Free from regenerative congestion - Adverse conditions do not cause additional presented load.

1. Free from congestion collapse - Adverse conditions do not cause declining goodput / overhead ratio

1. Bound control frequency - Control frequency scales with 1/rtt but is insensitive to data rate.

1. Bound steady state losses - Steady state bulk transport should not cause more than 2\% loss over any unchanging network.

1. Bound slowstart duration and loss - Slowstart into a droptail queue should not cause more than one RTT of loss nor cause more than 50% loss for that RTT.   e.g. Provisional window/rate reductions should start when losses/disorder is first detected, even before the loss recovery can decide if the missing segments are due to reordering or loss.

1. Bound losses on link changes - Step changes in link properties (RTT, bandwidth or queue size) or cross traffic should not cause losses that are larger than the change in maximum flight size supported by the link. Specifically, during loss recovery the transport is not permitted to send more data than reported at the receiver.  (Conservative property from PRR.)

1. No unnecessary slowstarts - All application stacks must use connection caching, CC state caching or some other mechanism such that application workloads are prevented from causing persistent or repeated overlapping slowstarts.

1. Monotonic response - The CCA should have monotonic response to all congestion signals that it responds to (loss, marks, delay, etc) otherwise it will have multiple stable operating points for the same network conditions.  It would be likely to exhibit stable pathologies such as latecomer (dis)advantage.

1. Freedom from starvation - (need a new strong definition).   Flows below some resource threshold (data rate, window size, ConEx marks, etc) will successfully search upwards, as long as there is either idle capacity or other flows above the same(?) threshold.

1. Bound standing queue - Do not create steady state standing queues larger than k\*minRTT\*maxBW, for some prescribed k, to be defined.

1. Maintain queue headroom - Individual flows do not keep queues pegged at full even if the queues are substantially smaller than minRTT\*maxBW.  When there is queue full, CC should reduce its window enough to create some small headroom to prevent locking out new flows

1. Balanced probe size - Balance the worst case queue backlog against the need to trigger mode shifting in links that batch data.   This should become a global (policy) parameter of the Internet, because the queue backlogs force jitter on flows trying to do realtime without QoS.

1. Self scaling - at all layers.  If the network is too slow, the application must also slow down to avoid "stacking" requests.


# Security Considerations

TODO Security

This document provides evaluation criteria for Congestion control and other implementation or algorithms that might be deployed on the internet.   It has no direct security considerations of its own.

Over the long haul it is expected to increase the overall robustness of the Internet by helping to eliminate pathological congestion behaviors that have the potential cause the Internet to be fragile under some conditions.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
