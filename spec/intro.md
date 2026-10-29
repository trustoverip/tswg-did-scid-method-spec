## Introduction

`did:scid` is a new DID method designed to maximize the security, privacy, and portability benefits of self-certifying identifiers (SCIDs). Here is the definition of a SCID in the [*ToIP Glossary*](https://glossary.trustoverip.org/):

*A subclass of* [**verifiable identifier**](https://glossary.trustoverip.org/#term:verifiable-identifier) *(VID) that is* [**cryptographically verifiable**](https://glossary.trustoverip.org/#term:cryptographically-verifiable) *without the need to rely on any* [**third party**](https://glossary.trustoverip.org/#term:third-party) *for* [**verification**](https://glossary.trustoverip.org/#term:verification) *because the* [**identifier**](https://glossary.trustoverip.org/#term:identifier) *is cryptographically bound to the* [**cryptographic keys**](https://glossary.trustoverip.org/#term:cryptographic-keys) *from which it was generated.*

By this definition, a SCID does not necessarily need to be a decentralized identifier (DID) as defined by the [*W3C Decentralized Identifiers (DIDs) 1.0*](https://www.w3.org/TR/did-core/) specification. However, there are many advantages to defining **a DID method devoted exclusively to SCIDs, independent of the specific type of SCID**. 

That is the purpose of the `did:scid` method which is a deliverable of the [ToIP DID SCID Task Force](https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/94437377/DID+SCID+Task+Force+DSTF).

::: note
`did:scid` is technically a DID method specification because it defines a DID method namespace. However it is a **metamethod** because it defines a standard set of capabilities for other SCID-based DID methods called **source DID methods**. Each source DID method defines a `did:scid` **format**. Resolution and verification of a `did:scid` DID is designed to use the resolution and verification code of the source DID method as described in the [DID SCID Resolution Overview](https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/131858449/DID+SCID+Resolution+Overview).
:::

## Requirements Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [IETF RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).
