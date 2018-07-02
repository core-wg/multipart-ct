---
stand_alone: true
ipr: trust200902
docname: draft-ietf-core-multipart-ct-latest
cat: std
pi:
  strict: 'yes'
  toc: 'yes'
  tocdepth: '4'
  symrefs: 'yes'
  sortrefs: 'yes'
  compact: 'yes'
  subcompact: 'no'
  comments: 'yes'
  inline: 'yes'
title: Multipart Content-Format for CoAP
abbrev: Multipart Content-Format for CoAP
area: ART
wg: CoRE
kw: CoAP, Multipart Content-Format
date: 2018-06-25
author:
- ins: T. F. Fossati
  name: Thomas Fossati
  org: Nokia
  email: thomas.fossati@nokia.com
-
  name: Klaus Hartke
  org: Ericsson
  email: klaus.hartke@ericsson.com
  street: Torshamnsgatan 23
  city: Stockholm
  code: SE-16483
  country: Sweden
-
  ins: C. Bormann
  name: Carsten Bormann
  org: Universität Bremen TZI
  street: Postfach 330440
  city: Bremen
  code: D-28359
  country: Germany
  phone: +49-421-218-63921
  email: cabo@tzi.org

normative:
  RFC7049: cbor
  RFC7252: coap
informative:
  I-D.ietf-ace-coap-est: est-coap
  I-D.ietf-cbor-cddl: cddl

--- abstract

This memo defines application/multipart-core, an
application-independent media-type that can be used
to combine representations of several different media types into a single
CoAP message-body with minimal framing overhead, each along with a CoAP
Content-Format identifier.

--- middle

# Introduction

This memo defines application/multipart-core, an application-independent
media-type that can be used to combine representations of several different media types
into a single CoAP {{-coap}} message-body with minimal framing
overhead, each along with a CoAP Content-Format identifier.

This simple and efficient binary framing mechanism can be employed to
create application specific request and response bodies which build on
multiple already existing media types.

Applications using the application/multipart-core Content-Format define the
internal structure of the application/multipart-core representation.

For example, one way to structure the sub-types specific to an application/multipart-core
container is to always include them at the same fixed position.
This specification allows to indicate that an optional part is not
present by substituting a null value for the representation of the part.

Optionally, an application might use the general format defined here,
but also register a new media type and an associated Content-Format
identifier --- typically one in the range 10000-64999 --- instead of
using application/multipart-core.


<!--  Leave out until needed:

## Requirements Language

{: :boilerplate bcp14}
-->

# Multipart Content-Format Encoding

A representation of media-type application/multipart-core contains a collection of
zero or more representations, each along with their respective content
format.

The collection is encoded as a CBOR {{-cbor}} array with an even
number of elements.
The second, fourth, sixth, etc. element is a byte string containing
a representation, or the value `null` if an optional part is indicated
as not given.
The first, third, fifth, etc. element is an unsigned integer
specifying the content format ID of the representation following it.
(Future extensions might want to include additional alternative ways of
specifying the media type of a representation in such a position.)

For example, a collection containing two representations, one with
content format ID 42 and one with content format ID 0, looks like this
in CBOR diagnostic notation:

> \[42, h'0123456789abcdef', 0, h'3031323334']

For illustration, the structure of an application/multipart-core representation can
be described by the CDDL {{-cddl}} specification in {{mct-cddl}}:

~~~CDDL
multipart-core = [* multipart-part]
multipart-part = (type: uint .size 2 / text, part: bytes / null)
~~~
{:#mct-cddl title="CDDL for application/multipart-core"}

# IANA Considerations {#IANA}

## Registration of media type application/multipart-core

IANA is requested to register the following media type {{?RFC6838}}:

Type name:
: application

Subtype name:
: multipart-core

Required parameters:
: N/A

Optional parameters:
: N/A

Encoding considerations:
: binary

Security considerations:
: See the Security Considerations Section of RFCthis

Interoperability considerations:
: N/A

Published specification:
: RFCthis

Applications that use this media type:
: Applications that need to combine representations of potentially
  several media types into one, e.g., EST-CoAP {{-est-coap}}

Fragment identifier considerations:
: N/A

Additional information:
:     Deprecated alias names for this type:
      : N/A

      Magic number(s):
      : N/A

      File extension(s):
      : N/A

      Macintosh file type code(s):
      : N/A

Person & email address to contact for further information:
: iesg&ietf.org

Intended usage:
: COMMON

Restrictions on usage:
: N/A

Author:
: CoRE WG

Change controller:
: IESG

Provisional registration? (standards tree only):
: no


## Registration of a Content-Format identifier for application/multipart-core

IANA is requested to register the following Content-Format to the
"CoAP Content-Formats" subregistry, within the "Constrained RESTful
Environments (CoRE) Parameters" registry, from the IETF Review space
(specifically, 256..999):

   | Media Type                 | Encoding | ID   | Reference |
   | application/multipart-core | ---      | TBD1 | RFCthis   |

# Security Considerations {#Security}

The security considerations of {{-cbor}} apply.  In particular,
resource exhaustion attacks may employ large values for the byte string
size fields, or deeply nested structures of recursively embedded
application/multipart-core representations.

--- back


# Acknowledgements
{: numbered='no'}

Most of the text in this draft is from earlier contributions by two of
the authors, Thomas Fossati and Klaus Hartke.  The re-mix in this
document is based on the requirements in {{-est-coap}}, based on
discussions with Michael Richardson, Panos Kampanis and Peter van der
Stok.

