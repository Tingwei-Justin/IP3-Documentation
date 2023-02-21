---
description: This page describe the core data entity for IP3 marketplace protocol.
---

# Core data entity

The core data entity in IP3 protocol consists of two main parts. One is for the NFT holder to list their NFT IP and delegate IP3 protocol to authorize others to use it. The other is for users to purchase the listed NFT IP and obtain a license to use it.

### Listing IP

Any address can list any NFT IP on IP3 protocol for anyone use to earn the license fee. The listing IP are represented by `ListingIP` struct:

```solidity
/**
 * @notice This struct defines the authorized listing IP.
 *
 * @title ListingIP
 * @param nft The NFT associated with the IP listing.
 * @param licensor The IP licensor information including licensor address, claimer address and wallet delegation type
 * @param licensedInfos  An array of license information for each type for the IP.
 * @param paymentTokens The payment token address array accepted the licensor.
 * @param metadataUri The URI to be displayed for the listing IP (e.g. Terms and Conditions, instructions of use, etc.)
 * @param salt A random number or an identifier for the listing, one possible use case is it can be used to identify the IP listed from which dapp
 */
struct ListingIP {
    NFT nft;
    Licensor licensor;
    LicensedInfo[] licensedInfos;
    address[] paymentTokens;
    string metadataUri;
    uint256 salt;
}

```

### License

Every user who purchases an NFT IP will receive a license certificate to prove their right to use the IP, and the specific terms of use are stored in the `metadataUri`.

```solidity
/**
 * @notice This struct defines the data structure of an IP license.
 *
 * @title License
 * @param id The unique ID of the license.
 * @param nft The NFT associated with the license.
 * @param licensor The licensor of the license.
 * @param metadataUri The URI of the license's metadata.
 * @param licensee The address of the licensee who owns the license.
 * @param licenseType The type of license.
 * @param price The price of the license.
 * @param paymentToken The address of the payment token.
 */
struct License {
    uint256 id,
    NFT nft;
    Licensor licensor;
    string metadataUri;
    address licensee;
    LicenseType licenseType;
    uint256 price;
    address paymentToken;
}
```

### License Type

There are three types of license in IP3 protocol. The first type of license is time-based, where the price of the license increases with usage time. This type of license is suitable for display-based licenses. The second type of license is quantity-based, where the price of the license increases with usage quantity. This type of license is suitable for product-based licenses. The third type of license is joint license, which allows joint ownership of the IP with other parties and incurs corresponding joint ownership fees.

```solidity
enum LicenseType {
    Time,
    Quantity,
    Joint,
}
```

### Licensed Information

IP3 protocol will track separate price for each type of license. The price are calculated by smart price engine based on license history. The more times licensed, the more expensive the price. The protocol also make sure the license price will never below the minimum starting price defined by IP licensor.

```solidity
/**
 * @notice This struct defines the data structure of a licensed information.
 *
 * @title LicensedInfo
 * @param minStartPrice The minimum starting price for the license.
 * @param latestStartPrice  The latest starting price for the license.
 * @param lastActive  The timestamp of the last activity for the license.
 */
struct LicensedInfo {
    uint256 minStartPrice;
    uint256 latestStartPrice;
    uint256 lastActive; // last active timestamp
}
```

### Licensor

The licensor represent the user who license the NFT IP on IP3 protocol. The licensor can use the NFT owner's address or use the delegate address ([warm.xyz/](https://warm.xyz/) or [delegate.cash](https://delegate.cash/))

```solidity
/**
 * @notice This struct defines the data structure of an IP licensor.
 *
 * @title Licensor
 * @param licensorAddress The address of the IP licensor.
 * @param claimerAddress  The address of the claimer, who can claim royalties on behalf of the licensor.
 * @param walletDelegation  The wallet delegation of the IP licensor.
 */
struct Licensor {
    address licensorAddress;
    address claimerAddress;
    WalletDelegation walletDelegation;
}
```

### Wallet Delegation

To keep user vaulted NFTs safe, IP3 protocol support user to use delegate wallet to prove the ownership. Currently, we support [warm.xyz](https://warm.xyz/) and [delegate.cash](https://delegate.cash/). More delegation contract will be supported based on future voting result by IP3 Community

```solidity
enum WalletDelegation {
    None,
    WarmXYZ,
    DelegateCash
}
```

### NFT

```solidity
/**
 * @notice This struct defines the data structure of an NFT (Non-Fungible Token).
 *
 * @title NFT
 * @param chainId The NFT associated with the IP listing.
 * @param NFTAddress The address of the contract where the NFT is deployed.
 * @param tokenId  The unique identifier id of the NFT.
 */
struct NFT {
    string chainId;
    address NFTAddress;
    uint256 tokenId;
}
```



