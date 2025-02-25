# Vault & Proof Identifiers

Zero-knowledge proofs (ZKPs) are malleable; from two valid ZKPs, it is impossible to know whether they were generated from the same secret inputs. This means that a user can create an infinite number of valid ZKPs—posing challenges for Sybil-resistance. The question of how to limit an individual user to one valid ZKP then arises.

As a result, zero-knowledge protocols such as Tornado Cash or Semaphore use a nullifier to prevent certain effects from occurring more than once (i.e, double spends). The nullifier is typically a unique ID inherited from the secret a user provides during ZK proving schemes. ZKPs allow users to prove this connection without revealing how it is true.

Sismo uses the notion of a Vault Identifier or Proof Identifier to ensure the associated user has not already accessed certain features. These identifiers are used by applications to substantiate a user or proof’s origin in a privacy-preserving manner. This prevents users from minting duplicate [Badges](../what-is-sismo/sismo-badges.md) or accessing gated features via [zkConnect](../what-is-sismo/zkconnect.md) multiple times when unintended.

## Vault Identifier

Sismo revolves around personal data aggregated in a user’s [Data Vault](../what-is-sismo/data-vault.md). Users can leverage data stored in their Vault via [zkConnect](../what-is-sismo/zkconnect.md), a privacy-preserving single sign-on (SSO) method. This is made possible with a Vault Identifier—an anonymous app-specific identifier that can be utilized as an in-app user ID.

A Vault Identifier is a unique deterministic number that can only be computed by the associated Vault. The uniqueness of a given Vault Identifier is ensured by its generation method. It is generated by hashing (Poseidon) the following public inputs in a zk-SNARK circuit run entirely in the user’s browser:

* The `VaultSecret` generated when adding a Data Source to the Data Vault
* The `appId` of the application requesting proof

Due to the one-way nature of hashing, it’s impossible to reverse-engineer a Vault Identifier and link it to the associated Vault. Furthermore, the entire set of Vault Identifiers is downloaded in a user’s browser to prevent potential correlation. A given Vault Identifier can only be generated by the owner of the associated Vault.

## Proof Identifiers

Alternatively, developers can opt to accept Proof Identifiers from their users instead of Vault Identifiers. A Proof Identifier limits services to a single proof instead of a single Vault—allowing applications to have multiple services without tracking their users. It is generated from a combination of public and secret data.

The `requestIdentifier` corresponds to a user’s request to prove group membership to a specific application. It is obtained by hashing (keccak256) the following public inputs:

* The `appId` of the application requesting proof
* The `Namespace` of the application’s specific service (optional)
* The `groupId` of the group the user is proving membership in
* The `groupTimestamp` of the group the user is proving membership in

The `groupId` and `groupTimestamp` form the group snapshot—the unique identifier of the group the user is proving membership in.

A Proof Identifier is subsequently created by hashing (Poseidon) the following inputs inside a zk-SNARK circuit run entirely in the user’s browser:

* The previously generated requestIdentifier
* A user’s secret associated with the sourceAccount (private key or commitment)

{% hint style="info" %}
The [Commitment Mapper](commitment-mapper.md) is an off-chain service that privately associates a unique commitment with a source account—ensuring users have a unique proofIdentifier derived from their address.
{% endhint %}

## Verified ZKPs

When participating in proving schemes, users generate ZKPs to prove ownership of data that categorizes them into specific groups. Applications can use [zkConnect](../what-is-sismo/zkconnect.md) to verify ZKPs generated to prove ownership of accounts in a user’s [Data Vault](../what-is-sismo/data-vault.md).

ZKPs are verified after users provide them to Sismo’s verifier, which authenticates user proofs in a decentralized manner. The verifier can be integrated into on-chain smart contracts or off-chain applications. After verifying ZKPs provided by users, the verifier outputs a Vault or Proof Identifier, depending on the application's preferences. These identifiers can be used to control access to the application in question and are stored either in on-chain smart contracts or in off-chain databases.

## Leveraging Vault & Proof Identifiers

After storing the relevant identifier, applications can decide how to use it. Vault and Proof Identifiers can be leveraged by applications in the following ways:

* Nullify source accounts
* Restrict source accounts

Nullifying a source account utilizes the identifier to ensure source accounts can not be used more than once, e.g., to mint duplicate Badges. Restricting a source account puts a limit on the number of times a source account can access a gated feature, e.g., users could claim a ticket for an event twice to bring an extra person.

As applications store the identifier, they can choose how many times a specific ZKP can be used. If a Vault or Proof Identifier exists in an application’s database (on-chain or off-chain), users can not access specific features more times than intended.
