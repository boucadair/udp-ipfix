---
title: "IPFIX IE for UDP Options"
abbrev: "UDP Options IPFIX IEs"
category: std

docname: draft-boucadair-opsawg-tsvwg-udp-ipfix-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Operations and Management Area Working Group"
keyword:
 - surplus area
 - UDP option

author:
 -
    fullname: Mohamed Boucadair
    organization: Orange
    email: mohamed.boucadair@orange.com

normative:

informative:


--- abstract

XXX.

This document specifies IPFIX IE for UDP options.


--- middle

# Introduction

TODO Introduction

{{!I-D.ietf-tsvwg-udp-options}} uses the surplus area (that is, the area of an IP payload that follows a UDP packet). See {{spa}}.

{{!I-D.ietf-tsvwg-udp-options}} base spec +  {{?I-D.ietf-tsvwg-udp-options-dplpmtud}} an example of UDP options.

Note that UDP options rely upon trailers, not headers to extend UDP XXXX. As such, more operational guidance may be elaborated in the future for such extensions. This document does not intend to elaborate those, but focues exclusively on exporting observed UDP options in datagrams.

~~~~
                             IP transport payload
                <------------------------------------------------->
      +--------+---------+----------------------+------------------+
      | IP Hdr | UDP Hdr |     UDP user data    |   surplus area   |
      +--------+---------+----------------------+------------------+
                <------------------------------>
                           UDP Length

                Figure 3
~~~~
{: #spa title="Surplus Are" artwork-align="center"}



# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the terms defined in Section 3 of {{!I-D.ietf-tsvwg-udp-options}} and {{!RFC7011}}.

UDP {{!RFC0768}}.

# Security Considerations

TODO Security


# IANA Considerations

This document requests IANA to XXX.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
