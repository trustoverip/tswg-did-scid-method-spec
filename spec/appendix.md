## Appendices

### Appendix A: did:scid Method Type Registry

::: todo
Add `did:scid` method types (which of course need their own specs). Proposed entries:

1.  `did:scid:ke` — uses the `did:webs` verification metadata format.

2.  `did:scid:vh` — uses the `did:webvh` verification metadata format.

3.  `did:scid:jl` — uses `did:jlinc` verification metadata format.

NOTE: `did:peer` is not included in this list for the reasons explained above—all of these `did:scid` method types can be used peer-to-peer and all offer more features and security than `did:peer`.
:::

### Appendix B: Bluesky DID Requirements

In December 2024, Bryan Newbold of [*Bluesky*](https://bsky.app/) sent the DIF DID Method Standardization WG the following list of requirements of DIDs for Bluesky account holders:

1.  Low and predictable marginal cost at scale (millions of accounts): as an example, SMS verification of user accounts can be prohibitively expensive for service providers, even when costing well under a US dollar per account

2.  Ability to create and update identifiers rapidly (within seconds, ideally under a second)

3.  Key rotation is a must

4.  Reliable and predictable-latency operation, both for updating identifiers and resolving them (the "tail latency" of resolution is important)

5.  Identifiers are long-lived and continue to function after periods of inactivity

6.  Resolution should not require additional state or context, and "new" resolvers can independently resolve all "old" identifiers

7.  DIDs are permanent and immutable account identifiers

Bryan’s email concluded:

*The last two requirements make most forms of "DID migration" very difficult to implement with AT Protocol. It is not feasible to re-write DIDs, because they are included in URIs which get included in content-addressed data records. Keeping track of aliases or DID history out of band would mean that a newly-instantiated client or service would be missing history, and would not be able to successfully resolve the same DIDs as an established service. We took it as an invariant that account identity is one-to-one with a permanent DID string.*

### Appendix C: DID Hosts

Because it is designed to be fully portable, a `did:scid` DID can have its verification metadata written to any number of target locations (“multi-anchoring”).

::: warning
**IMPORTANT: When the verification metadata of a** `did:scid` **DID is written to multiple locations, this may raise data synchronization challenges.** Responsibility for those challenges lies with each `did:scid` method type specification and/or implementation.
:::

Because location independence is a key feature of the did:scid method, we need a location-independent term for the different types of data repositories that could store verification metadata and respond to did:scid DID resolution requests, i.e., web servers, blockchains, DHTs, trust registries, etc. We propose to call this a **DID host**. 

With `did:scid`, Bluesky’s requirements could be met if Bluesky implemented this policy:

*Any* `did:scid` *DID used by Bluesky MUST have its verification metadata published to **at least one** designated Bluesky DID host (for example “did.bsky.social”).*

In other words, a `did:scid` DID can still be 100% portable—the owner can move it anywhere they want any time they want and keep their full verification metadata—BUT if they want to use it with Bluesky, the owner must agree to let Bluesky host at least one copy of their verification metadata on Bluesky’s DID host.

That way Bluesky can have its cake and eat it too. A `did:scid` DID can meet both of these requirements:

1.  DIDs are permanent and immutable account identifiers.

2.  Resolution should not require additional state or context, and "new" resolvers can independently resolve all "old" identifiers.

For Bluesky, there would always be a known context—the verification metadata for the `did:scid` DID on their own DID host. Even better, Bluesky can completely control the performance, redundancy, scalability, etc. of that resolution option. At the same time, the `did:scid` DID controller still has a fully portable DID they can take to any other DID host anywhere anytime.

This policy also works for any service provider who shares Bluesky’s requirements. In other words, if a service provider wants to be able to “always resolve” a `did:scid` DID regardless of what other DID hosts the DID controller currently uses, the site can make it part of their terms-of-service that the DID controller must publish their verification metadata to that service provider’s designated DID host.

::: note
This policy also enables service providers to avoid duplicity issues. The service provider's own DID host will always be authoritative for the verification metadata for the `did:scid` DID upon which they are relying. If the DID controller writes conflicting update records to that DID host, the service provider can immediately trigger revocation of that `did:scid` DID.
:::

### Appendix D: Blockchain support using did:cheqd

One of the benefits of `did:scid` is that the verification metadata may be stored on multiple different types of technical infrastructure, including blockchains. A blockchain is an excellent tool for storing the verification metadata due to its immutable nature, tamper-resistance and high availability. 

As such, this section shows how to use the [*cheqd*](https://cheqd.io/) blockchain as a **DID host**, based on the description in [Appendix C](#appendix-c%3A-did-hosts). 

Through the use of cheqd’s [*DID-Linked Resource*](https://w3c-ccg.github.io/DID-Linked-Resources/) functionality, cheqd is able to store **sequentially ordered, indexable and historically queryable JSON files** that are both cryptographically verifiable and tamper-resistant. 

Using this approach, a `did:scid` DID can point to the initial verifiable history log on cheqd.

1.  The SCID and verification metadata are generated using the same process as defined in the did:webvh or KERI AID specifications

2.  Once generated, the initial verifiable history JSON file is published as a DID-Linked Resource on cheqd. For example:

    1.  did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692?resourceName=didScidExample&resourceType=didScidLogEntry&resourceVersionTime=2025-01-23T07:17:26Z

    2.  (Using cheqd DID Resolver) [*https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692?resourceName=didScidExample&resourceType=didScidLogEntry&resourceVersionTime=2025-01-23T07:17:26Z*](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692?resourceName=didScidExample&resourceType=didScidLogEntry&resourceVersionTime=2025-01-23T07:17:26Z)

    3.  (using DIF Universal Resolver) <https://dev.uniresolver.io/1.0/identifiers/did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692?resourceName=didScidExample&resourceType=didScidLogEntry&resourceVersionTime=2025-01-23T07:17:26Z>

3.  When publishing the verification metadata JSON file on cheqd, the controller MUST also publish the following metadata to enable the file to be queried:

    1.  resourceName: Any arbitrary string, naming the file

    2.  resourceType: MUST be `didScidLogEntry`

4.  The controller MAY also publish the following optional metadata:

    1.  alternativeUri: A string or array specifying alternative locations where the initial verification metadata log is ***also*** published

    2.  resourceVersion: To enable more granular querying by a specific version name or number

5.  The DID-Linked Resource SHOULD be signed using the same keys specified in the “`authentication`” section of the DID Document, ensuring cryptographic verifiability back to the controller of the DID Document.

6.  Using this persistent identifier for the initial verification metadata, a `did:scid` DID can be derived. For example:

    1.  did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP?src=did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692

    2.  Including a portable `did:scid` DID which can be migrated to different infrastructure:

        1.  did:scid:ke:3:EKYGGh-FtAphGmSZbsuBs_t4qpsjYJ2ZqvMKluq9OxmP

While the verification metadata in this particular DID example is stored on cheqd, owing to the nature of `did:scid`, the DID Documents and associated verification metadata can be stored and updated elsewhere, such as on a web server. This is because the SCID can always be decoded by a relying party to dereference back to the initial verification metadata on cheqd. 

Additionally, a relying party will be able to check any updates to the verification metadata as cheqd provides the functionality of a “microledger” for resource versions. See below for how cheqd would work as a ledger of verification metadata updates:

[*https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692?resourceName=didScidExample&resourceType=didScidLogEntry&resourceMetadata=true*](https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:9cdea85b-7524-4962-ad11-c943b8303692?resourceName=didScidExample&resourceType=didScidLogEntry&resourceMetadata=true)

### Appendix E: Acknowledgements

{{The final appendix should contain any additional acknowledgements}}

Copyright © {{Year}} Trust Over IP (ToIP) Contributors  
This work is licensed under a Creative Commons Attribution 4.0 International License.
