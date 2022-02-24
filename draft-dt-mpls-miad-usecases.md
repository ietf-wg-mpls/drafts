---
title: Use Cases for MPLS Function Indicators and Ancillary Data
abbrev: MIAD Usecases
docname: draft-saad-mpls-miad-usecases-01
category: info
ipr: trust200902
workgroup: MPLS Working Group
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:

 -
    ins: T. Saad
    name: Tarek Saad
    organization: Juniper Networks
    email: tsaad@juniper.net

 -
    ins: K. Makhijani
    name: Kiran Makhijani
    organization: Futurewei Technologies
    email: kiranm@futurewei.com

 -
    ins: H. Song
    name: Haoyu Song
    organization: Futurewei Technologies
    email: haoyu.song@futurewei.com



normative:

informative:

--- abstract

This document presents a number of use cases that have a common need for
encoding MPLS function indicators and ancillary data inside MPLS packets.
There has been significant recent interest in extending the MPLS data plane to
carry such ancillary data to address a number of use cases described in this
document.

The use cases described are not an exhaustive set, but rather the ones that are
actively discussed by members of the IETF MPLS, PALS and DETNET working groups
in the MPLS Open Design Team.

--- middle

# Introduction

This document describes important cases that require carrying
additional ancillary data within the MPLS packets, as well as the means to indicate
ancillary data is present.

These use cases have been identified by the MPLS working group design team
working on defining MPLS function Indicators and Ancillary Data (MIAD) for the MPLS data
plane. The MPLS ancillary data can be classified as:

- implicit, or "no-data" associated with a funciton indicator,
- within the label stack, e.g., encoded as labels, referred to as In Stack Data (ISD), and
- after the Bottom of Stack (BoS), referred to as Post Stack Data (PSD).

The use cases described in this document will be used to assist in
identifying requirements and issues to be considered for future resolution by
the working group.

- ID: {{?I-D.gandhi-mpls-ioam}} describes the applicability of IOAM to MPLS
  data plane.
- {{?RFC8986}} describes the network programming use case for SRv6 dataplane.
- {{?RFC8595}} describes how Service Function Chaining (SFC) can be achieved in
  an MPLS network by means of a logical representation of the Network Service
  Header (NSH) in an MPLS label stack. Some limitations of this approach that
  may be addressed by MIAD  are described in
  {{?I-D.lm-mpls-sfc-path-verification}}.

## Terminology

The following terminology is used in the document:

{: vspace="0"}
IETF Network Slice:
: a well-defined composite of a set of
endpoints, the connectivity requirements between subsets of these
endpoints, and associated requirements; the term 'network slice'
in this document refers to 'IETF network slice' as defined in 
{{?I-D.ietf-teas-ietf-network-slices}}.

IETF Network Slice Controller (NSC):
: controller that is used to realize an IETF network slice {{?I-D.ietf-teas-ietf-network-slices}}.

Network Resource Partition:
: the collection of resources that are used to support a slice aggregate.

Time Sensitive Networking:
: Networks that transport time sensitive traffic.

## Acronyms and Abbreviations

> ISD: In-stack data

> PSD: Post-stack data

> MIAD: MPLS Indicators and Ancillary Data

# Use Cases

## No Further Fastreroute

MPLS Fast Reroute (FRR) {{?RFC4090}}, {{?RFC5286}} and {{?RFC7490}} is a useful
and widely deployed tool for minimizing packet loss in the case of a link or
node failure.

Several cases exist where, once FRR has taken place in an MPLS
network and resulted in rerouting a packet away from the failure, a second FRR that
impacts the same packet to rerouting  is not helpful, and may even be disruptive. 

For example, in such a case, the packet may continue to loop until its TTL
expires.  This can lead to link congestion and further packet loss.
Thus, the attempt to prevent a packet from being dropped may instead
affect many other packets. A proposal to address this is presented in {{?I-D.kompella-mpls-nffrr}}.


## In-situ OAM

In-situ Operations, Administration, and Maintenance (IOAM) may record operational
and telemetry information within the packet while the packet traverses a
particular path in a network domain.

The term "in-situ" refers to the fact that the IOAM data fields are added to
the data packets rather than being sent within the probe packets specifically
dedicated to OAM or Performance Measurement (PM). 

IOAM can run in two modes End-to-End (E2E) and Hop-by-Hop (HbH).  In E2E mode,
only the encapsulating and decapsulating nodes will process IOAM data fields.
In HbH mode, the encapsulating and decapsulating nodes as well as intermediate
IOAM-capable nodes process IOAM data fields. 

The IOAM data fields are defined in {{?I-D.ietf-ippm-ioam-data}}, and can be
used for various use-cases of OAM and PM.

{{?I-D.gandhi-mpls-ioam-sr}} defines how IOAM data fields are transported using
the MPLS data plane encapsulations, including Segment Routing (SR) with MPLS
data plane (SR-MPLS).

The IOAM data may be added after the bottom of the MPLS label stack. The IOAM
data fields can be of fixed or incremental size as defined in
{{?I-D.ietf-ippm-ioam-data}}.  {{?I-D.gandhi-mpls-ioam}} describes
applicability of IOAM to MPLS dataplane.  The encapsulating MPLS node needs to
know if the decapsulating MPLS node can process the IOAM data before adding it
in the packet. In HbH IOAM mode, nodes that are capable of processing IOAM will
intercept and process the IOAM data. The presence of IOAM data will be
transparent to nodes that does not support or do not participate in the IOAM
process.

## Network Slicing

{{?I-D.ietf-teas-ietf-network-slices}} specifies the definition of 
an IETF Network Slice. It further discusses the general framework for
requesting and operating IETF Network Slices, their characteristics, and the
necessary system components and interfaces.

Multiple network slices can be realized on top of a single shared network.  

In order to overcome scale challenges, IETF Network Slices may be aggregated
into groups according to similar characteristics.  The slice aggregate
{{?I-D.bestbar-teas-ns-packet}} is a construct that comprises of the traffic
flows of one or more IETF Network Slices of similar characteristics.

A router that requires forwarding of a packet that belongs to a slice aggregate
may have to decide on the forwarding action to take based on selected
next-hop(s), and the forwarding treatment (e.g., scheduling and drop policy) to
enforce based on the associated  per-hop behavior.

The routers in the network that forward traffic over links that are shared by
multiple slice aggregates need to identify the slice aggregate packets
in order to enforce the associated forwarding action and treatment.

An IETF network slice MAY support the following key features:

1. A Slice Selector
2. A Network Resource Partition associated with a slice aggregate.
3. A Path selection criteria
4. Verification that per slice SLOs are being met. This may be done by active measurements
   (inferred) or by using hybrid measurement methods, e.g., IOAM.
5. Additionally, there is an on-going discussion on using Service Functions
   (SFs) with network slices. This may require insertion of an NSH.
6. For multi-domain scenarios, a packet that traverses multiple domains may
   encode different identifiers within each domain.

### Global Identifier as Slice Selector

A Global Identifier as a Slice Selector (GISS) can be encoded in the MPLS
packet as defined in {{?I-D.kompella-mpls-mspl4fa}},
{{?I-D.li-mpls-enhanced-vpn-vtn-id}}, and
{{?I-D.decraene-mpls-slid-encoded-entropy-label-id}}.  The Global Identifier
Slice Selector can be used to associate the packets to the slice aggregate,
independent of the MPLS forwarding label that is bound to the destination.
LSRs use the MPLS forwarding label to determine the forwarding next-hop(s), and
use the Global Identifier Slice Selector field in the packet to infer the
specific forwarding treatment that needs to be applied on the packet.

The GISS can be encoded within an MPLS label carried in the packet's
MPLS label stack.  All packets that belong to the same slice aggregate MAY
carry the same GISS in the MPLS label stack.  It is also possible to have
multiple GISS's map to the same slice aggregate. The GISS can be encoded in an
MPLS label and may appear in several positions in the MPLS label stack.

### Forwarding Label as a Slice Selector

{{?RFC3031}} states in Section 2.1 that: 'Some routers analyze a packet's
network layer header not merely to choose the packet's next hop, but also to
determine a packet's "precedence" or "class of service"'.  

It is possible by assigning a unique MPLS forwarding label to each slice
aggregate (FEC) to distinguish the packets forwarded to the same destination
but that belong to different slice aggregates.  In this case, LSRs can use the
top forwarding label to infer both the forwarding action and the forwarding
treatment to be invoked on the packets.  A similar approach is described in
{{?I-D.ietf-spring-resource-aware-segments}} and {{?I-D.bestbar-teas-ns-packet}}.

## Time Sensitive Networking

The routers in a network can perform two distinct functions on incoming
packets, namely forwarding (where the packet should be sent) and scheduling
(when the packet should be sent). Time Sensitive Networking (TSN) and
Deterministic Networking provide several mechanisms for scheduling under the
assumption that routers are time synchronized.  The most effective mechanisms
for delay minimization involve per-flow resource allocation.

Segment Routing (SR) is a forwarding paradigm that allows encoding forwarding
instructions in the packet in a stack data structure, rather than being
programmed into the routers.  The SR instructions are contained within a packet
in the form of a First-in First-out stack dictating the forwarding decisions of
successive routers.  Segment routing may be used to choose a path sufficiently
short to be capable of providing a low end-to-end latency but does
not influence the queueing of individual packets in each router along that path.

TSN is required for networks transporting such time sensitive traffic,
whose packets are required to be delivered to their final
destination by a given time.


### Stack Based Methods for Latency Control

One efficient data structure for inserting local deadlines into
the headers is a "stack", similar to that used in Segment Routing to
carry forwarding instructions.  The number of deadline values in the
stack equals the number of routers the packet needs to traverse in
the network, and each deadline value corresponds to a specific
router.  The Top-of-Stack (ToS) corresponds to the first router's
deadline while the Bottom-of-Stack (BoS) refers to the last's.  All
local deadlines in the stack are later or equal to the current time
(upon which all routers agree), and times closer to the ToS are
always earlier or equal to times closer to the BoS.


The ingress router inserts the deadline stack into the packet
headers; no other router needs to be aware of the requirements of the
time sensitive flows.  Hence admitting a new flow only requires
updating the information base of the ingress router.

MPLS LSRs that expose the Top of Stack (ToS) label can also inspect the
associated "deadline" carried in the packet (either in MPLS stack or after BoS). 

### Stack Entry Format

A number of different time formats commonly used in networking applications
and can be used to encode the local deadlines.

For the forwarding sub-entry we could adopt like SR-MPLS standard
32-bit MPLS labels (which contain a 20-bit label and BoS bit), and
thus SR-TSN stack entries could be 64-bits in size comprising a 32-bit
MPLS label and the aforementioned nonstandard 32-bit timestamp.

Alternatively, an SR-TSN stack entry could be 96 bits in length
comprising a 32-bit MPLS label and either of the standardized 64-bit
timestamps.

## NSH Based Service Function Chaining

{{?RFC8595}} describes how Service Function Chaining (SFC) can be realized in
an MPLS network by emulating the NSH by using only MPLS label stack elements.

A reference to the NSH SFC use case is defined in {{?RFC8596}}.

The approach in {{?RFC8595}} introduces some limitations that are discussed in
{{?I-D.lm-mpls-sfc-path-verification}}. This approach, however, can benefit
from the solution framework introduced with MIAD.

For example, it may be possible for the Network Service Header (NSH) to be embedded in an
Extended Header (EH) to support the Path ID and any metadata that needs to be
carried and and exchanged between Service Function Forwarders (SFFs).


## Network Programming

In SR, an ingress node steers a packet through an ordered list of instructions,
called "segments".  Each one of these instructions represents a
function to be called at a specific location in the network.  A
function is locally defined on the node where it is executed and may
range from simply moving forward in the segment list to any complex
user-defined behavior. 

Network Programming combines Segment Routing (SR) functions to achieve a
networking objective that goes beyond mere packet routing.

It may be desirable to encode a pointer to function and its arguments
within an MPLS packet transport header. For example, in MPLS we can encode the
FUNC::ARGs within the label stack or after the Bottom of Stack to support the
equivalent of FUNC::ARG in SRv6 as described in {{?RFC8986}}.

## Application Aware Networking

Application-aware Networking (APN) as described in
{{?I-D.li-apn-problem-statement-usecases}} allows application-aware information
(i.e., APN attributes) including APN identification (ID) and/or APN parameters
(e.g.  network performance requirements) to be encapsulated at network edge
devices and carried in packets traversing an APN domain. 

The APN data is carried in packets to facilitate service provisioning, perform
fine-granularity traffic steering and network resource adjustment. To support
APN in MPLS networks, mechanisms are needed to carry such APN data in MPLS
encapsulated packets.

# Co-existence of Usecases

Two or more of the aforementioned use cases MAY co-exist in the same packet.
Some examples of such use cases are described below.

## IOAM with Network Slicing

IOAM may provide key functions with network slicing to help ensure that
critical network slice SLOs are being met by the network provider.

In such a case, IOAM is able to collect key performance measurement parameters of
network slice traffic flows as it traverses the transport network.

This may require, in addition to carrying a specific network slice selector
(e.g., GISS), the MPLS network slice packets may have to also carry IOAM
ancillary data.

Note that the IOAM ancillary data may have to be modified, and updated on
some/all LSRs traversed by the network slice MPLS packets.

## IOAM with Time-Sensitive Networking

IOAM operation may also be desirable on MPLS packets that carry time-sensitive
related data. Similarly, this may require the presence of multiple ancilary data
(whether In-stack or Post-stack ancillary data) to be present in the same MPLS packet.

# IANA Considerations

This document has no IANA actions.

# Security Considerations

This document introduces no new security considerations.

# Acknowledgement

The authors gratefully acknowledge the input of the members of the
MPLS Open Design Team.


# Contributors

The following individuals contributed to this document:

~~~

   Greg Mirsky
   Ericsson
   Email: gregimirsky@gmail.com

   Loa Andersson
   Bronze Dragon Consulting
   Email: loa@pi.nu

~~~
