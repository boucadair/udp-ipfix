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

  URL_IANA_UDP_OPTIONS:
     title: UDP Option Kind Numbers
     target: https://www.iana.org/assignments/url1

  URL_IANA_UDP_ExIDs:
    title: UDP Experimental Option Experiment Identifiers (UDP ExIDs)
    target: https://www.iana.org/assignments/url2

--- abstract

This document specifies new IP Flow Information Export (IPFIX) Information Elements for UDP options.

--- middle

# Introduction

IP Flow Information Export (IPFIX) {{!RFC7011}} is a protocol that is widely deployed in operators networks for traffic management purposes ({{Section 2 of ?RFC6632}}). The protocol specifies the encoding of a set of basic data types and how the various Information Elements (IEs) are transmitted. In order to support the export of new flow-related measurement data, new IEs can be defined and registered in a dedicated IANA registry {{IANA-IPFIX}} for interoperability.

This document specifies new IPFIX Information Elements for UDP options ({{sec-IE}}). A brief overview of UDP options is provided in {{uo}}.

The IE specified in {{udpOptions}} uses the new abstract data type defined in {{!I-D.ietf-opsawg-ipfix-tcpo-v6eh}}.

Transport (including MTU) considerations are discussed in {{Section 10 of !RFC7011}}.

Examples to illustrate the use of the new IPFIX Information Elements are provided in {{sec-ex}}.

# Conventions and Definitions

This document uses the IPFIX-specific terminology (e.g., Flow) defined in {{Section 2 of !RFC7011}}.
As in the base IPFIX specification {{!RFC7011}}, these IPFIX-specific terms have the first letter of a word capitalized.

The document adheres to the naming conventions for Information Elements per {{Section 2.3 of !RFC7012}}.

Also, this document uses the terms defined in {{Section 3 of !I-D.ietf-tsvwg-udp-options}}, especially "datagram" and "surplus area".

# UDP Options at a Glance {#uo}

UDP {{!RFC0768}} does not support an extension mechanism similar to the options supported by other transport protocols, such as TCP {{?RFC9293}}, SCTP {{?RFC9260}}, or DCCP {{?RFC4340}}. Such a mechanism can be useful for various applications, e.g., to discover a path MTU or share timestamps. To fill that void, {{!I-D.ietf-tsvwg-udp-options}} extends UDP with a mechanism to insert extensions in datagrams. To do so, and unlike the conventional approach that relies upon transport headers, {{!I-D.ietf-tsvwg-udp-options}} uses trailers. Concretely, UDP options are placed in the surplus area (that is, the area of an IP payload that follows a UDP packet). See {{spa}}. An example of the use of UDP options for Datagram Packetization Layer Path Maximum Transmission Unit Discovery (DPLPMTUD) is described in {{?I-D.ietf-tsvwg-udp-options-dplpmtud}}.

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

Sections {{<udpOptions}} and {{<udpUnsafeOptions}} introduce new IEs to export the observed UDP options.

Options indicated by Kind values in the range 0-191 are called SAFE options. Such options can be silently ignored by legacy receivers because they do not alter the UDP user data ({{Section 11 of !I-D.ietf-tsvwg-udp-options}}). Safe options are exported using the IE defined in {{udpOptions}}.

Options indicated by Kind values in the range 192-255 are called UNSAFE options. Such options are not safe for legacy receivers to ignore because they alter the UDP user data ({{Section 12 of !I-D.ietf-tsvwg-udp-options}}). Unsafe options are exported using the IE defined in {{udpUnsafeOptions}}.

UDP options occur per-packet within a Flow and can be inserted at any time in the Flow.

{{!I-D.ietf-tsvwg-udp-options}} reserves two options for experiments: the Experimental option (EXP, Kind=127) for SAFE options and the UNSAFE Experimental option (UEXP, Kind=254). For both options, Experiment Identifiers (ExIDs) are used to differentiate concurrent use of these options. Known ExIDs are expected to be registered within IANA. {{udpExID}} specifies a new IPFIX IE to export observed ExIDs in the EXP options. Also, {{udpUExID}} specifies a new IPFIX IE to export observed ExIDs in the UEXP options. Only 16-bit ExIDs are supported in {{!I-D.ietf-tsvwg-udp-options}}.

This document does not intend to elaborate operational guidance/implications of UDP options. The document focuses exclusively on exporting observed UDP options in datagrams.

# New UDP IPFIX Information Elements {#sec-IE}

> RFC Editor Note: Please update "URL_IANA_UDP_OPTIONS" reference with the URL of the "UDP Option Kind Numbers" registry group and "URL_IANA_UDP_ExIDs" with the URL of the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry that will be created by IANA as per {{Section 25 of !I-D.ietf-tsvwg-udp-options}}.

Given the Kind structure of safe and unsafe UDP options, using one single IE that would multiplex both types of option will limit the benefits of reduced-size encoding in the presence of unsafe options. In order to use less bits to report observed UDP options, distinct IEs are thus defined to report safe ({{udpOptions}}) and unsafe ({{udpUnsafeOptions}}) UDP options.

## udpSafeOptions {#udpOptions}

Name:
: udpSafeOptions

ElementID:
: TBD1

Description:
: Observed safe UDP options in a Flow. The information is encoded in a set of bit fields.
: Options are mapped to bits according to their option numbers. The first 64 most-significant bits MUST be set to 0. UDP
  option Kind 0 corresponds to the least-significant bit in the
  udpSafeOptions IE while Kind 191 corresponds to the 65 most-significant bit of the IE. The bit is set to 1 if the corresponding safe UDP option is observed in the Flow. The bit is set to 0 if the option is not observed in the Flow.
: The reduced-size encoding per {{Section 6.2 of !RFC7011}} is followed whenever fewer octets are needed to report observed safe UDP options. For example, if only option Kinds <= 31 are observed, then the value of the udpSafeOptions IE can be encoded as unsigned32, or if only option Kinds <= 63 are observed, then the value of the udpSafeOptions IE can be encoded as unsigned64.
: The presence of udpSafeExIDList is an indication that the SAFE Experimental option is observed in a Flow. The presence of udpSafeExIDList takes precedence over setting the corresponding bit in the udpSafeOptions IE for the same Flow. In order to optimize the use of the reduced-size encoding in the presence of udpSafeExIDList IE, the Exporter MUST NOT set to 1 the EXP flag of the udpSafeOptions IE that is reported for the same Flow.

Abstract Data Type:
:  unsigned256

Data Type Semantics:
:  flags

Additional Information:
: See the "UDP Option Kind Numbers" registry at [URL_IANA_UDP_OPTIONS].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about UDP options.

Reference:
: This-Document

## udpUnsafeOptions {#udpUnsafeOptions}

Name:
: udpUnsafeOptions

ElementID:
: TBD2

Description:
: Observed unsafe UDP options in a Flow. The information is encoded in a set of bit fields.
: Options are mapped to bits according to their option numbers. UDP
  option Kind 192 corresponds to the least-significant bit in the
  udpUnsafeOptions IE while Kind 255 corresponds to the most-significant bit of the IE. The bit is set to 1 if the corresponding unsafe UDP option is observed in the Flow. The bit is set to 0 if the option is not observed in the Flow.
: The reduced-size encoding per {{Section 6.2 of !RFC7011}} is followed whenever fewer octets are needed to report observed unsafe UDP options.
: The presence of udpUnsafeExIDList is an indication that the UNSAFE Experimental option is observed in a Flow. The presence of udpUnsafeExIDList takes precedence over setting the corresponding bit in the udpUnsafeOptions IE for the same Flow. In order to optimize the use of the reduced-size encoding in the presence of udpUnsafeExIDList IE, the Exporter MUST NOT set to 1 the UEXP flag of the udpUnsafeOptions IE that is reported for the same Flow.

Abstract Data Type:
:  unsigned64

Data Type Semantics:
:  flags

Additional Information:
: See the "UDP Option Kind Numbers" registry at [URL_IANA_UDP_OPTIONS].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about UDP options.

Reference:
: This-Document

## udpExID {#udpBasicExID}

Name:
: udpExID

ElementID:
:  TBD3

Description:
: Observed ExID in an Experimental option (EXP, Kind=127) or an UNSAFE Experimental option (UEXP, Kind=254).

Abstract Data Type:
:  unsigned16

Data Type Semantics:
:  identifier

Additional Information:
: See the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry at [URL_IANA_UDP_ExIDs].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about ExIDs.

Reference:
: This-Document

## udpSafeExIDList {#udpExID}

Name:
:  udpSafeExIDList

ElementID:
:  TBD4

Description:
: Observed ExIDs in the Experimental option (EXP, Kind=127).
: A basicList of udpExID Information Elements in which each udpExID Information Element carries the ExID observed in an EXP option.

Abstract Data Type:
:  basicList

Data Type Semantics:
:  list

Additional Information:
: See the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry at [URL_IANA_UDP_ExIDs].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about ExIDs.

Reference:
: This-Document

## udpUnsafeExIDList {#udpUExID}

Name:
:  udpUnsafeExIDList

ElementID:
:  TBD5

Description:
: Observed ExIDs in the UNSAFE Experimental option (UEXP, Kind=254).
: A basicList of udpExID Information Elements in which each udpExID Information Element carries the ExID observed in an UEXP option.

Abstract Data Type:
:  basicList

Data Type Semantics:
:  list

Additional Information:
: See the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry at [URL_IANA_UDP_ExIDs].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about ExIDs.

Reference:
: This-Document

# Examples {#sec-ex}

## Reduced-size Encoding

Given the UDP Kind allocation in {{Section 10 of !I-D.ietf-tsvwg-udp-options}} and the option mapping defined in {{udpOptions}} of this document, fewer octets are likely to be used for Flows with mandatory UDP options.

{{ex-udp}} shows an example of the Kind/bit mappings in the udpSafeOptions IE for a Flow in which End of Options List (EOL, Kind=0) and Alternate payload checksum (APC, Kind=2) options are observed. Only the bits that corresponds to EOL and APC options are set to 1.

~~~~
MSB                                                       LSB
                     1                          25
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 ... 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+...+-+-+-+-+-+-+-+-+
|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|   |0|0|0|0|0|1|0|1|
+-+-+-+-+-+-+-+-+-+-+-+-+-+-++-++-+-+-+-+...+-+-+-+-+-+-+-+-+
~~~~
{: #ex-udp title="An Example of udpSafeOptions IE with EOL and APC Options" artwork-align="center"}

One octet is sufficient to report these observed options because the leading zeros are dropped per the reduced-size encoding guidance. Concretely, the reported udpSafeOptions IE will be set to 0x05 ({{ex-udp-wire}}).

~~~~
MSB           LSB
 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+
|0|0|0|0|0|1|0|1|
+-+-+-+-+-+-+-+-+
~~~~
{: #ex-udp-wire title="An Example of the Wire udpSafeOptions IE Value with EOL and APC Options" artwork-align="center"}

## SAFE Experimental Option

Let us now consider a UDP Flow in which SAFE Experimental options are observed. If a udpSafeOptions IE is exported for this Flow, then that IE will have the EXP bit set to 1 ({{ex-udp-shared}}). This example does not make any assumption about the presence of other UDP options ("X" can be set to 0 or 1).

~~~~
MSB                                                     LSB
                  12                          25
 0 1 2 3 ... 7 8 9 0 1 2 3 4 5 6 7 8 9 ... 8 9 0 1 2 3 4 5
+-+-+-+-+...+-+-+-+-+-+-+-+-+-+-+-+-+-+-+...+-+-+-+-+-+-+-+
|X|X|X|X|   |X|X|X|X|X|X|X|X|X|X|X|1|X|X|   |X|X|X|X|X|X|X|
+-+-+-+-+...+-+-+-+-+-+-+-+-++-++-+-+-+-+...+-+-+-+-+-+-+-+
~~~~
{: #ex-udp-shared title="An Example of udpSafeOptions with EXP Option" artwork-align="center"}

## ExIDs and Reduced-size Encoding

Now assume that EOL, APC, EXP, and UEXP options are observed in a Flow. Let us also consider that the observed SAFE Experimental options have ExIDs set to 0x9858 and 0xE2D4, and UNSAFE Experimental options have ExIDs set to 0xC3D9 and 0x1234. {{ex-sho}} shows an excerpt of the Data Set encoding with a focus on SAFE Experimental options have ExIDs. The meaning of the fields is defined in {{!RFC6313}}.

~~~~
 MSB                                                          LSB
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
:                           ...                                 :
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      255      |        List Length = 9        |semantic=allof |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           udpExID = TBD3      |         Field Length = 2      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| SAFE ExID =  0x9858           | SAFE ExID = 0xE2D4            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      255      |        List Length = 9        |semantic=allof |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           udpExID = TBD3      |         Field Length = 2      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| UNSAFE ExID =  0xC3D9         | UNSAFE ExID =  0x1234         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
:                           ...                                 :
~~~~
{: #ex-sho title="Example of UDP Experimental Option ExID IEs" artwork-align="center"}

Following the guidance in {{udpOptions}}, the reported udpSafeOptions IE will be set to 0x05 even in the presence of EXP options.

# Security Considerations

This document does not introduce new security considerations other than those already discussed in {{Section 11 of !RFC7011}} and {{Section 8 of !RFC7012}}.

The reader may refer to {{Section 24 of !I-D.ietf-tsvwg-udp-options}} for the security considerations related to UDP options.

# IANA Considerations {#IANA}

## IPFIX Information Elements

This document requests IANA to add the following new IEs to the "IPFIX Information Elements" registry under the "IP Flow Information Export (IPFIX) Entities" registry group {{IANA-IPFIX}}:

|Value|	Name|	Reference|
|TBD1| udpSafeOptions|{{udpOptions}} of This-Document|
|TBD2| udpUnsafeOptions|{{udpUnsafeOptions}} of This-Document|
|TBD3| udpExID|{{udpBasicExID}} of This-Document|
|TBD4| udpSafeExIDList|{{udpExID}} of This-Document|
|TBD5| udpUnsafeExIDList|{{udpUExID}} of This-Document|
{: title="New IPFIX Information Elements"}

> udpSafeOptions uses the abstract data type ("unsigned256") defined in {{!I-D.ietf-opsawg-ipfix-tcpo-v6eh}}.

--- back

# Acknowledgments
{:numbered="false"}

Thanks to Benoît Claise for the discussion on the ordering of IPFIX IEs. Thanks to Paul Aitken for the review and comments.

Thanks to Tommy Pauly for the tsvart review, Joe Touch for the intdir review, Robert Sparks for the genart review, Watson Ladd for the secdir review, and Jouni Korhonen for the opsdir review.

Thanks to Thomas Graf for the Shepherd review.

Thanks to Mahesh Jethanandani for the AD review.
