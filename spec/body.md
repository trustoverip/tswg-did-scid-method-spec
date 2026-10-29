## Why another DID method?

Given that nearly 200 different DID methods have been registered with the [W3C DID Methods Registry](https://www.w3.org/TR/did-extensions-methods/), it begs the question, “Why another DID method”. There are three main reasons.

### \#1: Self-Certifying Identifiers (SCIDs)

Because a SCID is is cryptographically bound to the cryptographic keys from which it was generated, it does not require reliance on a third party for verification. The only requirement to verify the binding is access to the **verification metadata** (the DID document and the key event history). Note that, if a SCID is used in peer-to-peer relationship, the verification metadata can be stored locally by each peer.

### \#2: Portability and Location Independence

Because a SCID is bound directly to its verification metadata, the SCID is fully portable, meaning it is not bound to any particular location for the verification metadata. For example, a `did:scid` DID can be used peer-to-peer, with a web server, with a blockchain, or any other storage target. The verification metadata can even be written to multiple locations to increase resilience—a technique called **multi-anchoring**. Any type of repository can be used to store the verification metadata: web servers, blockchains, trust registries, databases, file systems, etc.

### \#3: Privacy and Security

Because the cryptographic binding between the SCID and the verification metadata can be generated entirely within a digital wallet, secure element, or HSM, the generation phase can be both very secure and very private. This means SCIDs can work equally well for public DIDs and private DIDs.

SCIDs can further improve privacy for individuals because they are lightweight enough to support pairwise private DID connections. This enables DID applications used by individuals to automatically use pairwise pseudonyms and encrypted private channels for communications.

SCIDs can further improve security because:

- Each new version of the verification metadata (e.g., DID document and key event history) can include a hash of the previous version. By digitally signing the new version, this forms a **cryptographic event log**, also known as a **hashchain** or **microledger**.

- Storing the verification metadata in multiple locations—multi-anchoring—can make it harder to attack, particularly if SCIDs are used in conjunction with the ToIP [*High Assurance VIDs Specification*](https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/32473104/High+Assurance+VID+Task+Force+HAVID).

## Why support multiple formats?

The advantages of SCIDs have led to the development of multiple SCID-based DID methods. The first one, [*did:peer*](https://identity.foundation/peer-did-method-spec/), is still the default DID method used in [DIDComm](https://identity.foundation/didcomm-messaging/spec/v2.0/). However, `did:peer` assumes peer-to-peer usage, and thus does not include a mechanism for discovery of the location of verification metadata—a requirement for most publicly-verifiable DIDs. `did:peer` also does not support key rotation.

Several newer SCID-based DID methods now support a full range of DID features including key rotation, pre-rotation, and multi-sig. These include:

- [*did:webs*](https://trustoverip.github.io/tswg-did-method-webs-specification/) (based on [KERI AIDs](https://weboftrust.github.io/WOT-terms/docs/glossary/autonomic-identifier?level=2))

- [*did:webvh*](https://didwebvh.info/)

- [did:jlinc](https://did-spec.jlinc.org/) (also being used by [FedID](https://fedid.me/about/fedid-did-document))

- [did:plc](https://github.com/did-method-plc/did-method-plc) (used by [Bluesky](https://bsky.app/))

`did:webs` and `did:webvh` are web-based DID methods where the DID is bound to the location of a specific web server. While some degree of portability is still possible by using the DID document `alsoKnownAs` property to establish synonyms with other DIDs, this design does not support the requirement for the DID alone be able to serve as a permanent immutable account identifier (for more about this requirement, see [*Appendix B*](#appendix-b%3A-bluesky-did-requirements)).

`did:jlinc` is now being adapted for use with a federated network of servers to increase portability. However it is not currently designed for peer-to-peer usage.

`did:plc` uses SCIDs but in its current implementation uses a centralized directory service operated by Bluesky.

`did:scid` can address all of these limitations *without requiring new formats for SCIDs or SCID verification metadata*. It can reuse the formats listed above. While in theory `did:scid` could choose to support only one of these formats, picking one would discriminate against the others. Since all these formats have their adherents—and existing code bases that support them—`did:scid` is designed to support any of them, and be extensible to support other SCID-based formats *if necessary*. This enables adopters to choose the SCID format that works best for them, and for these formats to evolve over time.

## Examples

::: note
The first two sets of examples in this section are based on the SCIDs and verification metadata formats defined by the `did:webvh` and `did:webs` methods, both of which are web-based DID methods. While `did:scid` supports storing verification metadata on a web server, it is not strictly a web-based DID method because: a) it does not include any web location information in the DID itself (only in a `did:scid` URL parameter), and b) `did:scid` supports storing verification metadata in other locations, including blockchains, DHTs, and local storage in peer-to-peer usages.
:::

### did:scid Webvh Example

Examples in this section are based on [*the current specification for the did:webvh method*](https://identity.foundation/didwebvh/).

The first example is a “pure” `did:scid `DID because it does not include any location parameter. This is the format used in peer-to-peer exchange where the location information is implicit for both peers.

`did:scid:vh:1:QmfGEUAcMpzo25kF2Rhn8L5FAXysfGnkzjwdKoNPi615XQ`

The next three examples are `did:scid` URLs showing three different web servers where the verifiable history may be retrieved as specified by the [*did:webvh method*](https://identity.foundation/didwebvh/).

`did:scid:vh:1:QmfGEUAcMpzo25kF2Rhn8L5FAXysfGnkzjwdKoNPi615XQ?src=example.com/allan`

`did:scid:vh:1:QmfGEUAcMpzo25kF2Rhn8L5FAXysfGnkzjwdKoNPi615XQ?src=my.name.me`

`did:scid:vh:1:QmfGEUAcMpzo25kF2Rhn8L5FAXysfGnkzjwdKoNPi615XQ?src=did.company.info/employee/abc`

The final example is a `did:scid` URL that shows how the verification metadata could also be retrieved via another DID method that uses a blockchain.

::: warning
IMPORTANT: This example illustrates that a blockchain-based DID method *could* be modified to support `did:scid` DIDs by specifying:

1.  How the DID controller can store `did:scid` verification metadata on the blockchain.

2.  How a DID resolver can use the `did:scid` DID to look up and retrieve the verification metadata from the blockchain using [DID Linked Resources](https://w3c-ccg.github.io/DID-Linked-Resources/). See the [DID SCID Resolution Overview](https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/131858449/DID+SCID+Resolution+Overview) for more.
:::

This example uses a hypothetical modified version of the [cheqd DID method](https://docs.cheqd.io/product/architecture/adr-list/adr-001-cheqd-did-method) that specifies: 1) how the `did:scid` DID could be used as the `unique-id` component of a `did:cheqd` DID, and 2) how to retrieve the `did:scid` DID verification metadata from the [cheqd](https://cheqd.io/) blockchain.

`did:scid:vh:1:QmfGEUAcMpzo25kF2Rhn8L5FAXysfGnkzjwdKoNPi615XQ?src=did:cheqd:testnet`

### did:scid WebS Example

These are the same five examples as above except they use the (hypothetical) version 3 of the KERI AID specification. The first example shows how a `did:scid:ke` DID would be used in peer-to-peer mode where the location of the verification metadata is implicit.

`did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP`

The next three `did:scid:ke` URLs show where the DID document and the KERI event stream may be found on a web server as defined by the [*did:webs specification*](https://trustoverip.github.io/tswg-did-method-webs-specification).

`did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP?src=example.com/allan`

`did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP?src=my.name.me`

`did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP?src=did.company.info/`  
`employee/abc`

The final example shows how a `did:scid:ke` DID could store its verification metadata on the [cheqd](https://cheqd.io/) blockchain (again, assuming a modified version of the [cheqd DID method](https://docs.cheqd.io/product/architecture/adr-list/adr-001-cheqd-did-method) that supports `did:scid` DIDs as described above):

`did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP?src=did:cheqd:testnet`

### did:scid Peer Example

::: note
This example is intended to show how a peer-to-peer DID method such as `did:peer` could be used as a `did:scid` method format. However, creation of a `did:scid:pr` method is NOT proposed because both of the previous examples (`did:scid:vh` and `did:scid:ke`) can also be used peer-to-peer and both support key rotation and other security features not currently included in `did:peer`.
:::

This example uses variant \#2 of the `did:peer` [*method*](https://identity.foundation/peer-did-method-spec/). As with the first two examples above, `did:peer` assumes local storage, so there is no need for the `src` parameter.

`did:scid:pr:2:2.Vz6Mkj3PUd1WjvaDhNZhhhXQdz5UnZXmS7ehtx8bsPpD47kKc.Ez6LSg8zQom395jKLrGiBNruB9MM6V8PWuf2FpEy4uRFiqQBR.SeyJ0IjoiZG0iLCJzIjp7InVyaSI6Imh0dHA6Ly9leGFtcGxlLmNvbS9kaWRjb21tIiwiYSI6WyJkaWRjb21tL3YyIl0sInIiOlsiZGlkOmV4YW1wbGU6MTIzNDU2Nzg5YWJjZGVmZ2hpI2tleS0xIl19fQ.SeyJ0IjoiZG0iLCJzIjp7InVyaSI6Imh0dHA6Ly9leGFtcGxlLmNvbS9hbm90aGVyIiwiYSI6WyJkaWRjb21tL3YyIl0sInIiOlsiZGlkOmV4YW1wbGU6MTIzNDU2Nzg5YWJjZGVmZ2hpI2tleS0yIl19fQ`

## Requirements

1.  A `did:scid` DID MUST be a self-certifying identifier (SCID) that is fully cryptographically verifiable using only the verification metadata (which may be any combination of DID documents and other verification files).

2.  A `did:scid` DID MUST be able to serve as a permanent immutable account identifier, i.e., it must not need to change if the DID controller changes the location of the verification metadata.

3.  A `did:scid` DID MUST be fully portable, i.e., the DID controller must be able to:

    1.  Choose the location(s) for the verifiable history (subject to policy constraints that may be imposed by a relying party).

    2.  Continue using the `did:scid` DID even if the location of the verification metadata changes.

4.  A `did:scid` DID MUST be able to support storing the verification metadata in multiple locations (“multi-anchoring”).

5.  A `did:scid` DID MUST meet all the other requirements of a DID as specified in [*W3C Decentralized Identifiers (DIDs) 1.0*](https://www.w3.org/TR/did-core/).

## Syntax

*This section is normative.*

The design of `did:scid` separates the SCID from the verification metadata location. The DID is a “pure SCID” that will never need to change. The verification metadata location is conveyed in a standard query parameter of a `did:scid` URL.

### did:scid

A `did:scid` consists of five colon-delimited segments as summarized in table 1:

| \# | Segment | Purpose |
|----|----|----|
| 1 | did | URI scheme as required by [*W3C DID 1.0*](https://www.w3.org/TR/did-core/) |
| 2 | scid | DID method name |
| 3 | format-name | Short name identifying the format |
| 4 | ver | Format version |
| 5 | method-specific-id | SCID value |

A `did:scid` MUST conform to the following ABNF (most of which is identical to [*section 3.1*](https://www.w3.org/TR/did-core/#did-syntax) of W3C Decentralized Identifiers 1.0):

```abnf
did-scid           = "did:scid:" format-name ":" ver ":" method-specific-id
format-name        = 1*format-char
format-char        = %x61-7A / DIGIT
ver                = 1*DIGIT
method-specific-id = *( *idchar ":" ) 1*idchar
idchar             = ALPHA / DIGIT / "." / "-" / "_" / pct-encoded
pct-encoded        = "%" HEXDIG HEXDIG
```

The value of the `method-specific-id` component MUST be a self-certifying identifier whose binding to the verification metadata is cryptographically verifiable using only the verification metadata.

### did:scid URL

A `did:scid` URL MUST conform to the following ABNF:

```abnf
did-scid-URL = did-scid path-abempty [ "?" [ "src=" source ] query ]
               [ "#" fragment ]
source = 1*( pchar / "/" / "?" )
```

Any ABNF rules that are not defined above are defined in [*RFC 3986*](https://datatracker.ietf.org/doc/html/rfc3986).

With this ABNF, the only difference between a `did:scid` URL and a standard DID URL is that a `did:scid` URL takes an optional query parameter called `src` (for “source”).

## The src (“source”) parameter

*This section is normative.*

The purpose of the src parameter is to supply the location information a DID resolver needs to locate the verification metadata. There are two options for the value of this parameter.

### Absolute or relative URL

The first option is a URL, which may be either absolute or relative.

1.  If a URL is used, a relative URL is RECOMMENDED.

2.  If a relative URL is used:

    1.  The base URL MUST be assumed to be `https://`

    2.  It is NOT RECOMMENDED for the relative URL to include its own query or fragment component.

Examples:

`did:scid:vh:1:f3ad7beb1abc4a26b892466df4379a51?src=my.name.me`

`did:scid:vh:1:f3ad7beb1abc4a26b892466df4379a51?src=example.com/allan`

`did:scid:vh:1:f3ad7beb1abc4a26b892466df4379a51?src=did.company.info/employee/abc`

### DID method

The second option is to use a DID method. If this option is used, the specification for the target DID method MUST support storage and lookup/retrieval of the verification metadata for a `did:scid`.

This example assumes a hypothetical modified version of the [cheqd DID method](https://docs.cheqd.io/product/architecture/adr-list/adr-001-cheqd-did-method) (discussed [here](https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/131858449/DID+SCID+Resolution+Overview#Suboption-%232%3A-Algorithmic-DID-Linked-Resources-URL) on the DID SCID Resolution Overview page) that specifies: 1) using the `did:scid` DID as the `unique-id` component of a `did:cheqd` DID as specified in [the Syntax section of the did:cheqd method](https://docs.cheqd.io/product/architecture/adr-list/adr-001-cheqd-did-method#syntax-for-did-cheqd-method), and 2) how to retrieve the `did:scid` DID verification metadata from the [cheqd](https://cheqd.io/) blockchain:

`did:scid:vh:1:f3ad7beb1abc4a26b892466df4379a51?src=did:cheqd:testnet`

## alsoKnownAs property

*This section is normative.*

::: warning
IMPORTANT: The requirements in this section apply only to values of the `alsoKnownAs` property in a DID document that contain the same `did:scid` DID as the DID document itself. These requirements DO NOT apply to any other values of the `alsoKnownAs` property.
:::

For a `did:scid` method, the `alsoKnownAs` property of a DID document as specified in [*W3C Decentralized Identifiers 1.0*](https://www.w3.org/TR/did-core/) is a means for discovering other locations of the verification metadata.

1.  The `alsoKnownAs` property of a DID document for a `did:scid` DID SHOULD include one entry for each `did:scid` DID URL that identifies a different location for the verification metadata.

2.  For each such `did:scid` URL, the verification metadata at the location identified by the `src` parameter value of that `did:scid` URL has the following requirements:

    1.  The values of all versions of the verification metadata that are present in all locations MUST be equivalent.

    2.  One or more of the locations MAY have a newer version of the verification metadata than the other locations.

The latter requirement avoids a race condition for the DID controller when synchronizing updates to the verification metadata for a `did:scid` DID.

## did:scid formats

*This section is normative.*

Like the `did:` scheme defined in [*W3C Decentralized Identifiers (DIDs) 1.0*](https://www.w3.org/TR/did-core/), `did:scid` is a scheme for `did:scid` formats. As with DID methods, there is no limit to the number of `did:scid` formats. However, authors SHOULD NOT create a new `did:scid` format if:

1.  A current `did:scid` format provides all required functionality, OR

2.  A revision to a current `did:scid` format could add the required new functionality.

### did:scid format specifications

1.  A `did:scid` format MUST have a written specification, either published independently or as an Appendix to this specification.

2.  The `did:scid` format specification MUST meet all the requirements in:

    1.  This specification.

    2.  [*W3C Decentralized Identifiers (DIDs) 1.0*](https://www.w3.org/TR/did-core/).

3.  The `did:scid` format specification SHOULD be available via a canonical URL.

### did:scid format names

::: note
The recommendations in this section are based on the rationale that **there is no good reason to have a large number of** `did:scid` **formats**. All SCID-based DID methods work basically the same way. Since cryptographic agility can be supported by versioning an existing `did:scid` format, it is in the best interests of everyone to have **a small number of very widely supported** `did:scid` **formats**.
:::

`did:scid` method type names have the same ABNF as DID method names.

1.  It is RECOMMENDED to keep `did:scid` format names as short as possible.

2.  By convention, `did:scid` format names SHOULD consist of exactly two characters.

3.  This specification SHALL maintain a registry of `did:scid` format names in Appendix A.

### did:scid format version identifiers

Although [*W3C Decentralized Identifiers (DIDs) 1.0*](https://www.w3.org/TR/did-core/) does not require DID methods to have version identifiers, practical usage has shown they can be quite valuable, especially to support [cryptographic agility](https://en.wikipedia.org/wiki/Cryptographic_agility) as newer and better cryptographic algorithms are developed, especially for [post-quantum cryptography](https://en.wikipedia.org/wiki/Post-quantum_cryptography) (PQC). In any case, for a `did:scid` format, a single digit version identifier plus the colon delimiter adds only two characters to the DID.

1.  A `did:scid` format MUST have a version number.

2.  The version number MUST be one or more digits.

3.  Version numbers MUST increment.

## Security Considerations

{{REQUIRED for all ToIP specifications. This section SHOULD be formatted as either a numbered list or numbered subsections. [See this IETF guidance](https://www.ietf.org/archive/id/draft-flanagan-7322bis-07.html#name-security-considerations-sec) and also the Security Considerations sections in [DID Core](https://www.w3.org/TR/did-1.0/#security-considerations) and [W3C VC](https://www.w3.org/TR/vc-data-model/#security-considerations).}}

## Privacy Considerations

{{REQUIRED for all ToIP specifications. This section SHOULD be formatted as either a numbered list or numbered subsections. [See this IETF guidance](https://datatracker.ietf.org/doc/html/rfc6973) and also the Privacy Considerations sections in [DID Core](https://www.w3.org/TR/did-1.0/#privacy-considerations) and [W3C VC](https://www.w3.org/TR/vc-data-model/#privacy-considerations).}}

## Governance Considerations

{{RECOMMENDED for most ToIP specifications. This section SHOULD be formatted as either a numbered list or numbered subsections. This section SHOULD include any relevant references to the [ToIP governance metamodel](https://trustoverip.org/wp-content/uploads/ToIP-Governance-Metamodel-Specification-V1.0-2021-12-21.pdf). [See also this IETF guidance](https://www.ietf.org/archive/id/draft-attoumani-ietf-inclusion-00.html).}}

## Internationalization Considerations

{{RECOMMENDED for most ToIP specifications. This section SHOULD be formatted as either a numbered list or numbered subsections. [See this IETF guidance](https://www.ietf.org/archive/id/draft-flanagan-7322bis-07.html#name-internationalization-consid).}}

## Accessibility Considerations

{{RECOMMENDED for most ToIP specifications. This section SHOULD be formatted as either a numbered list or numbered subsections. [See this IETF guidance](https://datatracker.ietf.org/doc/html/rfc6973) and also the Accessibility Considerations section in [W3C VC](https://www.w3.org/TR/vc-data-model/#accessibility-considerations).}}

## Conformance

{{REQUIRED. This section should state what normative requirements (expressed using “MUST” or “SHALL” keywords) apply to which conformance targets. See the [Guidelines to Writing Conformance Clauses](https://docs.oasis-open.org/templates/TCHandbook/ConformanceGuidelines.html) from OASIS Open.}}

### Conformance Targets

### Conformance Tests

## References

{{Required. See the [IETF guidance](https://www.ietf.org/archive/id/draft-flanagan-7322bis-07.html#name-references-section) on how to add normative references and informative references.}}

### Normative References

### Informative References
