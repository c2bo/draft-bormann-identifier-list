---
title: "OAuth identifier List"
category: info
ipr: "trust200902"
keyword: ["security", "oauth2", "revocation", "status"]

docname: draft-bormann-identifier-list-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
v: 3
venue:
  github: "c2bo/draft-bormann-identifier-list"
  latest: "https://c2bo.github.io/draft-bormann-identifier-list/draft-bormann-identifier-list.html"
  wg: "Web Authorization Protocol"

author:
 -
    fullname: Christian Bormann
    email: chris.bormann@gmx.de
 -
    fullname: Paul Bastian
    email: paul.bastian@posteo.de

normative:
  RFC1950: RFC1950
  RFC1951: RFC1951
  RFC2046: RFC2046
  RFC3986: RFC3986
  RFC6125: RFC6125
  RFC6838: RFC6838
  RFC7515: RFC7515
  RFC7519: RFC7519
  RFC8259: RFC8259
  RFC8392: RFC8392
  RFC8949: RFC8949
  RFC9052: RFC9052
  RFC9110: RFC9110
  RFC9111: RFC9111
  IANA.MediaTypes:
    author:
      org: "IANA"
    title: "Media Types"
    target: "https://www.iana.org/assignments/media-types/media-types.xhtml"
  IANA.JOSE:
    author:
      org: "IANA"
    title: "JSON Object Signing and Encryption (JOSE)"
    target: "https://www.iana.org/assignments/jose/jose.xhtml"
  IANA.JWT:
    author:
      org: "IANA"
    title: "JSON Web Token Claims"
    target: "https://www.iana.org/assignments/jwt/jwt.xhtml"
  IANA.CWT:
    author:
      org: "IANA"
    title: "CBOR Web Token (CWT) Claims"
    target: "https://www.iana.org/assignments/cwt/cwt.xhtml"
  IANA.COSE:
    author:
      org: "IANA"
    title: "CBOR Object Signing and Encryption (COSE)"
    target: "https://www.iana.org/assignments/cose/cose.xhtml"
  CWT.typ: I-D.ietf-cose-typ-header-parameter
informative:
  RFC5280: RFC5280
  RFC6749: RFC6749
  RFC7662: RFC7662
  RFC7800: RFC7800
  SD-JWT.VC: I-D.ietf-oauth-sd-jwt-vc
  StatusList: I-D.ietf-oauth-identifier-list
  ISO.mdoc:
    author:
      org: "ISO/IEC JTC 1/SC 17"
    title: "ISO/IEC 18013-5:2021 ISO-compliant driving licence"


--- abstract

This specification defines a new status mechanism according to {{StatusList}} to convey information about tokens after their issuance. The Identifier List links the status to a unique identifier in the referenced token, similar to the Certificate Revocation List {{RFC5280}} but secured by JOSE {{IANA.JOSE}} and COSE {{IANA.COSE}}.

--- middle

# Introduction

Token formats secured by JOSE {{IANA.JOSE}} or COSE {{RFC9052}}, such as JSON Web Tokens (JWTs) {{RFC7519}}, CBOR Web Tokens (CWTs) {{RFC8392}} and ISO mdoc {{ISO.mdoc}}, have vast possible applications. Some of these applications can involve issuing a token whereby certain semantics about the token can change over time, which are important to be able to communicate to relying parties in an interoperable manner, such as whether the token is considered invalidated or suspended by its issuer.

This document defines a new status mechanism using the framework that is given by the Status List {{StatusList}}, thus registering a new mechanism in it's registry.

This document defines an Identifier List and its representations in JSON and CBOR formats that describe the individual statuses of multiple Referenced Tokens, which themselves are JWTs or CWTs. The statuses of the listed Referenced Tokens are conveyed via an array in the Idenitfier List. Each Referenced Token is allocated an unique identifier during issuance. The status of each Referenced Token is then linked to its unique identifier. Not all Referenced Tokens provided by the Issuer may be present in the Identifier List, the absense of a Referenced Token may therefore be interpreted by a default value. An Identifier List may either be provided by an endpoint or be signed and embedded into an Identifier List Token, whereas this document defines its representations in JWT and CWT.


~~~ ascii-art

+-------------------+                  +------------------------+
|                   | describes status |                        |
|  Identifier List  +----------------->|    Referenced Token    |
|   (JSON or CBOR)  <------------------+      (JOSE, COSE)      |
|                   |   references     |                        |
+-------+-----------+                  +--------+---------------+
        |
        |embedded in
        v
+-------------------+
|                   |
|  Identifier List  |
| Token (JWT or CWT)|
|                   |
+-------------------+

~~~

## Rationale

> TODO: new Rationale:
- very simple
- optimized for very low revocation rates
- no interaction between Issuance and revocation service

## Design Considerations

The decisions taken in this specification aim to achieve the following design goals:

* the specification shall favor a simple and easy to understand concept
* the specification shall be easy, fast and secure to implement in all major programming languages
* the specification shall be optimized to support the most common use cases and avoid unnecessary complexity of corner cases
* the Identifier List shall enable caching policies and offline support
* the specification shall support JSON and CBOR based tokens
* the specification shall not specify key resolution or trust frameworks

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Terminology

The specification uses the terms "Isusuer", "Relying Party", "Referenced Token" defined by {{StatusList}}.

Idenitifier List:
: An object in JSON or CBOR representation containing an array that lists the statuses of multiple Referenced Tokens.

Idenitifier List Token:
: A token in JWT or CWT representation that contains a cryptographically secured Idenitifier List.

> TODO: change definition in StatusList to reuse Referenced Token

# Idenitfier List {#identifier-list}

## Identifier List in JSON Format {#identifier-list-json}

This section defines the structure for a JSON-encoded Idenitifier List:

* `status`: REQUIRED, as defined in section xyz of {{StatusList}}
  * `identifier_list` : REQUIRED, an array containing objects with the following entries
    * `id` : REQUIRED, represents the identifier of the Referenced Token.
    * `status`: REQUIRED, values are the same as IANA Status Registry

The identifier list objects may contains other claims.

The following example illustrates the JSON representation of the Idenitifier List:
Option 1a:
```json
"status" : {
    "identifier_list": [
        { "id" : 256984364732378, "status" : 1},
        { "id" : 719348638462628, "status" : 1}
    ]
}
```
> We currently prefer Option 1b:

Option 1b:
```json
"status" : {
    "identifier_list": {
        "256984364732378": { "status" : 1},
        "719348638462628": { "status" : 1}
    }
}
```


## Idenitifier List in CBOR Format {#identifier-list-cbor}

This section defines the structure for a CBOR-encoded Identifier List:

The following example illustrates the CBOR representation of the Idenitifier List:

The following is the CBOR diagnostic output of the example above:

# Idenitifier List Token {#identifier-list-token}

A Idenitifier List Token embeds the Idenitifier List into a token that is cryptographically signed and protects the integrity of the Idenitifier List. This allows for the Idenitifier List Token to be hosted by third parties or be transferred for offline use cases.

This section specifies Idenitifier List Tokens in JSON Web Token (JWT) and CBOR Web Token (CWT) format.

## Idenitifier List Token in JWT Format {#identifier-list-token-jwt}

> Paul: I've worked until here

The Identifier List Token MUST be encoded as a "JSON Web Token (JWT)" according to {{RFC7519}}.

The following content applies to the JWT Header:

* `typ`: REQUIRED. The JWT type MUST be `identifierlist+jwt`.

The following content applies to the JWT Claims Set:

* `iss`: REQUIRED, as defined in section xyz of {{StatusList}}
* `sub`: REQUIRED, as defined in section xyz of {{StatusList}}
* `iat`: REQUIRED, as defined in section xyz of {{StatusList}}
* `exp`: OPTIONAL, as defined in section xyz of {{StatusList}}
* `ttl`: OPTIONAL, as defined in section xyz of {{StatusList}}
* `identifier_list`: REQUIRED. The `identifier_list` (identifier list) claim MUST specify the Identifier List conforming to the rules outlined in [](#identifier-list-json).

The following additional rules apply:

1. The JWT MAY contain other claims.

2. The JWT MUST be digitally signed using an asymmetric cryptographic algorithm. Relying parties MUST reject the JWT if it is using a Message Authentication Code (MAC) algorithm. Relying parties MUST reject JWTs with an invalid signature.

3. Relying parties MUST reject JWTs that are not valid in all other respects per "JSON Web Token (JWT)" {{RFC7519}}.

4. Application of additional restrictions and policy are at the discretion of the verifying party.

The following is a non-normative example for a Identifier List Token in JWT format:

// ~~~~~~~~~~
// {::include ./examples/identifier_list_jwt}
// ~~~~~~~~~~

## Identifier List Token in CWT Format {#identifier-list-token-cwt}

The Identifier List Token MUST be encoded as a "CBOR Web Token (CWT)" according to {{RFC8392}}.

The following content applies to the CWT protected header:

* `16` TBD (type): REQUIRED. The type of the CWT MUST be `statuslist+cwt` as defined in {{CWT.typ}}.

The following content applies to the CWT Claims Set:

* `1` (issuer): REQUIRED. Same definition as `iss` claim in [](#identifier-list-token-jwt).
* `2` (subject): REQUIRED. Same definition as `sub` claim in [](#identifier-list-token-jwt).
* `6` (issued at): REQUIRED. Same definition as `iat` claim in [](#identifier-list-token-jwt).
* `4` (expiration time): OPTIONAL. Same definition as `exp` claim in [](#identifier-list-token-jwt).
* `65530` (identifier list): REQUIRED. The identifier list claim MUST specify the Identifier List conforming to the rules outlined in [](#identifier-list-cbor).

The following additional rules apply:

1. The CWT MAY contain other claims.

2. The CWT MUST be digitally signed using an asymmetric cryptographic algorithm. Relying parties MUST reject the CWT if it is using a Message Authentication Code (MAC) algorithm. Relying parties MUST reject CWTs with an invalid signature.

3. Relying parties MUST reject CWTs that are not valid in all other respects per "CBOR Web Token (CWT)" {{RFC8392}}.

4. Application of additional restrictions and policy are at the discretion of the verifying party.

The following is a non-normative example for a Identifier List Token in CWT format (not including the type header yet):

// ~~~~~~~~~~
// {::include ./examples/identifier_list_cwt}
// ~~~~~~~~~~

The following is the CBOR diagnostic output of the example above:

// ~~~~~~~~~~
// {::include ./examples/identifier_list_cwt_diag}
// ~~~~~~~~~~

# Referenced Token {#referenced-token}

## Status Claim {#status-claim}

By including a "status" claim in a Referenced Token, the Issuer is referencing a mechanism to retrieve status information about this Referenced Token. The claim contains members used to reference to an Identifier List as defined in this specification. Other members of the "status" object may be defined by other specifications. This is analogous to "cnf" claim in Section 3.1 of {{RFC7800}} in which different authenticity confirmation methods can be included.

## Referenced Token in JWT Format {#referenced-token-jwt}

The Referenced Token MUST be encoded as a "JSON Web Token (JWT)" according to {{RFC7519}}.

The following content applies to the JWT Claims Set:

* `iss`: REQUIRED, as defined in section xyz of {{StatusList}}
* `status`: REQUIRED, as defined in section xyz of {{StatusList}}
  * `identifier_list`: REQUIRED when the identifier list mechanism defined in this specification is used. It contains a reference to a Identifier List or Status Identifier Token. The object contains exactly two claims:
    * `id`: REQUIRED. The `id` (identifier) claim MUST specify a String that represents the identifier to check for status information in the Identifier List for the current Referenced Token.
    * `uri`: REQUIRED. The `uri` (URI) claim MUST specify a String value that identifies the Identifier List or Identifier List Token containing the status information for the Referenced Token. The value of `uri` MUST be a URI conforming to {{RFC3986}}.

Application of additional restrictions and policy are at the discretion of the verifying party.

The following is a non-normative example for a decoded header and payload of a Referenced Token:

~~~ ascii-art

{
  "alg": "ES256",
  "kid": "11"
}
.
{
  "iss": "https://example.com",
  "status": {
    "identifier_list": {
      "id": "256984364732378",
      "uri": "https://example.com/statuslists/1"
    }
  }
}
~~~

## Referenced Token in CWT Format {#referenced-token-cwt}

The Referenced Token MUST be encoded as a "COSE Web Token (CWT)" object according to {{RFC8392}}.

The following content applies to the CWT Claims Set:

* `1` (issuer): REQUIRED. Same definition as `iss` claim in [](#referenced-token-jwt).
* `65535` (status): REQUIRED. The status claim is encoded as a `Status` CBOR structure and MUST include at least one data item that refers to a status mechanism. Each data item in the `Status` CBOR structure comprises a key-value pair, where the key must be a CBOR text string (Major Type 3) specifying the identifier of the status mechanism, and the corresponding value defines its contents. This specification defines the following data items:
  * `identifier_list` (identifier list): REQUIRED when the identifier list mechanism defined in this specification is used. It has the same definition as the `identifier_list` claim in [](#referenced-token-jwt) but MUST be encoded as a `StatusListInfo` CBOR structure with the following fields:
    * `idx`: REQUIRED. Same definition as `idx` claim in [](#referenced-token-jwt).
    * `uri`: REQUIRED. Same definition as `uri` claim in [](#referenced-token-jwt).

Application of additional restrictions and policy are at the discretion of the verifying party.

The following is a non-normative example for a decoded payload of a Referenced Token:

~~~ ascii-art

18(
    [
      / protected / << {
        / alg / 1: -7 / ES256 /
      } >>,
      / unprotected / {
        / kid / 4: h'3132' / '13' /
      },
      / payload / << {
        / iss    / 1: "https://example.com",
        / status / 65535: {
          "status_list": {
            "idx": "0",
            "uri": "https://example.com/statuslists/1"
          }
        }
      } >>,
      / signature / h'...'
    ]
  )
~~~

## Referenced Token in other COSE/CBOR Format {#referenced-token-cose}

The Referenced Token MUST be encoded as a `COSE_Sign1` or `COSE_Sign` CBOR structure as defined in "CBOR Object Signing and Encryption (COSE)" {{RFC9052}}.

It is required to encode the status mechanisms referred to in the Referenced Token using the `Status` CBOR structure defined in [](#referenced-token-cwt).

It is RECOMMENDED to use `status` for the label of the field that contains the `Status` CBOR structure.

Application of additional restrictions and policy are at the discretion of the verifying party.

The following is a non-normative example for a decoded payload of a Referenced Token:

TBD: example

# Status Types {#status-types}

Reusing the same Status registered in IANA by Status List

# Verification and Processing

## Status List Request

To obtain the Status List or Status List Token, the Relying Party MUST send a HTTP GET request to the Status List Endpoint. Communication with the Status List Endpoint MUST utilize TLS. Which version(s) should be implemented will vary over time. A TLS server certificate check MUST be performed as defined in Section 5 and 6 of {{RFC6125}}.

The Relying Party SHOULD send the following Accept-Header to indicate the requested response type:

- "application/statuslist+json" for Status List in JSON format
- "application/statuslist+jwt" for Status List in JWT format
- "application/statuslist+cbor" for Status List in CBOR format
- "application/statuslist+cwt" for Status List in CWT format

If the Relying Party does not send an Accept Header, the response type is assumed to be known implicit or out-of-band.

## Status List Response

In the successful response, the Status List Provider MUST use the following content-type:

- "application/statuslist+json" for Status List in JSON format
- "application/statuslist+jwt" for Status List in JWT format
- "application/statuslist+cbor" for Status List in CBOR format
- "application/statuslist+cwt" for Status List in CWT format

In the case of "application/statuslist+json", the response MUST be of type JSON and follow the rules of [](#identifier-list-json).
In the case of "application/statuslist+jwt", the response MUST be of type JWT and follow the rules of [](#identifier-list-token-jwt).
In the case of "application/statuslist+cbor", the response MUST be of type CBOR and follow the rules of [](#identifier-list-cbor).
In the case of "application/statuslist+cwt", the response MUST be of type CWT and follow the rules of [](#identifier-list-token-cwt).

The HTTP response SHOULD use gzip Content-Encoding as defined in {{RFC9110}}.

## Caching

If caching is required (e.g., to enable the use of alternative mechanisms for hosting, like Content Delivery Networks), the control of the caching mechanism SHOULD be implemented using the standard HTTP Cache-Control as defined in {{RFC9111}}.

## Validation Rules

TBD


# Privacy Considerations

## Limiting issuers observability of token verification {#privacy-issuer}

The same considerations as in {{StatusList}} applies.

## Malicious Issuers

The same considerations as in {{StatusList}} applies.

## Unobservability of Relying Parties {#privacy-relying-party}

The same considerations as in {{StatusList}} applies.

## Unlinkability

Colluding Issuers and a Relying Parties have the possibility to link two transactions, as the tuple of `uri` and `index` inside the Referenced Token are unique and therefore traceable data. By comparing the status claims of received Referenced Tokens, two colluding Relying Parties could determine that they have interacted with the same user or an Issuer could trace the usage of its issued Referenced Token by colluding with various Relying Parties. It is therefore recommended to use Status Lists for Referenced Token formats that have similar unlinkability properties.

To avoid privacy risks for colluding Relying Parties, it is RECOMMENDED that Issuers use batch issuance to issue multiple tokens, see [](#implementation-lifecycle).

To avoid further correlatable information by the values of `uri` and `index`, Issuers are RECOMMENDED to:

- choose non-sequential, pseudo-random or random indices
- use decoy or dead entries to obfuscate the real number of Referenced Tokens within a Status List
- choose to deploy and utilize multiple Status Lists simultaneously

## Third Party Hosting

The same considerations as in {{StatusList}} applies.

# Implementation Considerations {#implementation}

## Token Lifecycle {#implementation-lifecycle}

The lifetime of a Status List (and the Status List Token) depends on the lifetime of its Referenced Tokens. Once all Referenced Tokens are expired, the Issuer may stop serving the Status List (and the Status List Token).

Referenced Tokens may be regularly re-issued to increase security or to mitigate linkability and prevent tracking by Relying Parties. In this case, every Referenced Token MUST have a fresh Status List entry.

Referenced Tokens may also be issued in batches, such that Holders can use individual tokens for every transaction. In this case, every Referenced Token MUST have a dedicated Status List entry. Revoking batch issued Referenced Tokens might reveal this correlation later on.

# IANA Considerations

## JSON Web Token Claims Registration

This specification requests registration of the following Claims in the
IANA "JSON Web Token Claims" registry {{IANA.JWT}} established by {{RFC7519}}.

### Registry Contents

*  Claim Name: `identifier_list`
*  Claim Description: An identifier list containing up-to-date status information on multiple other JWTs encoded as a array.
*  Change Controller: IETF
*  Specification Document(s):  [](#identifier-list-token-jwt) of this specification

## JWT Status Mechanism Methods Registry {#iana-registry}

This specification requests registration of the following mechanism in the IANA "Status Mechanism Methods" registry established by {{StatusList}}.

### Registry Contents

*  Status Method Value: `identifier_list`
*  Status Method Description: An identifier list containing up-to-date status information on multiple other JWTs encoded as a array.
*  Change Controller: IETF
*  Specification Document(s):  [](#referenced-token-jwt) of this specification

## CBOR Web Token Claims Registration

This specification requests registration of the following Claims in the
IANA "CBOR Web Token (CWT) Claims" registry {{IANA.CWT}} established by {{RFC8392}}.

### Registry Contents

*  Claim Name: `identifier_list`
*  Claim Description: An identifier list containing up-to-date status information on multiple other CWTs encoded as an array.
*  Change Controller: IETF
*  Specification Document(s):  [](#identifier-list-token-cwt) of this specification

## CWT Status Mechanism Methods Registry {#cwt-iana-registry}

This specification requests registration of the following mechanism in the IANA "Status Mechanism Methods" registry established by {{StatusList}}.

### Registry Contents

*  Status Method Value: `identifier_list`
*  Status Method Description: An identifier list containing up-to-date status information on multiple other CWTs encoded as a array.
*  Change Controller: IETF
*  Specification Document(s):  [](#referenced-token-cwt) of this specification

## Media Type Registration

This section requests registration of the following media types {{RFC2046}} in
the "Media Types" registry {{IANA.MediaTypes}} in the manner described
in {{RFC6838}}.

To indicate that the content is an JSON-based Status List:

  * Type name: application
  * Subtype name: statuslist+json
  * Required parameters: n/a
  * Optional parameters: n/a
  * Encoding considerations: binary; A JSON-based Status List is a JSON Object.
  * Security considerations: See (#Security) of \[ this specification \]
  * Interoperability considerations: n/a
  * Published specification: \[ this specification \]
  * Applications that use this media type: Applications using \[ this specification \] for updated status information of tokens
  * Fragment identifier considerations: n/a
  * Additional information:
    * File extension(s): n/a
    * Macintosh file type code(s): n/a
  * Person &amp; email address to contact for further information: Paul Bastian, paul.bastian@posteo.de
  * Intended usage: COMMON
  * Restrictions on usage: none
  * Author: Paul Bastian, paul.bastian@posteo.de
  * Change controller: IETF
  * Provisional registration? No

To indicate that the content is an JWT-based Status List:

  * Type name: application
  * Subtype name: statuslist+jwt
  * Required parameters: n/a
  * Optional parameters: n/a
  * Encoding considerations: binary; A JWT-based Status List is a JWT; JWT values are encoded as a series of base64url-encoded values (some of which may be the empty string) separated by period ('.') characters.
  * Security considerations: See (#Security) of \[ this specification \]
  * Interoperability considerations: n/a
  * Published specification: \[ this specification \]
  * Applications that use this media type: Applications using \[ this specification \] for updated status information of tokens
  * Fragment identifier considerations: n/a
  * Additional information:
    * File extension(s): n/a
    * Macintosh file type code(s): n/a
  * Person &amp; email address to contact for further information: Paul Bastian, paul.bastian@posteo.de
  * Intended usage: COMMON
  * Restrictions on usage: none
  * Author: Paul Bastian, paul.bastian@posteo.de
  * Change controller: IETF
  * Provisional registration? No

To indicate that the content is an CBOR-based Status List:

  * Type name: application
  * Subtype name: statuslist+cbor
  * Required parameters: n/a
  * Optional parameters: n/a
  * Encoding considerations: binary; A CBOR-based Status List is a CBOR Object.
  * Security considerations: See (#Security) of \[ this specification \]
  * Interoperability considerations: n/a
  * Published specification: \[ this specification \]
  * Applications that use this media type: Applications using \[ this specification \] for updated status information of tokens
  * Fragment identifier considerations: n/a
  * Additional information:
    * File extension(s): n/a
    * Macintosh file type code(s): n/a
  * Person &amp; email address to contact for further information: Paul Bastian, paul.bastian@posteo.de
  * Intended usage: COMMON
  * Restrictions on usage: none
  * Author: Paul Bastian, paul.bastian@posteo.de
  * Change controller: IETF
  * Provisional registration? No

To indicate that the content is an CWT-based Status List:

  * Type name: application
  * Subtype name: statuslist+cwt
  * Required parameters: n/a
  * Optional parameters: n/a
  * Encoding considerations: binary;
  * Security considerations: See (#Security) of \[ this specification \]
  * Interoperability considerations: n/a
  * Published specification: \[ this specification \]
  * Applications that use this media type: Applications using \[ this specification \] for updated status information of tokens
  * Fragment identifier considerations: n/a
  * Additional information:
    * File extension(s): n/a
    * Macintosh file type code(s): n/a
  * Person &amp; email address to contact for further information: Paul Bastian, paul.bastian@posteo.de
  * Intended usage: COMMON
  * Restrictions on usage: none
  * Author: Paul Bastian, paul.bastian@posteo.de
  * Change controller: IETF
  * Provisional registration? No

--- back

# Acknowledgments
{:numbered="false"}

We would like to thank
A

for their valuable contributions, discussions and feedback to this specification.

# Document History
{:numbered="false"}

* Initial draft
