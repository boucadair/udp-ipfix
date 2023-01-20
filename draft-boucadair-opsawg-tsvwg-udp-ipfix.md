---
title: "Export of UDP Options Information in IP Flow Information Export (IPFIX)"
abbrev: "IPFIX IE for UDP Options"
category: std

docname: draft-boucadair-opsawg-tsvwg-udp-ipfix-latest
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

This document specifies new IPFIX Information Elements for UDP options ({{IANA}}). A brief overview of UDP option is provided in {{uo}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the terms defined in Section 3 of {{!I-D.ietf-tsvwg-udp-options}} and {{!RFC7011}}.

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

Options indicated by Kind values in the range 0-191 are called SAFE options because they do not alter the UDP data payload. Such options can be silently ignored by receivers without affecting the meaning of the UDP user data (Section 9 of {{!I-D.ietf-tsvwg-udp-options}}).

Options indicated by Kind values in the range 192-255 are called UNSAFE options. Such options are not safe to ignore (Section 10 of {{!I-D.ietf-tsvwg-udp-options}}).

{{!I-D.ietf-tsvwg-udp-options}} reserves two options for experiements: the Experimental option (EXP, Kind=127) for SAFE options and the UNSAFE Experimental option (UEXP, Kind=254). For both options, Experimental ID (ExIDs) are used to differentiate concurrent use of these options. Known ExIDs are expected to be registered within IANA. {{udpExID}} specifies a new IPFIX to export observed ExIDs in the EXP options. Also, {{udpUExID}} specifies a new IPFIX to export observed ExIDs in the UEXP options. Only 16-bits ExIDs are supported.

This document does not intend to elaborate operational guidance/implications of UDP options. The document focuses exclusively on exporting observed UDP options in datagrams. The motivation for exporting such data is similar to the one for exporting TCP options (tcpOptions IE) or IPv6 Extension Headers (ipv6ExtensionHeaders).


# Security Considerations

This document does not introduce new security considerations other than those already discussed in {{!RFC7012}}.

# IANA Considerations {#IANA}

This document requests IANA to add the following new IEs to the IANA registry entitled "IP Flow Information Export (IPFIX) Entities" {{IANA-IPFIX}}.

## udpOptions {#udpOptions}

* Name:  udpOptions
* ElementID:  TBD1
* Description: Observed UDP options of a Flow.  The information is encoded in a set of bit fields. Options are mapped to bits according to their option numbers.
      Option number X is mapped to bit X. A bit is set to 1 (or 0) if the corresponding UDP option is observed (or not).
* Abstract Data Type:  unsigned8
* Data Type Semantics:  flags
* Additional Information:  {{!I-D.ietf-tsvwg-udp-options}}.  See the assignments in the "xxxx" IANA registry at URL_IANA_UDP_OPTIONS.
* Reference:  [This-Document]

## udpExID {#udpExID}

* Name:  udpExID
* ElementID:  TBD2
* Description: Observed Expermients ID (ExIDs) in the Experimental option (EXP, Kind=127). The information is encoded in a set of 16-bit fields. Each 16-bit field carries the observed ExID in an EXP option.
* Abstract Data Type:  octetArray
* Data Type Semantics:  identifier
* Additional Information:  {{!I-D.ietf-tsvwg-udp-options}}.  See the assignments in the "xxxx" IANA registry at URL_IANA_UDP_ExIDs.
* Reference:  [This-Document]

## udpUnsafeExID {#udpUExID}

* Name:  udpUnsafeExID
* ElementID:  TBD3
* Description: Observed Expermients ID (ExIDs) in the UNSAFE Experimental option (UEXP, Kind=254). The information is encoded in a set of 16-bit fields. Each 16-bit field carries the observed ExID in an UEXP option.
* Abstract Data Type:  octetArray
* Data Type Semantics:  identifier
* Additional Information:  {{!I-D.ietf-tsvwg-udp-options}}.  See the assignments in the "xxxx" IANA registry at URL_IANA_UDP_ExIDs.
* Reference:  [This-Document]
--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
