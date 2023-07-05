%%%
Title = "Human Readable Validate ROA Payload Notation"
abbrev = "VRP Notation"
ipr = "trust200902"

[seriesInfo]
status = "informational"
name = "Internet-Draft"
value = "draft-timbru-sidrops-vrp-notation-00"

[[author]]
initials="T."
surname="Bruijnzeels"
fullname="Tim Bruijnzeels"
organization = "NLnet Labs"
  [author.address]
  email = "tim@nlnetlabs.nl"

[pi]
 toc = "yes"
 compact = "yes"
 symrefs = "yes"
 sortrefs = "yes"

%%%

.# Abstract

This document defines a human readable notation for Validated ROA
Payloads (VRP, RFC 6811) based on ABNF (RFC 5234) for use with RPKI
tooling and documentation.

{mainmatter}

# Requirements notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in
this document are to be interpreted as described in BCP 14 [@!RFC2119]
[@!RFC8174] when, and only when, they appear in all capitals, as shown here.

# Introduction

This informational document defines a human readable notation for
Validated ROA Payloads (VRPs) [@!RFC6811].

We provide this notation because it can can help to create consistency
between RPKI Relying Party software output, making it easier for operators
to compare results. It can also be used by RPKI Certificate Authorities
(CA) command line interfaces and/or configuration. E.g. allowing a CA to
provide a listing of intended VRPs which can be easily compared to RP
output. Our final goal is that this will be helpful for documentation.

That said, this document is informational. Implementations can choose
to use their own notation styles instead of, or in addition to this.

# VRP Notation Definition

This specification uses ABNF syntax specified in [@!RFC5234].

~~~
notation      = vrp-prefix separator origin-asn
vrp-prefix    = v4-vrp-prefix / v6-vrp-prefix

v4-vrp-prefix = v4-cidr ["-" max-length-v4]
v4-cidr       = v4-address "/" v4-pfx-length
v4-address    = v4-byte "." v4-byte "." v4-byte "." v4-byte
v4-byte       = %d0-255
v4-pfx-length = %d0-32
v4-max-length = %d0-32

v6-vrp-prefix = v6-cidr ["-" v6-max-length]
v6-cidr       = v6-address "/" v6-pfx-length
v6-address    = (v6-un / v6-no-0 / v6-l-0 / v6-m-0 / v6-t-0)
v6-un         = "::"
v6-no-0       = v6-bytes 7*7(":" v6-bytes)
v6-l-0        = ":" 1*7(":" v6-bytes)
v6-m-0        = 1*6(v6-bytes ":") ":" 1*6(":" v6-bytes)
v6-t-0        = 1*7(v6-bytes ":") ":"
v6-bytes      = %x0-FFFF
v6-pfx-length = %d0-128
v6-max-length = %d0-128

separator     = " => "

origin-asn    = ["AS"] uint32
uint32        = %d0-4294967295
~~~

## roa-prefix

This field denotes a single Validated ROA Prefix (VRP) as defined in
section 2 of [@!RFC6811]. VRPs consist of either an IPv4 or an IPv6
prefix using CIDR prefix notation, optionally followed by a max-length
value.

Unfortunately we could not find pre-existing formal ABNF syntax
specifications for IPv4 and IPv6 CIDR prefix notation. The fairly basic
specifications defined above cover the notation of valid CIDR prefixes,
but it should be noted that they also allow for the notation of invalid
IPv6 addresses, and allow for prefix length values that are not applicable
to the base IP address. Rather than trying to define these rule in detail
here we refer to section 3.1 of [@!RFC4632] for guidelines on IPv4 CIDR
notation and section 2.3 of [@!RFC4291] for IPv6.

If the max-length is specified then its value needs to be the same as or
longer than the length of the prefix within the bounds applicable to the
address family of the prefix. If it is omitted then the effective
max-length for the VRP will be the same as the VPR prefix length.

## origin-asn

This field can optionally be prepended with the string "AS" followed by
a decimal value of a 32 bit Autonomous System Number using the asplain
presentation as specified in [@!RFC5396]. Decimal values MUST be used,
and values MUST be part of the range 0-4294967295.

# Example Notations

~~~
192.0.2.0/24 => AS65000
192.0.2.0/24-24 => AS65000
192.0.2.0/24-32 => AS65000
192.0.2.0/32 => AS65000

2001:db8::/32 => 65000,
2001:db8::/32-32 => 65000,
2001:db8::/32-128 => 65000,
2001:db8::/128 => 65000,
~~~

# IANA Considerations

This document has no IANA actions.

# Security Considerations

TBD

# Acknowledgements

TBD

{backmatter}
