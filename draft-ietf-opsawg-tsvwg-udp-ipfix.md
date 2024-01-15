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

IP Flow Information Export (IPFIX) {{!RFC7011}} is a protocol that is widely deployed in operators networks for traffic management purposes. The protocol specifies the encoding of a set of basic data types and how the various Information Elements (IEs) are transmitted. In order to support the export of new flow-related measurement data, new IEs can be defined and registered in a dedicated IANA registry {{IANA-IPFIX}} for interoperability.

This document specifies new IPFIX Information Elements for UDP options ({{sec-IE}}). A brief overview of UDP option is provided in {{uo}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the IPFIX-specific terminology (e.g., Flow) defined in {{Section 2 of !RFC7011}}.
As in {{!RFC7011}}, these IPFIX-specific terms have the first letter of a word capitalized.

Also, this document uses the terms defined in {{Section 3 of !I-D.ietf-tsvwg-udp-options}}.

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

Options indicated by Kind values in the range 0-191 are called SAFE options because they do not alter the UDP data payload. Such options can be silently ignored by legacy receivers because they do not alter the UDP user data ({{Section 9 of !I-D.ietf-tsvwg-udp-options}}).

Options indicated by Kind values in the range 192-255 are called UNSAFE options. Such options are not safe for legacy receivers to ignore because they alter the UDP user data ({{Section 10 of !I-D.ietf-tsvwg-udp-options}}).

UDP options occur per-packet within a Flow and can be inserted at any time in the Flow.

{{!I-D.ietf-tsvwg-udp-options}} reserves two options for experiements: the Experimental option (EXP, Kind=127) for SAFE options and the UNSAFE Experimental option (UEXP, Kind=254). For both options, Experimental ID (ExIDs) are used to differentiate concurrent use of these options. Known ExIDs are expected to be registered within IANA. {{udpExID}} specifies a new IPFIX IE to export observed ExIDs in the EXP options. Also, {{udpUExID}} specifies a new IPFIX to export observed ExIDs in the UEXP options. Only 16-bits ExIDs are supported.

This document does not intend to elaborate operational guidance/implications of UDP options. The document focuses exclusively on exporting observed UDP options in datagrams. The motivation for exporting such data is similar to the one for exporting TCP options (tcpOptions) or IPv6 Extension Headers (ipv6ExtensionHeaders).

# New UDP IPFIX Information Elements {#sec-IE}

> Note: "URL_IANA_UDP_OPTIONS" is the URL of the "UDP Option Kind Numbers" registry group while "URL_IANA_UDP_ExIDs" is the URL of the "UDP Experimental Option Experiment Identifiers (UDP ExIDs)" registry that will be created by IANA as per {{Section 23 of !I-D.ietf-tsvwg-udp-options}}.

## udpOptions {#udpOptions}

Name:
: udpOptions

ElementID:
: TBD1

Description:
: Observed UDP options of a Flow. The information is encoded in a set of bit fields.
: Options are mapped to bits according to their option numbers. Option with a Kind value X is mapped to bit position "254-X" with bit 254 is the less-signficant bit of the IE and bit 0 is the most-significant bit. A bit is set to 1 if the corresponding UDP option is observed in the Flow. The bit is set to 0 if the option is not observed in the Flow.
: To cover the 0-255 kind range, up to 255 flags can be set in the value field. The encoding specified in {{Section 6.2 of !RFC7011}} is followed whenever fewer octets are needed to report observed UDP options. For example, if only option kinds =< 32 are observed, then the value can be encoded as unsigned32, or if only option kinds =< 63 are observed, then the value can be encoded as unsigned64.

Abstract Data Type:
:  unsigned

Data Type Semantics:
:  flags

Additional Information:
: See the assigned UDP options in the "UDP Option Kind Numbers" registry at [URL_IANA_UDP_OPTIONS].
: See {{!I-D.ietf-tsvwg-udp-options}} for more details about UDP options.

Reference:
: This-Document


## udpExpOptionExID {#udpExID}

Name:
:  udpExpExID

ElementID:
:  TBD2

Description:
: Observed Expermients ID (ExIDs) in the Experimental option (EXP, Kind=127).
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

## udpUnsafeExpOptionExID {#udpUExID}

Name:
:  udpUnsafeExpOptionExID

ElementID:
:  TBD3

Description:
: Observed Expermients ID (ExIDs) in the UNSAFE Experimental option (UEXP, Kind=254).
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

# An Example

Given UDP kind allocation in {{Section 8 of !I-D.ietf-tsvwg-udp-options}} and the option mapping defined in {{udpOptions}}, fewer octers are likely to be used for
Flows with mandatory UDP options.

{{ex-udp}} shows an example of reported values in a udpOptions IE for a Flow in which End of Options List (EOL) and Alternate payload checksum (APC) options are observed. One octet is sufficient to report these observed options. Concretely, the udpOptions IE will be set to 5.

~~~~
MSB                                                       LSB
                     1                   2     …  25
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 … 9 0 1 2 3 4
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+…+-+-+-+-+-+-+
|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0|0| |0|0|0|1|0|1|
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+…+-+-+-+-+-+-+
~~~~
{: #ex-udp title="An Example of udpOptions IE" artwork-align="center"}

# Security Considerations

This document does not introduce new security considerations other than those already discussed in {{!RFC7012}}.

The reader may refer to {{Section 22 of !I-D.ietf-tsvwg-udp-options}} for the security considerations related to UDP options.

# IANA Considerations {#IANA}

This document requests IANA to add the following new IEs to the IANA registry entitled "IP Flow Information Export (IPFIX) Entities" {{IANA-IPFIX}}:

|Value|	Name|	Reference|
|TBD1| udpOptions|{{udpOptions}} of This-Document|
|TBD2| udpExpOptionExID|{{udpExID}} of This-Document|
|TBD3| udpUnsafeExpOptionExID|{{udpUExID}} of This-Document|
{: title="New IPFIX Information Elements"}

--- back

# Acknowledgments
{:numbered="false"}

Thanks to Benoît Claise for the discussion on the ordering of IPFIX IEs.

Thanks to Joe Touch for the intdir review.
