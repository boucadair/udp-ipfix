---
title: "Export of UDP Options Information in IP Flow Information Export (IPFIX)"
abbrev: "IPFIX IE for UDP Options"
category: std

docname: draft-ietf-opsawg-tsvwg-udp-ipfix-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "OPSAWG"
keyword:
 - surplus area
 - UDP options

author:
 -
    fullname: Mohamed Boucadair
    organization: Orange
    city: Rennes
    code: 35000
    country: France
    email: mohamed.boucadair@orange.com

 -
    fullname: Tirumaleswar Reddy.K
    organization: Nokia
    country: India
    email: kondtir@gmail.com

normative:

informative:
  IANA-IPFIX:
     title: IP Flow Information Export (IPFIX) Entities
     target: https://www.iana.org/assignments/ipfix/ipfix.xhtml

--- abstract

This document specifies new IP Flow Information Export (IPFIX) Information Elements for UDP options.

--- middle

# Introduction

IP Flow Information Export (IPFIX) {{!RFC7011}} is a protocol that is widely deployed in operators networks for traffic management purposes ({{Section 2 of ?RFC6632}}). The protocol specifies the encoding of a set of basic data types and how the various Information Elements (IEs) are transmitted. In order to support the export of new flow-related measurement data, new IEs can be defined and registered in a dedicated IANA registry {{IANA-IPFIX}} for interoperability.

This document specifies new IPFIX Information Elements for UDP options ({{sec-IE}}). A brief overview of UDP options is provided in {{uo}}.

The IE specified in {{udpOptions}} uses the new abstract data type defined in {{?I-D.ietf-opsawg-ipfix-tcpo-v6eh}}.

Examples to illustrate the use of the new IPFIX Information Elements are provided in {{sec-ex}}.

# Conventions and Definitions

This document uses the IPFIX-specific terminology (e.g., Flow) defined in {{Section 2 of !RFC7011}}.
As in {{!RFC7011}}, these IPFIX-specific terms have the first letter of a word capitalized.

The document adheres to the naming conventions for Information Elements per {{Section 2.3 of !RFC7012}}.

Also, this document uses the terms defined in {{Section 3 of !I-D.ietf-tsvwg-udp-options}}, especially "datagram" and "surplus area".

# UDP Options at a Glance {#uo}

UDP {{!RFC0768}} does not support an extension mechanism similar to the options supported by other transport protocols, such as TCP {{?RFC9293}}, SCTP {{?RFC9260}}, or DCCP {{?RFC4340}}. Such a mechanism can be useful for various applications, e.g., discover a path MTU or share timestamps. To fill that void, {{!I-D.ietf-tsvwg-udp-options}} extends UDP with a mechanism to insert extensions in datagrams. To do so, and unlike the conventional approach that relies upon transport headers, {{!I-D.ietf-tsvwg-udp-options}} uses trailers. Concretely, UDP options are placed in the surplus area (that is, the area of an IP payload that follows a UDP packet). See {{spa}}. An example of the use of UDP options is described in {{?I-D.ietf-tsvwg-udp-options-dplpmtud}}.

~~~~
                       IP transport payload
          <------------------------------------------------->
+--------+---------+----------------------+------------------+
| IP Hdr | UDP Hdr |     UDP user data    |   surplus area   |
+--------+---------+----------------------+------------------+
          <------------------------------>
                     UDP Length
~~~~
{: #spa title="Surplus Area" artwork-align="center"}

{{udpOptions}} introduces a new IE to export the observed UDP options.

Options indicated by Kind values in the range 0-191 are called SAFE options. Such options can be silently ignored by legacy receivers because they do not alter the UDP user data ({{Section 11 of !I-D.ietf-tsvwg-udp-options}}).

Options indicated by Kind values in the range 192-255 are called UNSAFE options. Such options are not safe for legacy receivers to ignore because they alter the UDP user data ({{Section 12 of !I-D.ietf-tsvwg-udp-options}}).

UDP options occur per-packet within a Flow and can be inserted at any time in the Flow.

{{!I-D.ietf-tsvwg-udp-options}} reserves two options for experiments: the Experimental option (EXP, Kind=127) for SAFE options and the UNSAFE Experimental option (UEXP, Kind=254). For both options, Experimental ID (ExIDs) are used to differentiate concurrent use of these options. Known ExIDs are expected to be registered within IANA. {{udpExID}} specifies a new IPFIX IE to export observed ExIDs in the EXP options. Also, {{udpUExID}} specifies a new IPFIX IE to export observed ExIDs in the UEXP options. Only 16-bit ExIDs are supported in {{!I-D.ietf-tsvwg-udp-options}}.

This document does not intend to elaborate operational guidance/implications of UDP options. The document focuses exclusively on exporting observed UDP options in datagrams.

# New UDP IPFIX Information Elements {#sec-IE}

> Note: "URL_IANA_UDP_OPTIONS" is the URL of the "UDP Option Kind Numbers" registry group while "URL_IANA_UDP_ExIDs" is the URL of the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry that will be created by IANA as per {{Section 25 of !I-D.ietf-tsvwg-udp-options}}.

## udpOptions {#udpOptions}

Name:
: udpOptions

ElementID:
: TBD1

Description:
: Observed UDP options in a Flow. The information is encoded in a set of bit fields.
: Options are mapped to bits according to their option numbers. UDP
  option Kind 0 corresponds to the least-significant bit in the
  udpOptions IE while Kind 255 corresponds to the most-significant bit of the IE. A bit is set to 1 if the corresponding UDP option is observed in the Flow. The bit is set to 0 if the option is not observed in the Flow.

Abstract Data Type:
:  unsigned256

Data Type Semantics:
:  flags

Additional Information:
: See the assigned UDP options in the "UDP Option Kind Numbers" registry at [URL_IANA_UDP_OPTIONS].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about UDP options.

Reference:
: This-Document


## udpSafeExperimentalOptionExID {#udpExID}

Name:
:  udpSafeExperimentalOptionExID

ElementID:
:  TBD2

Description:
: Observed Experiments ID (ExIDs) in the Experimental option (EXP, Kind=127).
: The information is encoded in a set of 16-bit fields. Each 16-bit field carries the observed ExID in an EXP option.

Abstract Data Type:
:  octetArray

Data Type Semantics:
:  identifier

Additional Information:
: See the assignments in the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry at [URL_IANA_UDP_ExIDs].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about ExIDs.

Reference:
: This-Document

## udpUnsafeExperimentalOptionExID {#udpUExID}

Name:
:  udpUnsafeExperimentalOptionExID

ElementID:
:  TBD3

Description:
: Observed Experiments ID (ExIDs) in the UNSAFE Experimental option (UEXP, Kind=254).
: The information is encoded in a set of 16-bit fields. Each 16-bit field carries the observed ExID in an UEXP option.

Abstract Data Type:
:  octetArray

Data Type Semantics:
:  identifier

Additional Information:
: See the assignments in the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry at [URL_IANA_UDP_ExIDs].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about ExIDs.

Reference:
: This-Document

# Operational Considerations {#sec-ops}

To cover the 0-255 Kind range, up to 256 flags can be set in the value field of udpOptions IE. The reduced-size encoding specified in {{Section 6.2 of !RFC7011}} is followed whenever fewer octets are needed to report observed UDP options. For example, if only option Kinds <= 32 are observed, then the value can be encoded as unsigned32, or if only option Kinds <= 63 are observed, then the value can be encoded as unsigned64.

The presence of udpSafeExperimentalOptionExID (or udpUnsafeExperimentalOptionExID) is an indication that the SAFE (or UNSAFE) Experimental option is observed in a Flow. The presence of udpSafeExperimentalOptionExID (or udpUnsafeExperimentalOptionExID) takes precedence over setting the correspoding bits in the udpOptions IE for the same Flow. In order to make use of the reduced-size encoding in the presence of udpSafeExperimentalOptionExID (or udpUnsafeExperimentalOptionExID) IE, the Exporter SHOULD NOT set to 1 the EXP (or UEXP) flags of the udpOptions IE that is reported for the same Flow.

Transport (including MTU) considerations are discussed in {{Section 10 of !RFC7011}}.

# Examples {#sec-ex}

Given UDP Kind allocation in {{Section 10 of !I-D.ietf-tsvwg-udp-options}} and the option mapping defined in {{udpOptions}} of this document, fewer octets are likely to be used for Flows with mandatory UDP options.

{{ex-udp}} shows an example of reported values in a udpOptions IE for a Flow in which End of Options List (EOL, Kind=0) and Alternate payload checksum (APC, Kind=2) options are observed. One octet is sufficient to report these observed options. Concretely, the reported udpOptions IE will be set to 0x05.

~~~~
MSB                                                     LSB
                     1                          25
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 ... 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+...+-+-+-+-+-+-+-+-+
|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|   |0|0|0|0|0|1|0|1|
+-+-+-+-+-+-+-+-+-+-+-+-+-+-++-++-+-+-+-+...+-+-+-+-+-+-+-+-+
~~~~
{: #ex-udp title="An Example of udpOptions with EOL and APC Options" artwork-align="center"}

Let us now consider a UDP Flow in which both SAFE and UNSAFE Experimental options are observed. If udpOptions IE is exported for this Flow, then that IE will have bits in positions 127 (EXP) and 254 (UEXP) set to 1 ({{ex-udp-shared}}). This example does not make any assumption about the presence of other UDP options.

~~~~
MSB                                                     LSB
                    12                          25
 0 1 2 3 ... 7 8 9 0 1 2 3 4 5 6 7 8 9 ... 8 9 0 1 2 3 4 5
+-+-+-+-+...+-+-+-+-+-+-+-+-+-+-+-+-+-+-+...+-+-+-+-+-+-+-+
|X|1|X|X|   |X|X|X|X|X|X|X|X|X|X|X|1|X|X|   |X|X|X|X|X|X|X|
+-+-+-+-+...+-+-+-+-+-+-+-+-++-++-+-+-+-+...+-+-+-+-+-+-+-+
~~~~
{: #ex-udp-shared title="An Example of udpOptions with EXP and UEXPOptions" artwork-align="center"}

Now assume that EOL, APC, EXP, and UEXP options are observed for a Flow. Let us also consider that the observed SAFE Experimental options have ExIDs set to 0x9858 and 0xE2D4, and UNSAFE Experimental options have ExIDs set to 0xC3D9 and 0x9858. As shown in {{ex-sho}}, the following IEs are used to report observed ExIDs:

1. udpSafeExperimentalOptionExID IE set to 0x9858E2D4 to report observed ExIDs of SAFE Experimental options.
2. udpUnsafeExperimentalOptionExID IE set to 0xC3D99658 to report the ExIDs of the observed UNSAFE Experimental options.

~~~~
udpSafeExperimentalOptionExID IE:

MSB                                                          LSB
                     1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|              0x9858           |             0xE2D4            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

udpUnsafeExperimentalOptionExID IE:

                     1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|              0xC3D9           |             0x9658            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~
{: #ex-sho title="Example of UDP Experimental option IEs" artwork-align="center"}

Following the guidance in {{sec-ops}}, the udpOptions IE will be set to 0x05 (i.e., set the EXP/UEXP bits to 0 in the udpSafeExperimentalOptionExID and udpUnsafeExperimentalOptionExID IEs).

# Security Considerations

This document does not introduce new security considerations other than those already discussed in {{Section 11 of !RFC7011}} and {{Section 8 of !RFC7012}}.

The reader may refer to {{Section 24 of !I-D.ietf-tsvwg-udp-options}} for the security considerations related to UDP options.

# IANA Considerations {#IANA}

This document requests IANA to add the following new IEs to the IANA registry entitled "IP Flow Information Export (IPFIX) Entities" {{IANA-IPFIX}}:

|Value|	Name|	Reference|
|TBD1| udpOptions|{{udpOptions}} of This-Document|
|TBD2| udpSafeExperimentalOptionExID|{{udpExID}} of This-Document|
|TBD3| udpUnsafeExperimentalOptionExID|{{udpUExID}} of This-Document|
{: title="New IPFIX Information Elements"}

> {{udpOptions}} uses the abstract data type ("unsigned256") defined in {{?I-D.ietf-opsawg-ipfix-tcpo-v6eh}}.

--- back

# Acknowledgments
{:numbered="false"}

Thanks to Benoît Claise for the discussion on the ordering of IPFIX IEs. Thanks to Paul Aitken for the review and comments.

Thanks to Tommy Pauly for the tsvart review, Joe Touch for the intdir review, Robert Sparks for the genart review, and Watson Ladd for the secdir review.

Thanks to Thomas Graf for the Shepherd review.

Thanks to Mahesh Jethanandani for the AD review.
