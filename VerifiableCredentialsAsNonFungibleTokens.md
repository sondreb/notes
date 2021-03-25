# Verifiable Credentials (VC) As Non Fungible Tokens (NFT)

This document contains some notes and ideas regarding a possible architecture and implementation to use VCs as NFTs.

[Verifiable Credentials](https://www.w3.org/TR/vc-data-model/) is basically signed data that is often encoded as JWTs. They can encode any data in a document that is signed by the creator.

This document attempts to uncover a possible alternative to ERC-1155 tokens for Ethereum blockchain, an alternative that is as platform-agnostic as possible and can be implemented in many different traditional programming languages. The idea is to be as little dependent on blockchains as possible, but use it for value-transfer.

VCs are independent of any store, but could use a storage mechanism on networking nodes. The architecture for this smart contract alternative, is to allow for fully off-chain distribution of NFTs, to increase privacy.

The implementations of this could rely on Decentralized Identity (DID) for resolving metadata about the seller and purchaser, ensuring utilization of existing open standard and infrastructure for identities.

## User flow

### Publishing token

Apps can be built to allow users to create token(s), which are essentially structures of metadata ("JSON") that contains details about the token. Some standard fields (schema) should be used to identity certain elements that can be displayed on marketplace apps, etc.

Basic example of a VC document:

```
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "http://example.edu/credentials/3732",
  "type": ["VerifiableCredential", "UniversityDegreeCredential"],
  "issuer": "https://example.edu/issuers/14",
  "issuanceDate": "2010-01-01T19:23:24Z",
  "expirationDate": "2020-01-01T19:23:24Z",
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21",
    "degree": {
      "type": "BachelorDegree",
      "name": "Bachelor of Science and Arts"
    }
  },
  "proof": { 
  }
}
```

The app would allow users to specify range of numbers for retrieval of serialized images of the token, and then on behalf of the user, generate all of these unique documents and allow users to sign them.

After signing, the documents can be published to an REST API, or they could be sent in private to a receiver/potential buyer.

The REST API would need to verify the signature and public key of the owner, and other fields according to some standard schema, e.g. "InstanceCount".

The REST API should not allow anyone to PUT a modification to the document unless they are listed in the allowed keys section of the document.

To increase decentralization, the app could POST the same signed document to multiple known hosts of VC documents. In some cases it is highly likely that a single centralized authority will host, serve and receive the documents.

User apps / Wallets / Clients should always ensure to keep local backup and copy of all issued VCs, ensuring that network connectivity, data storage disasters or bad behavior of API hosts does not result in total data loss.

The VC document contains the payment address of which is used to redeem the NFT.

### Buying a token

When a user want to buy a token, they must send a transaction to the address specified in the VC document.

There are many possible ways of handling the transactions:

- OP_RETURN of public key of owner.
- The input public key could be assigned as the owner.
- Signed message sent by purchaser to the seller before transaction put on blockchain.

Transfer of NFTs can be done either seperately, meaning that a chain of transactions could be the way to trace current owner. This means that the origina VC would not need to be modified with new pub key (owner/editor/publisher) when purchased, but simply that the blockchain has the always up to date ownership based on the chain from the original payment-address until current state.

### Verifying API provider

There is obviously no way that the users of the marketplace can verify that the API provider is performing the checks normally required, e.g. not replace the document unless properly signed.

With tokens of less value, such as event tickets, this is not of a very big concern compared to existing solutions.

If large value items is sold on this platform, there is potential fraud that can happen by the marketplace API provider.

## Cost of tokens

This architecture does not cost anything for issuer of these NFTs, all it takes is an host that provides REST API and storage of VCs. VCs could even be stored on services such as IPFS.

If based on a standard UTXO-based blockchain, then costs will be the regular blockchain transaction fees, which in some cases might be too much. Alternative solutions such as second-layer or completely alternative to UTXO can also be used to handle the transfer of value between purchaser and seller.

## FLOW

1. Token Issuer App generates VCs
1. VCs posted to marketplace API
1. User signs message with intention to buy (e.g. auction-mode). This step is optional.
1. User pays for NFT using a transaction to receiver address.
1. User can at any time prove to any third parties that they are the rightful owner.
1. User want to resell an NFT they could publish a new VC to the marketplace API.