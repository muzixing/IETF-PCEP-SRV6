---
v: 3
docname: draft-ietf-pce-segment-routing-ipv6-19
cat: std
stream: IETF
consensus: true
pi:
  toc: 'yes'
  tocompact: 'yes'
  tocdepth: '4'
  tocindent: 'yes'
  symrefs: 'yes'
  sortrefs: 'no'
  rfcedstyle: 'yes'
  subcompact: 'no'
  compact: 'yes'
  iprnotified: 'Yes'
  strict: 'no'
title: Path Computation Element Communication Protocol (PCEP) Extensions for Segment Routing leveraging the IPv6 dataplane
abbrev: PCEP-SRv6
area: Routing
wg: PCE Working Group
author:
- ins: C. Li
  name: Cheng Li(Editor)
  org: Huawei Technologies
  street: Huawei Campus, No. 156 Beiqing Rd.
  city: Beijing
  code: '100095'
  country: China
  email: c.l@huawei.com
- ins: M. Negi
  name: Mahendra Singh Negi
  org: RtBrick Inc
  city: Bangalore
  region: Karnataka
  country: India
  email: mahend.ietf@gmail.com
- ins: S. Sivabalan
  name: Siva Sivabalan
  org: Ciena Corporation
  email: msiva282@gmail.com
- ins: M. Koldychev
  name: Mike Koldychev
  org: Cisco Systems, Inc.
  country: Canada
  email: mkoldych@cisco.com
- ins: P. Kaladharan
  name: Prejeeth Kaladharan
  org: RtBrick Inc
  city: Bangalore
  region: Karnataka
  country: India
  email: prejeeth@rtbrick.com
- ins: Y. Zhu
  name: Yongqing Zhu
  org: China Telecom
  street: 109 West Zhongshan Ave, Tianhe District
  city: Bangalore
  region: Guangzhou
  country: P.R. China
  email: zhuyq8@chinatelecom.cn

contributor:
- name: Dhruv Dhody
  org: Huawei
  country: India
  email: dhruv.ietf@gmail.com
- name: Huang Wumin
  org: Huawei Technologies
  street: Huawei Building, No. 156 Beiqing Rd.
  city: Beijing
  code: 100095
  country: China
  email: huangwumin@huawei.com
- name: Shuping Peng
  org: Huawei Technologies
  street: Huawei Building, No. 156 Beiqing Rd.
  city: Beijing
  code: 100095
  country: China
  email: pengshuping@huawei.com
- name: Ran Chen
  org: ZTE Corporation
  country: China
  email: chen.ran@zte.com.cn


normative:
  RFC3209:
  RFC5440:
  RFC5511:
  RFC6952:
  RFC8126:
  RFC8231:
  RFC8281:
  RFC8408:
  RFC8491:
  RFC8253:
  RFC8664:
  RFC8986:
informative:
  RFC4657:
  RFC7942:
  RFC8051:
  RFC8402:
  RFC8754:
  RFC9256:
  RFC9352:


--- abstract


Segment Routing (SR) can be used to steer packets through an IPv6 or MPLS network using the source routing paradigm. SR enables
any head-end node to select any path without relying on a hop-by-hop signaling technique (e.g., LDP or RSVP-TE).

A Segment Routed Path can be derived from a variety of mechanisms, including an IGP Shortest Path Tree (SPT), explicit configuration, or a PCE.

Since SR can be applied to both MPLS and IPv6 forwarding planes, a PCE should be able to compute SR-Path for both MPLS and IPv6 forwarding planes. The PCEP extension and mechanisms to support SR-MPLS have been defined. This document describes the extensions required for SR support for IPv6 data plane in the Path Computation Element communication Protocol (PCEP).

--- middle

# Introduction

As defined in {{RFC8402}}, Segment Routing (SR) architecture allows the source node to steer a packet through a path indicated by an ordered list of instructions, called segments. A segment can represent any instruction, topological or service-based, and it can have a semantic local to an SR node or global within an SR domain.

When the SR architecture is applied to the MPLS forwarding plane, it is called SR-MPLS. When the SR architecture is applied to the IPv6 data plane, is is called SRv6 (Segment Routing over IPv6 data plane) {{RFC8754}}.


An SR path can be derived from an IGP Shortest Path Tree (SPT), but SR-TE (Segment Routing Traffic Engineering) paths may not follow IGP SPT. Such paths may be chosen by a suitable network planning tool, or a PCE and provisioned on the ingress node.


{{RFC5440}} describes Path Computation Element communication Protocol (PCEP) for communication between a Path Computation Client (PCC) and a Path Computation Element (PCE) or between a pair of PCEs. A PCE or a PCC operating as a PCE (in hierarchical PCE environment) computes paths for MPLS Traffic Engineering LSPs (MPLS-TE LSPs) based on various constraints and optimization criteria.

{{RFC8231}} specifies extensions to PCEP that allow a stateful PCE to compute and recommend network paths in compliance with {{RFC4657}} and defines objects and TLVs for MPLS-TE LSPs. Stateful PCEP extensions provide synchronization of LSP state between a PCC and a PCE or between a pair of PCEs, delegation of LSP control, reporting of LSP state from a PCC to a PCE, controlling the setup and path routing of an LSP from a PCE to a PCC. Stateful PCEP extensions are intended for an operational model in which LSPs are configured on the PCC, and control over them is delegated to the PCE.

A mechanism to dynamically initiate LSPs on a PCC based on the requests from a stateful PCE or a controller using stateful PCE is specified in {{RFC8281}}. As per {{RFC8664}}, it is possible to use a stateful PCE for computing one or more SR-TE paths taking into account various constraints and objective functions. Once a path is chosen, the stateful PCE can initiate an SR-TE path on a PCC using PCEP extensions specified in {{RFC8281}} using the SR specific PCEP extensions specified in {{RFC8664}}. {{RFC8664}} specifies PCEP extensions for supporting a SR-TE LSP for the MPLS data plane. This document extends {{RFC8664}} to support SR for the IPv6 data plane. Additionally, using procedures described in this document, a PCC can request an SRv6 path from either a stateful or stateless PCE. This specification relies on the PATH-SETUP-TYPE TLV and procedures specified in {{RFC8408}}.

This specification provides a mechanism for a network controller (acting as a PCE) to instantiate candidate paths for an SR Policy onto a
head-end node (acting as a PCC) using PCEP. For more information on the SR Policy Architecture, see {{RFC9256}} which is applicable to both SR-MPLS and SRv6.

## Requirements Language

{::boilerplate bcp14-tagged}



# Terminology

This document uses the following terms defined in {{RFC5440}}: PCC, PCE, PCEP, PCEP Peer.

This document uses the following terms defined in {{RFC8051}}: Stateful PCE, Delegation.

The message formats in this document are specified using Routing
Backus-Naur Format (RBNF) encoding as specified in {{RFC5511}}.

Further, following terms are used in the document,

MSD:
: Maximum SID Depth.


PST:
: Path Setup Type.


SR:
: Segment Routing.


SID:
: Segment Identifier.


SRv6:
: Segment Routing for IPv6 forwarding plane.


SRH:
: IPv6 Segment Routing Header.


SRv6 Path:
: IPv6 Segment List (List of IPv6 SIDs representing a path in IPv6 SR domain in the context of this document)


Further, note that the term LSP used in the PCEP specifications,
would be equivalent to an SRv6 Path (represented as a list of SRv6
segments) in the context of supporting SRv6 in PCEP.


# Overview of PCEP Operation in SRv6 Networks {#Overview}

Basic operations for PCEP speakers are as per {{RFC8664}}. SRv6 Paths computed by a PCE can be represented as an ordered list of SRv6 segments of 128-bit value.

{{RFC8664}} defined a new Explicit Route Object (ERO) subobject denoted by "SR-ERO subobject" capable of carrying a SID as well as the identity of the node/adjacency represented by the SID for SR-MPLS. SR-capable PCEP speakers can generate and/or process such an ERO subobject. An ERO containing SR-ERO subobjects can be included in the PCEP Path Computation Reply (PCRep) message defined in {{RFC5440}}, the PCEP LSP Initiate Request message (PCInitiate) defined in {{RFC8281}}, as well as in the PCEP LSP Update Request (PCUpd) and PCEP LSP State Report (PCRpt) messages defined in {{RFC8231}}. {{RFC8664}} also defines a new Reported Route Object(RRO) called SR-RRO to represents the SID list that was applied by the PCC, that is, the actual path taken by the LSP in SR-MPLS network.

This document defines new subobjects "SRv6-ERO" and "SRv6-RRO" in the ERO and the RRO respectively to carry the SRv6 SID (IPv6 Address). SRv6-capable
PCEP speakers MUST be able to generate and/or process these subobjects.

When a PCEP session between a PCC and a PCE is established, both PCEP speakers exchange their capabilities to indicate their ability to support SRv6 specific functionality as described in {{SRv6-PCE-Capability-sub-TLV}}.

In summary, this document,

* Defines a new PCEP capability for SRv6.

* Defines a new subobject SRv6-ERO in ERO.

* Defines a new subobject SRv6-RRO in RRO.

* Defines a new path setup type (PST) {{RFC8408}} carried in the
PATH-SETUP-TYPE TLV and the PATH-SETUP-TYPE-CAPABILITY TLV.


## Operation Overview {#Operation-overview}

In SR networks, an SR source node encodes all packets being steered onto an SR path with a list of segments. The segment list has all necessary information to guide the packets from the ingress node to the egress node of the path, and hence there is no need for any signaling protocol.


For the use of an IPv6 control plane but an MPLS data plane, mechanism remains the same as specified in {{RFC8664}}.

This document describes the extension to support SRv6 in PCEP. A PCC or PCE indicates its ability to support SRv6 during the PCEP
session Initialization Phase via a new SRv6-PCE-CAPABILITY sub-TLV
(see details in {{SRv6-PCE-Capability-sub-TLV}}).


## SRv6-Specific PCEP Message Extensions {#SRv6-Specific-PCEP-Message-Extensions}

As defined in {{RFC5440}}, a PCEP message consists of
a common header followed by a variable length body made up of
mandatory and/or optional objects. This document does not require any
changes in the format of PCReq and PCRep messages specified in {{RFC5440}},
PCInitiate message specified in {{RFC8281}}, and PCRpt and PCUpd messages
specified in {{RFC8231}}. However, PCEP messages pertaining to SRv6 MUST
include PATH-SETUP-TYPE TLV in the RP (Request Parameters) or SRP (Stateful PCE Request Parameters) object to clearly
identify that SRv6 is intended.

<!-- In other words, a PCEP speaker MUST NOT infer whether or
   not a PCEP message pertains to SRv6 from any other object or
   TLV. -->



# Object Formats {#Object-Formats}

## The OPEN Object {#The-OPEN-Object}

### The SRv6 PCE Capability sub-TLV {#SRv6-PCE-Capability-sub-TLV}

This document defines a new Path Setup Type (PST) {{RFC8408}} for SRv6, as follows.

* PST = 3 : Path is setup using SRv6.


A PCEP speaker indicates its support of the function described in this document by sending a PATH-SETUP-TYPE-CAPABILITY TLV in the OPEN object with this new PST "3" included in the PST list.

This document also defines the SRv6-PCE-CAPABILITY sub-TLV. PCEP speakers use this sub-TLV to exchange information about their SRv6 capability. If a PCEP speaker includes PST=3 in the PST List of the PATH-SETUP-TYPE-CAPABILITY TLV then it MUST also include the SRv6-PCE-CAPABILITY sub-TLV inside the PATH-SETUP-TYPE-CAPABILITY TLV. For further error handling, please see {{Procedures}}.

The format of the SRv6-PCE-CAPABILITY sub-TLV is shown in the following figure.

~~~~
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|            Type=27            |            Length             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|            Reserved           |             Flags         |N| |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   MSD-Type    | MSD-Value     |   MSD-Type    | MSD-Value     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
//                             ...                             //
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   MSD-Type    | MSD-Value     |           Padding             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~
{: #SRv6-PCE-CAPABILITY-sub-TLV-format title='SRv6-PCE-CAPABILITY sub-TLV format'}

The code point for the TLV type is 27 and the format is compliant with the PCEP TLV format defined in {{RFC5440}}. That is, the sub-TLV is composed of 2 octets for the type, 2 octets specifying the length, and a Value field. The Type field when set to 27 identifies the SRv6-PCE-CAPABILITY sub-TLV and the presence of the sub-TLV indicates the support for the SRv6 paths in PCEP. The Length field defines the length of the value portion in octets. The TLV is padded to 4-octet alignment, and padding is not included in the Length field. The (MSD-Type,MSD-Value) pairs are OPTIONAL. The number of (MSD-Type,MSD-Value) pairs can be determined from the Length field of the TLV.

The value comprises of -

> Reserved: 2 octet, this field MUST be set to 0 on
> transmission, and ignored on receipt.

> Flags: 2 octet, one bit is currently assigned in this
> document. {{SRv6-PCE-Capability-Flags}}

> * N bit: A PCC sets this flag bit to 1 to indicate that it
>   is capable of resolving a Node or Adjacency Identifier (NAI)
>   to an SRv6-SID.
> * Unassigned bits MUST be set to 0 and ignored on
>   receipt.

> A pair of (MSD-Type, MSD-Value): Where MSD-Type (1 octet) is
> as per the IGP MSD Type registry created by {{RFC8491}} and populated
> with SRv6 MSD types as per {{RFC9352}};
> MSD-Value (1 octet) is as per {{RFC8491}}.

The SRv6 MSD information advertised via SRv6-PCE-Capability sub-TLV conveys the SRv6 capabilities of the PCEP speaker alone. However, when it comes to the computation of an SR Policy for the SRv6 dataplane, the SRv6 MSD capabilities of all the intermediate SRv6 Endpoint node as well as the tailend node also need to be considered to ensure those midpoints are able to correctly process their segments and for the tailend to dispose of the SRv6 encapsulation. The SRv6 MSD capabilities of these other nodes MAY be learned as part of the topology information via IGP/BGP-LS or via PCEP if the PCE also happens to have PCEP sessions to those nodes.

It is RECOMMENDED that the SRv6 MSD information be not included in the SRv6-PCE-Capability sub-TLV in deployments where the PCE is able to obtain this via IGP/BGP-LS as part of the topology information.

## The RP/SRP Object {#The-SRP-Object}

This document defines a new Path Setup Type (PST=3) for SRv6. In order to indicate that the path is for SRv6, any RP or SRP object MUST include the PATH-SETUP-TYPE TLV as specified in [RFC8408], where PST is set to 3.


## ERO {#ERO}

In order to support SRv6, a new "SRv6-ERO" subobject is defined for inclusion in the ERO.


### SRv6-ERO Subobject {#SRv6-ERO-Subobject}

An SRv6-ERO subobject is formatted as shown in the following figure.


~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |L|   Type=40   |     Length    | NT    |     Flags     |V|T|F|S|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              Reserved         |      Endpoint Behavior        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   |                      SRv6 SID (optional)                      |
   |                     (128-bit)                                 |
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   //                    NAI (variable, optional)                 //
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   |                     SID Structure (optional)                  |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~
{: #SRv6-ERO-Subobject-Format title='SRv6-ERO Subobject Format'}

The fields in the SRv6-ERO subobject are as follows:

The 'L' Flag: Indicates whether the subobject represents a
loose-hop (see {{RFC3209}}). If this flag is set to
zero, a PCC MUST NOT overwrite the SID value present in the SRv6-ERO
subobject. Otherwise, a PCC MAY expand or replace one or more SID
values in the received SRv6-ERO based on its local policy.

Type: indicates the content of the subobject, i.e. when the field
is set to 40, the suboject is an SRv6-ERO subobject
representing an SRv6 SID.

Length: Contains the total length of the subobject in octets. The
Length MUST be at least 24, and MUST be a multiple of 4. An SRv6-ERO
subobject MUST contain at least one of an SRv6-SID or an NAI. The S
and F bit in the Flags field indicates whether the SRv6-SID or NAI
fields are absent.

NAI Type (NT): Indicates the type and format of the NAI contained
in the object body, if any is present. If the F bit is set to one
(see below) then the NT field has no meaning and MUST be ignored by
the receiver. This document reuses NT types defined in {{RFC8664}}, but assigns them new meanings appropriate to SRv6.


> If NT value is 0, the NAI MUST NOT be included.

> When NT value is 2, the NAI is as per the 'IPv6 Node ID'
> format defined in {{RFC8664}}, which specifies an
> IPv6 address. This is used to identify the owner of the SRv6
> Identifier. This is optional, as the LOC (the locator portion)
> of the SRv6 SID serves a similar purpose (when present).

> When NT value is 4, the NAI is as per the 'IPv6 Adjacency'
> format defined in {{RFC8664}}, which specify a pair
> of IPv6 addresses. This is used to identify the IPv6 Adjacency
> and used with the SRv6 Adj-SID.

> When NT value is 6, the NAI is as per the 'link-local IPv6
> addresses' format defined in {{RFC8664}}, which
> specify a pair of (global IPv6 address, interface ID) tuples. It
> is used to identify the IPv6 Adjacency and used with the SRv6
> Adj-SID.

> SR-MPLS specific NT types are not valid in SRv6-ERO.


Flags: Used to carry additional information pertaining to the
SRv6-SID. This document defines the following flag bits. The other
bits MUST be set to zero by the sender and MUST be ignored by the
receiver.

* S: When this bit is set to 1, the SRv6-SID value in the
  subobject body is absent. In this case, the PCC is responsible
  for choosing the SRv6-SID value, e.g., by looking up in the
  SR-DB using the NAI which, in this case, MUST be present in the
  subobject. If the S bit is set to 1 then F bit MUST be set to
  zero.

* F: When this bit is set to 1, the NAI value in the subobject
  body is absent. The F bit MUST be set to 1 if NT=0, and
  otherwise MUST be set to zero. The S and F bits MUST NOT both be
  set to 1.

* T: When this bit is set to 1, the SID Structure value in the
  subobject body is present. The T bit MUST be set to 0 when S bit
  is set to 1. If the T bit is set when the S bit is set, the T
  bit MUST be ignored. Thus, the T bit indicates the presence of
  an optional 8-byte SID Structure when SRv6 SID is included. The
  SID Structure is defined in {{SID-Structure}}.

* V: The "SID verification" bit usage is as per Section 5.1 of {{RFC9256}}. If a PCC "Verification fails" for a SID, it MUST report this error by
   including the LSP-ERROR-CODE TLV with LSP error-value "SID Verification fails" in the LSP object in the PCRpt message to the PCE.

Reserved: MUST be set to zero while sending and ignored on receipt.

Endpoint Behavior: A 16-bit field representing the behavior
associated with the SRv6 SIDs. This information is optional, but it is recommended to signal it always if possible. It could be used for maintainability and diagnostic purpose. If behavior is not known, the opaque value '0xFFFF' is used {{RFC8986}}.


SRv6 SID: SRv6 Identifier is an 128-bit IPv6 address representing the SRv6 segment.

NAI: The NAI associated with the SRv6-SID. The NAI's format
depends on the value in the NT field, and is described in {{RFC8664}}.

At least one SRv6-SID or the NAI MUST be included in the SRv6-ERO subobject, and both MAY be included.

#### SID Structure {#SID-Structure}

The SID Structure is an optional part of the SR-ERO subobject,
as described in {{SRv6-ERO-Subobject}}.

{{RFC8986}} defines an SRv6 SID as consisting of LOC:FUNCT:ARG,
where a locator (LOC) is encoded in the L most significant bits of
the SID, followed by F bits of function (FUNCT) and A bits of
arguments (ARG).  A locator may be represented as B:N where B is
the SRv6 SID locator block (IPv6 prefix allocated for SRv6 SIDs by
the operator) and N is the identifier of the parent node
instantiating the SID called locator node.

It is
formatted as shown in the following figure.


~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    LB Length  |  LN Length    | Fun. Length   |  Arg. Length  |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                 Reserved                      |   Flags       |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+


~~~~
{: #SID-Structure-Format title='SID Structure Format'}

where:

LB Length: 1 octet. SRv6 SID Locator Block length in bits.

LN Length: 1 octet. SRv6 SID Locator Node length in bits.

Fun. Length: 1 octet. SRv6 SID Function length in bits.

Arg. Length: 1 octet. SRv6 SID Arguments length in bits.

The sum of all four sizes in the SID Structure must be lower or
equal to 128 bits. If the sum of all four sizes advertised in the
SID Structure is larger than 128 bits, the corresponding SRv6 SID
MUST be considered invalid and a PCErr message with Error-Type =
10 ("Reception of an invalid object") and Error-Value = 37 ("Invalid SRv6 SID Structure") is returned.

Reserved: MUST be set to zero while sending and ignored on
receipt.

Flags: Currently no flags are defined. Unassigned bits must be
set to zero while sending and ignored on receipt.

The SRv6 SID Structure provides the detailed encoding
information of an SRv6 SID, which is useful in the use cases that
require to know the SRv6 SID structure. When a PCEP speaker
receives the SRv6 SID and its structure information, the SRv6 SID
can be parsed based on the SRv6 SID Structure and/or possible
local policies. The SRv6 SID Structure could be used by the PCE for ease
of operations and monitoring.  For example, this information could be
used for validation of SRv6 SIDs being instantiated in the network
and checked for conformance to the SRv6 SID allocation scheme chosen
by the operator as described in Section 3.2 of {{RFC8986}}.  In the future, PCE can also be utilized to verify and automate the security of the SRv6 domain by provisioning filtering rules at the domain boundaries as described in Section 5 of {{RFC8754}}.  The details of these potential applications are outside the scope of this document.

#### Order of the Optional fields {#order}

The optional elements in the SRv6-ERO subobject i.e. SRv6 SID, NAI and the
SID Structure MUST be encoded in the order as depicted in {{SRv6-ERO-Subobject-Format}}.
The presence of each of them is indicated by the respective flags i.e.
S flag, F flag and T flag.

In order to ensure future compatibility, any optional elements added to the SRv6-ERO subobject in the future must specify their order and request the Internet Assigned Numbers Authority (IANA) to allocate a flag to indicate their presence from the subregistry created in {{SRv6-ERO-flag}}.



## RRO {#RRO}

In order to support SRv6, a new "SRv6-RRO" subobject is defined for inclusion in the RRO.


### SRv6-RRO Subobject {#SRv6-RRO-Subobject}

A PCC reports an SRv6 path to a PCE by sending a PCRpt message,
per {{RFC8231}}. The RRO on this message represents the
SID list that was applied by the PCC, that is, the actual path
taken. The procedures of {{RFC8664}} with respect to
the RRO apply equally to this specification without change.

An RRO contains one or more subobjects called "SRv6-RRO
subobjects" whose format is shown below.


~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   Type=40     |     Length    |  NT   |     Flags     |V|T|F|S|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |              Reserved         |      Endpoint Behavior        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   |                      SRv6 SID(optional)                       |
   |                           (128-bit)                           |
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   //                    NAI (variable)                           //
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   |                     SID Structure (optional)                  |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

~~~~
{: #SRv6-RRO-Subobject-Format title='SRv6-RRO Subobject Format'}

The format of the SRv6-RRO subobject is the same as that of the
SRv6-ERO subobject, but without the L flag.

The V flag has no meaning in the SRv6-RRO and is ignored on
receipt at the PCE.

Ordering of SRv6-RRO subobjects by PCC in PCRpt message remains
as per {{RFC8664}}.

The ordering of optional elements in the SRv6-RRO subobject is the same as described in {{order}}.


# Procedures {#Procedures}

## Exchanging the SRv6 Capability {#Exchanging-SRv6-Capability}

A PCC indicates that it is capable of supporting the head-end
functions for SRv6 by including the SRv6-PCE-CAPABILITY sub-TLV in the
Open message that it sends to a PCE. A PCE indicates that it is
capable of computing SRv6 paths by including the SRv6-PCE-CAPABILITY
sub-TLV in the Open message that it sends to a PCC.

If a PCEP speaker receives a PATH-SETUP-TYPE-CAPABILITY TLV with a
PST list containing PST=3, but the SRv6-PCE-CAPABILITY sub-TLV is absent, then the PCEP speaker MUST send a PCErr message with Error-Type = 10 (Reception of an invalid object) and Error-Value = 34 (Missing PCE-SRv6-CAPABILITY sub-TLV) and MUST then close the PCEP session. If a PCEP speaker receives a PATH-SETUP-TYPE-CAPABILITY TLV with an SRv6-PCE-CAPABILITY sub-TLV, but the PST list does not contain PST=3, then the PCEP speaker MUST ignore the SRv6-PCE-CAPABILITY sub-TLV.

In case the MSD-Type in SRv6-PCE-CAPABILITY sub-TLV received by the PCE
does not correspond to one of the SRv6 MSD types, the PCE MUST respond
with a PCErr message (Error-Type = 1 "PCEP session establishment
failure" and Error-Value = 1 "reception of an invalid Open message or a
non Open message.").

Note that the MSD-Type, MSD-Value exchanged via the
SRv6-PCE-CAPABILITY sub-TLV indicates the SRv6 SID imposition limit
for the sender PCC node only. However, if a PCE learns these via alternate mechanisms, e.g routing protocols {{RFC9352}}, then it ignores the values in the SRv6-PCE-CAPABILITY sub-TLV. Furthermore, whenever a PCE learns any other SRv6 MSD types that may be defined in the future via alternate mechanisms, it MUST use those values regardless of the values exchanged in the SRv6-PCE-CAPABILITY sub-TLV.

During path computation, PCE must consider the MSD information of all the nodes along the path instead of only the MSD information of the ingress PCC since a packet may be dropped on any node in a forwarding path because of MSD exceeding. The MSD capabilities of all SR nodes along the path can be learned as part of the topology information via IGP/BGP-LS or via PCEP if the PCE also happens to have PCEP sessions to those nodes.

A PCE MUST NOT send SRv6 paths exceeding the SRv6 MSD capabilities of the PCC. If a PCC needs to modify the SRv6 MSD value signaled via the Open message, it MUST close the PCEP session and re-establish it with the new value. If the PCC receives an SRv6 path that exceed its SRv6 MSD capabilties, the PCC MUST send a PCErr message with Error-Type = 10 (Reception of an invalid object) and Error-Value = 39 (Unsupported number of SRv6-ERO subobjects).


The N flag and (MSD-Type,MSD-Value) pair inside the SRv6-PCE-CAPABILITY sub-TLV are meaningful only in the Open message sent to a PCE. As such,the flags MUST be set to zero and a (MSD-Type,MSD-Value) pair MUST NOT be present in the SRv6-PCE-CAPABILITY sub-TLV in an Open message sent to a PCC.  Similarly, a PCC MUST ignore flags and any (MSD-Type,MSD-Value) pair in a received Open message. If a PCE receives multiple SRv6-PCE-CAPABILITY sub-TLVs in an Open message, it processes only the first sub-TLV received.


## ERO Processing {#ERO-Processing}

The processing of ERO remains unchanged in accordance with both {{RFC5440}} and {{RFC8664}}.

### SRv6 ERO Validation

If a PCC does not support the SRv6 PCE Capability and thus cannot
recognize the SRv6-ERO or SRv6-RRO subobjects. It should respond according to the rules for a malformed object as described in {{RFC5440}}.

On receiving an SRv6-ERO, a PCC MUST validate that the Length
field, the S bit, the F bit, the T bit, and the NT field are
consistent, as follows.

* If NT=0, the F bit MUST be 1, the S bit MUST be zero and the
  Length MUST be 24.

* If NT=2, the F bit MUST be zero. If the S bit is 1, the
  Length MUST be 24, otherwise the Length MUST be 40.

* If NT=4, the F bit MUST be zero. If the S bit is 1, the
  Length MUST be 40, otherwise the Length MUST be 56.

* If NT=6, the F bit MUST be zero. If the S bit is 1, the
  Length MUST be 48, otherwise the Length MUST be 64.

* NT types (1,3, and 5) are not valid for SRv6.

* If T bit is 1, then S bit MUST be zero.


If a PCC finds that the NT field, Length field, S bit, F bit, and
T bit are not consistent, it MUST consider the entire ERO invalid
and MUST send a PCErr message with Error-Type = 10 ("Reception of an
invalid object") and Error-Value = 11 ("Malformed object").

If a PCC does not recognize or support the value in the NT field, it
MUST consider the entire ERO invalid and send a PCErr message
with Error-Type = 10 ("Reception of an invalid object") and Error-
value = 40 ("Unsupported NAI Type in the SRv6-ERO/SRv6-RRO subobject").

If a PCC receives an SRv6-ERO subobject in which the S and F bits are
both set to 1 (that is, both the SID and NAI are absent), it MUST
consider the entire ERO invalid and send a PCErr message with Error-
Type = 10 ("Reception of an invalid object") and Error-value = 41
("Both SID and NAI are absent in the SRv6-ERO subobject").

If a PCC receives an SRv6-ERO subobject in which the S bit is set to 1
and the F bit is set to zero (that is, the SID is absent and the NAI
is present), but the PCC does not support NAI resolution, it MUST
consider the entire ERO invalid and send a PCErr message with Error-
Type = 4 ("Not supported object") and Error-value = 4 ("Unsupported
parameter").

If a PCC detects that the subobjects of an ERO are a mixture of SRv6-
ERO subobjects and subobjects of other types, then it MUST send a
PCErr message with Error-Type = 10 ("Reception of an invalid object")
and Error-value = 42 ("ERO mixes SRv6-ERO subobjects with other
subobject types").

In case a PCEP speaker receives an SRv6-ERO subobject, when the PST is not set to 3 or SRv6-PCE-CAPABILITY sub-TLV was not exchanged, it MUST send a PCErr message with Error-Type = 19 ("Invalid Operation") and Error-Value = 19 ("Attempted SRv6 when the capability was not advertised").

If a PCC receives an SRv6 path that exceeds the SRv6 MSD capabilities, it MUST send a PCErr message with Error-Type = 10 ("Reception of an invalid object") and Error-Value = 43 ("Unsupported number of SRv6-ERO subobjects") as per {{RFC8664}}.


### Interpreting the SRv6-ERO

The SRv6-ERO contains a sequence of subobjects. According to {{RFC9256}}, each
SRv6-ERO subobject in the sequence identifies a segment that the
traffic will be directed to, in the order given. That is, the first
subobject identifies the first segment the traffic will be directed
to, the second SRv6-ERO subobject represents the second segment, and
so on.


## RRO Processing {#RRO-Processing}

The syntax checking rules that apply to the SRv6-RRO subobject are
identical to those of the SRv6-ERO subobject, except as noted
below.

If a PCEP speaker receives an SRv6-RRO subobject in which both SRv6
SID and NAI are absent, it MUST consider the entire RRO invalid and
send a PCErr message with Error-Type = 10 ("Reception of an invalid
object") and Error-Value = 35 ("Both SID and NAI
are absent in
SRv6-RRO subobject").

If a PCE detects that the subobjects of an RRO are a mixture of
SRv6-RRO subobjects and subobjects of other types, then it MUST send a
PCErr message with Error-Type = 10 ("Reception of an invalid object")
and Error-Value = 36 ("RRO mixes SRv6-RRO subobjects
with other
subobject types").

The mechanism by which the PCC learns the path is outside the scope of this document.


# Security Considerations {#Security-Considerations}

The security considerations described in {{RFC5440}}, section 2.5 of {{RFC6952}}, {{RFC8231}}, {{RFC8281}}, {{RFC8253}} and {{RFC8664}} are applicable to this specification. No additional security measure is required.

Note that this specification enables a network controller to
instantiate an SRv6 path in the network.  This creates an additional
vulnerability if the security mechanisms of {{RFC5440}}, {{RFC8231}}, and {{RFC8281}}
are not used.  If there is no integrity protection on the
session, then an attacker could create an SRv6 path that may not subjected
to the further verification checks. Further, the MSD field in the Open message
could disclose node forwarding capabilities if suitable security mechanisms
are not in place.


# Manageability Considerations {#Manage}

All manageability requirements and considerations listed in {{RFC5440}}, {{RFC8231}},
{{RFC8281}}, and {{RFC8664}} apply to PCEP protocol extensions defined in this
document. In addition, requirements and considerations listed in this
section apply.



## Control of Function and Policy

A PCEP implementation SHOULD allow the operator to configure the SRv6 capability.
Further a policy to accept NAI only for the SRv6 SHOULD be allowed to be set.


## Information and Data Models

The PCEP YANG module is out of the scope of this document and defined in other drafts. An augmented YANG module for SRv6 is also specified in another draft that allows for SRv6 capability and MSD configurations as well as to monitor the SRv6 paths set in the network.

## Liveness Detection and Monitoring

Mechanisms defined in this document do not imply any new liveness
detection and monitoring requirements in addition to those already
listed in {{RFC5440}}.


## Verify Correct Operations

Verification of the mechanisms defined in this document can be built on those already listed in [RFC5440], [RFC8231], and [RFC8664].


## Requirements On Other Protocols

Mechanisms defined in this document do not imply any new
requirements on other protocols.


## Impact On Network Operations

Mechanisms defined in {{RFC5440}}, {{RFC8231}}, and {{RFC8664}} also apply to PCEP
extensions defined in this document.



# Implementation Status

[Note to the RFC Editor - remove this section before publication, as
well as remove the reference to {{RFC7942}}.

This section records the status of known implementations of the
protocol defined by this specification at the time of posting of this
Internet-Draft, and is based on a proposal described in {{RFC7942}}.
The description of implementations in this section
is intended to assist the IETF in its decision processes in progressing
drafts to RFCs. Please note that the listing of any individual
implementation here does not imply endorsement by the IETF. Furthermore,
no effort has been spent to verify the information presented here that
was supplied by IETF contributors. This is not intended as, and must not
be construed to be, a catalog of available implementations or their
features. Readers are advised to note that other implementations may
exist.

According to {{RFC7942}}, "this will allow reviewers and
working groups to assign due consideration to documents that have the
benefit of running code, which may serve as evidence of valuable
experimentation and feedback that have made the implemented protocols
more mature. It is up to the individual working groups to use this
information as they see fit".



## Cisco's Commercial Delivery

* Organization: Cisco Systems, Inc.

* Implementation: IOS-XR PCE and PCC.

* Description: Implementation with experimental codepoints.

* Maturity Level: Production

* Coverage: Partial

* Contact: ssidor@cisco.com



# IANA Considerations {#IANA-Considerations}

## PCEP ERO and RRO subobjects {#PCEP-ERO-and-RRO-subobjects}

This document defines a new subobject type for the PCEP explicit
route object (ERO), and a new subobject type for the PCEP reported route
object (RRO). The code points for subobject types of these objects is
maintained in the RSVP parameters registry, under the EXPLICIT_ROUTE
and REPORTED_ROUTE objects. IANA is requested to confirm the following allocations in the RSVP Parameters registry for each of the new subobject types
defined in this document.


~~~~
  Object                Subobject                  Subobject Type
  --------------------- -------------------------- ------------------
  EXPLICIT_ROUTE        SRv6-ERO (PCEP-specific)     40
  REPORTED_ROUTE        SRv6-RRO (PCEP-specific)     40

~~~~


## New SRv6-ERO Flag Registry {#SRv6-ERO-flag}

IANA is requested to create a new sub-registry, named "SRv6-ERO
Flag Field", within the "Path Computation Element Protocol (PCEP)
Numbers" registry to manage the 12-bit Flag field of the SRv6-ERO subobject.
New values are to be assigned by Standards Action {{RFC8126}}.
Each bit should be tracked with the following qualities.

* Bit (counting from bit 0 as the most significant
  bit)

* Description

* Reference


The following values are defined in this document.


~~~~
                Bit     Description            Reference
                -----   ------------------     --------------
                 0-7      Unassigned
                   8      SID Verification (V)  This document
                   9      SID Structure is      This document
                          present (T)
                  10      NAI is absent (F)     This document
                  11      SID is absent (S)     This document
~~~~

##  LSP-ERROR-CODE TLV

This document defines a new value in the sub-registry "LSP-ERROR-CODE TLV Error Code Field" in the "Path Computation Element Protocol(PCEP) Numbers" registry.

~~~~
    Value      Meaning                     Reference
    ---       -----------------------     -----------
     9        SID Verification fails      This document
~~~~

## PATH-SETUP-TYPE-CAPABILITY Sub-TLV Type Indicators {#sub-TLV-Type-Indicators}

IANA maintains a sub-registry, named "PATH-SETUP-TYPE-CAPABILITY
Sub-TLV Type Indicators", within the "Path Computation Element
Protocol (PCEP) Numbers" registry to manage the type indicator space
for sub-TLVs of the PATH-SETUP-TYPE-CAPABILITY TLV. IANA is requested to
confirm the following allocations in the sub-registry.


~~~~
   Value     Meaning                     Reference
   -----     -------                     ---------
   27        SRv6-PCE-CAPABILITY         This Document
~~~~


## SRv6 PCE Capability Flags {#SRv6-PCE-Capability-Flags}

IANA is requested to create a new sub-registry, named "SRv6
Capability Flag Field", within the "Path Computation Element Protocol (PCEP) Numbers" registry to manage the 16-bit Flag field of the SRv6-PCE-CAPABILITY sub-TLV. New values are to be assigned by Standards Action {{RFC8126}}. Each bit should be tracked with the following qualities.

* Bit (counting from bit 0 as the most significant
  bit)

* Description

* Reference


The following values are defined in this document.


~~~~
                 Bit     Description           Reference
                -----   ------------------     --------------
                 0-13    Unassigned
                  14     Node or Adjacency     This document
                         Identifier (NAI) is
                         supported (N)
                  15     Unassigned
~~~~


## New Path Setup Type {#New-Path-Setup-Type}

{{RFC8408}} created a sub-registry within the "Path
Computation Element Protocol (PCEP) Numbers" registry called "PCEP
Path Setup Types". IANA is requested to confirm the following allocations
in the sub-registry.


~~~~
Value             Description                  Reference
-----             -----------                  ---------
3                 Traffic engineering path is  This Document
                  setup using SRv6.

~~~~


## ERROR Objects {#ERROR-Objects}

IANA is requested to confirm the following allocations in the PCEP-ERROR
Object
Error Types and Values registry for the following new error-values.


~~~~
   Error-Type   Meaning
   ----------   -------
   10           Reception of an invalid object
                Error-value = 34 (Missing
                PCE-SRv6-CAPABILITY sub-TLV)
                Error-value = 35 (Both SID and NAI are absent
                in SRv6-RRO subobject)
                Error-value = 36 (RRO mixes SRv6-RRO subobjects
                with other subobject types)
                Error-value = 37 (Invalid SRv6 SID Structure)
   19           Invalid Operation
                Error-value = 19 (Attempted SRv6 when the
                capability was not advertised)

~~~~

IANA is requested to make a new allocations in the PCEP-ERROR Object
Error Types and Values registry for the following new error-values.


~~~~
   Error-Type   Meaning
   ----------   -------
   10           Reception of an invalid object
                Error-value = 39 (Unsupported number of
                SRv6-ERO subobjects)
                Error-value = 40 (Unsupported NAI Type
                in the SRv6-ERO/SRv6-RRO subobject)
                Error-value = 41 (Both SID and NAI are
                absent in the SRv6-ERO subobject)
                Error-value = 42 (ERO mixes SRv6-ERO
                subobjects with other subobject types)
                Error-value = 43 (Unsupported number
                of SRv6-ERO subobjects)

~~~~


--- back


# Acknowledgements
{:unnumbered}

The authors would like to thank Jeff Tantsura, Adrian Farrel, Aijun
Wang, Khasanov Boris, Ketan Talaulikar, Martin Vigoureux, Hariharan Ananthakrishnan, Julien Meuric and Robert Varga for valuable suggestions.
