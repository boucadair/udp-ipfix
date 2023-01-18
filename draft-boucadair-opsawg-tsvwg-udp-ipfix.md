---
title: "Export of UDP Options Inforamtion in IP Flow Information Export (IPFIX)"
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

normative:

informative:
  IANA-IPFIX:
     title: IP Flow Information Export (IPFIX) Entities
     target: https://www.iana.org/assignments/ipfix/ipfix.xhtml

--- abstract

This document specifies a new IP Flow Information Export (IPFIX) Information Element for UDP options.

--- middle

# Introduction

IP Flow Information Export (IPFIX) {{!RFC7011}} is a protocol that is widely deployed in operators networks for traffic management purposes. The protocol specifies the encoding of a set of basic data types and how the various Information Elements (IEs) are transmitted. In order to support the export of new flow-related measurement data, new IEs can be defined and registered in a dedicated IANA registry {{IANA-IPFIX}} for interoperability.

This document specifies a new IPFIX Information Element for UDP options ({{IANA}}). A brief overview of UDP option is provided in {{uo}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the terms defined in Section 3 of {{!I-D.ietf-tsvwg-udp-options}} and {{!RFC7011}}.

## UDP Options at a Glance {#uo}

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

This document does not intend to elaborate operational guidance/implications of UDP options. The document focuses exclusively on exporting observed UDP options in datagrams. The motivation for exporting such data is similar to the one for exporting TCP options (tcpOptions IE) or IPv6 Extension Headers (ipv6ExtensionHeaders).

# Security Considerations

This document does not introduce new security considerations other than those already discussed in {{!RFC7012}}.

# IANA Considerations {#IANA}

This document requests IANA to add the following new IE to the IANA registry entitled "IP Flow Information Export (IPFIX) Entities" {{IANA-IPFIX}}.

* Name:  udpOptions
* ElementID:  TBD1
* Description: Observed UDP options of a Flow.  The information is encoded in a set of bit fields. Options are mapped to bits according to their option numbers.
      Option number X is mapped to bit X. A bit is set to 1 (or 0) if the corresponding UDP option is observed (or not).
* Abstract Data Type:  unsigned8
* Data Type Semantics:  flags
* Additional Information:  {{!I-D.ietf-tsvwg-udp-options}}.  See the assignments in the "xxxx" IANA registry at URL_IANA_UDP_OPTIONS.
* Reference:  [This-Document]

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
