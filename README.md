**Designation ERC721-201801**
# Standard Interface for Nonfungible Tokens
*Active Standard [ERC721](https://github.com/ethereum/EIPs/issues/721)*

This standard is issued under the fixed designation ERC721; the number immediately following the designation indicates the year and month of original adoption or, in the case of revision, the year and month of last revision. This aims to resolve the problem of creating a new standard for a revision of a previous standard, as seen with [ERC20](https://github.com/ethereum/EIPs/issues/20) and [ERC223](https://github.com/ethereum/EIPs/issues/223). When this standard is finalized, it shall be written as ERC721, but further revisions following its finilization that have the same function but address specific bugs shall contain the core name ERC721, but have an additional year month suffix (ie. ERC721-201911 for a proposal written in November 2019). All future revisions that take the ERC721 name must be backwards compatible with the ERC721 finalized core standard. 

## In this standard:

Section 1 Scope

Section 2 Referenced Documents

Section 3 Terminology

Section 4 Significance and Use

Section 5 Specification

Section 6 Rationale

Section 7 Backwards Compatibility 

Section 8 Implementation 


## 1 | Scope


These specifications cover the standard interface for creating nonfungible tokens. 


## 2 | Referenced Documents 

ERC Standards:

**[ERC20](https://github.com/ethereum/EIPs/issues/20)** Token standard

**[ERC223](https://github.com/ethereum/EIPs/issues/223)** Token standard

**[ERC721](https://github.com/ethereum/EIPs/issues/721)** Standard interface for nonfungible tokens


## 3 | Terminology

Definitions: 

Fungible -- Freely exchangeable or replaceable, in whole or part, for another of kind. Each of its kind has equal value. 

Nonfungible -- Not equally exchangable or replacable, in whole or part, for another of kind. Each of its kind has unique value. 



## 4 | Significance and Use
 
The [ERC20](https://github.com/ethereum/EIPs/issues/20) token standard for fungible tokens allowed the Cryptotoken to come into existence, and thus allow people to make blockchain app tokens that could be resold on exchanges without further generation. With the [ERC721](https://github.com/ethereum/EIPs/issues/721), the following, but not limited to, are possible:

* [ERC721/Cryptocollectibles] - The first key use case of the ERC721 token was cryptocollectibles, such as trading cards, seen with CryptoKittens.
* [ERC721/Marketplace] - Exchanges function for [ERC20](https://github.com/ethereum/EIPs/issues/20) tokens as the price of one token in a single brand has a designated value determined by supply and demand, designating that value to every token in that brand.  But with ERC721, each token has a unique market value, requiring more thought to buy and sell, needing auctions or buy it now reserve prices, much like on eBay. A Marketplaces instead of exchanges would be the platform.
* [ERC721/Property Deeds] - Real property deeds, both virtual property enforced with DRM, and property enforced by government or organizations.
* [ERC721/Tracking] - RFID/QR Code tracking for items in a supply chain.

## 5 | Specification

### 5.1 ERC20 Compatibility

#### 5.1.1 name

```function name() constant returns (string name)```

*It is recommend but not required that this method be implemented for enhanced usability with wallets and exchanges, but interfaces and other contracts MUST NOT depend on the existence of this method.*

Returns the name of the collection of NFTs managed by this contract. - e.g. `"My Non-Fungibles"`.

#### 5.1.2 symbol

```function symbol() constant returns (string symbol)```

*It is recommend but nor required that that this method be implemented for enhanced usability with wallets and exchanges, but interfaces and other contracts MUST NOT depend on the existence of this method.*

Returns a short string symbol referencing the entire collection of NFTs managed in this contract. e.g. "MNFT". This symbol SHOULD be short (3-8 characters recommended), with no whitespace characters or new-lines and SHOULD be limited to the uppercase latin alphabet (i.e. the 26 letters used in English).

#### 5.1.3 totalSupply

```function totalSupply() constant returns (uint256 totalSupply)```

Returns the total number of NFTs currently tracked by this contract.

#### 5.1.4 balanceOf

```function balanceOf(address _owner) constant returns (uint256 balance)```

Returns the number of NFTs assigned to address `_owner`.

### 5.2 Basic Ownership

#### 5.2.1 ownerOf

```function ownerOf(uint256 _tokenId) constant returns (address owner)```

Returns the address currently marked as the owner of `_tokenID`. This method MUST `throw` if `_tokenID` does not represent an NFT currently tracked by this contract. This method MUST NOT return 0 (NFTs assigned to the zero address are considered destroyed, and queries about them should throw).

#### 5.2.2 approve

```function approve(address _to, uint256 _tokenId)```

Grants approval for address `_to` to take possession of the NFT with ID `_tokenId`. This method MUST `throw` if `msg.sender != ownerOf(_tokenId)`, or `if _tokenID` does not represent an NFT currently tracked by this contract, or `if msg.sender == _to`.

Only one address can "have approval" at any given time; calling `approveTransfer` with a new address revokes approval for the previous address. Calling this method with 0 as the `_to` argument clears approval for any address.

Successful completion of this method MUST emit an `Approval` event (defined below) unless the caller is attempting to clear approval when there is no pending approval. In particular, an Approval event MUST be fired if the `_to` address is zero and there is some outstanding approval. Additionally, an Approval event MUST be fired if `_to` is already the currently approved address and this call otherwise has no effect. (i.e. An `approve()` call that "reaffirms" an existing approval MUST fire an event.)


Action |	Prior State |	_to address |	New State |	Event
-------|--------------|-------------|-----------|------
Clear unset approval |	Clear |	0 |	Clear |	None
Set new approval |	Clear |	X |	Set to X |	Approval(owner, X, tokenID)
Change approval |	Set to X |	Y |	Set to Y |	Approval(owner, Y, tokenID)
Reaffirm approval |	Set to X |	X |	Set to X |	Approval(owner, X, tokenID)
Clear approval |	Set to X |	0 |	Clear |	Approval(owner, 0, tokenID)

Note: ANY change of ownership of an NFT – whether directly through the `transfer` and `transferFrom` methods defined in this interface, or through any other mechanism defined in the conforming contract – MUST clear any and all approvals for the transferred NFT. The implicit clearing of approval via ownership transfer MUST also fire the event `Approval(0, _tokenId)` if there was an outstanding approval. (i.e. All actions that transfer ownership must emit the same Approval event, if any, as would emitted by calling `approve(0, _tokenID)`.)

#### 5.2.3 takeOwnership

```function takeOwnership(uint256 _tokenId)```

Assigns the ownership of the NFT with ID `_tokenId` to `msg.sender` if and only if msg.sender currently has approval (via a previous call to `approveTransfer`). A successful transfer MUST fire the `Transfer` event (defined below).

This method MUST transfer ownership to `msg.sender` or `throw`, no other outcomes can be possible. Reasons for failure include (but are not limited to):

* `msg.sender` does not have approval for `_tokenId`
* `_tokenID` does not represent an NFT currently tracked by this contract
* `msg.sender` already has ownership of `_tokenId`

Important: Please refer to the Note in the `approveTransfer` method description; a successful transfer MUST clear pending approval.


#### 5.2.4 transfer

```function transfer(address _to, uint256 _tokenId)```

Assigns the ownership of the NFT with ID `_tokenId` to `_to` if and only if `msg.sender == ownerOf(_tokenId)`. A successful transfer MUST fire the Transfer event (defined below).

This method MUST transfer ownership to `_to` or `throw`, no other outcomes can be possible. Reasons for failure include (but are not limited to):

* `msg.sender` is not the owner of `_tokenId`
* `_tokenID` does not represent an NFT currently tracked by this contract
* `_to` is 0 (Conforming contracts MAY have other methods to destroy or burn NFTs, which are conceptually "transfers to 0" and will emit `Transfer` events reflecting this. However, `transfer(0, tokenID)` MUST be treated as an error.)

A conforming contract MUST allow the current owner to "transfer" a token to themselves, as a way of affirming ownership in the event stream. (i.e. it is valid for `_to == ownerOf(_tokenID)`.) This "no-op transfer" MUST be considered a successful transfer, and therefore MUST fire a `Transfer` event (with the same address for `_from` and `_to`).

Important: Please refer to the Note in the `approveTransfer` method description; a successful transfer MUST clear pending approval. This includes no-op transfers to the current owner!

#### 5.2.5 tokenOfOwnerByIndex

```function tokenOfOwnerByIndex(address _owner, uint256 _index) constant returns (uint tokenId)```

*OPTIONAL - It is recommend that this method is implemented for enhanced usability with wallets and exchanges, but interfaces and other contracts MUST NOT depend on the existence of this method.*

Returns the nth NFT assigned to the address `_owner`, with n specified by the `_index` argument. This method MUST throw if `_index >= balanceOf(_owner)`.

Recommended usage is as follows:

```
uint256 ownerBalance = nonFungibleContract.balanceOf(owner);

uint256[] memory ownerTokens = new uint256[](ownerBalance);

for (uint256 i = 0; i < ownerBalance; i++) {
    ownerTokens[i] = nonFungibleContract.tokenOfOwnerByIndex(owner, i);
}
```

Implementations MUST NOT assume that NFTs are accessed in any particular order by their callers (In particular, don't assume this method is called in a monotonically ascending loop.), and MUST ensure that calls to `tokenOfOwnerByIndex` are fully idempotent unless and until some non-`constant` function is called on this contract.

Callers of `tokenOfOwnerByIndex` MUST never assume that the order of NFTs is maintained outside of a single operation, or through the invocation (direct or indirect) of any non-`constant` contract method.

NOTE: Current limitations in Solidity mean that there is no efficient way to return a complete list of an address's NFTs with a single function call. Callers should not assume this method is implemented efficiently (from a gas standpoint) and should *strenuously avoid* calling this method "on-chain" (i.e. from any non-constant contract function, or from any `constant` contract function that is likely to be called on-chain).


### 5.3 NFT Metadata

#### 5.3.1 tokenMetadata

```function tokenMetadata(uint256 _tokenId) constant returns (string infoUrl)```

*OPTIONAL - It is recommend that this method is implemented for enhanced usability with wallets and exchanges, but interfaces and other contracts MUST NOT depend on the existence of this method.*

Returns a multiaddress string referencing an external resource bundle that contains (optionally localized) metadata about the NFT associated with `_tokenId`. The string MUST be an IPFS or HTTP(S) base path (without a trailing slash) to which specific subpaths are obtained through concatenation. (IPFS is the preferred format due to better scalability, persistence, and immutability.)

Standard sub-paths:
* `name` (required) - The `name` sub-path MUST contain the UTF-8 encoded name of the specific NFT (i.e. distinct from the name of the collection, as returned by the contract's `name` method). A name SHOULD be 50 characters or less, and unique amongst all NFTs tracked by this contract. A name MAY contain white space characters, but MUST NOT include new-line or carriage-return characters. A name MAY include a numeric component to differentiate from similar NFTs in the same contract. For example: "Happy Token #157".
* `image` (optional) - If the `image` sub-path exists, it MUST contain a PNG, JPEG, or SVG image with at least 300 pixels of detail in each dimension. The image aspect ratio SHOULD be between 16:9 (landscape mode) and 2:3 (portrait mode). The image SHOULD be structured with a "safe zone" such that cropping the image to a maximal, central square doesn't remove any critical information. (The easiest way to meet this requirement is simply to use a 1:1 image aspect ratio.)
* `description` (optional) - If the `description` sub-path exists, it MUST contain a UTF-8 encoded textual description of the asset. This description MAY contain multiple lines and SHOULD use a single new-line character to delimit explicit line-breaks, and two new-line characters to delimit paragraphs. The description MAY include CommonMark-compatible Markdown annotations for styling. The description SHOULD be 1500 characters or less.
* other metadata (optional) - A contract MAY choose to include any number of additional subpaths, where they are deemed useful. There may be future formal and informal standards for additional metadata fields independent of this standard.

Each metadata subpath (including subpaths not defined in this standard) MUST contain a sub-path default leading to a file containing the `default` (i.e. unlocalized) version of the data for that metadata element. For example, an NFT with the metadata path `/ipfs/QmZU8bKEG8fhcQwKoLHfjtJoKBzvUT5LFR3f8dEz86WdVe` MUST contain the NFT's name as a UTF-8 encoded string available at the full path `/ipfs/QmZU8bKEG8fhcQwKoLHfjtJoKBzvUT5LFR3f8dEz86WdVe/name/default`. Additionally, each metadata subpath MAY have one or more localizations at a subpath of an ISO 639-1 language code (the same language codes used for HTML). For example, `/ipfs/QmZU8bKEG8fhcQwKoLHfjtJoKBzvUT5LFR3f8dEz86WdVe/name/en` would have the name in English, and `/ipfs/QmZU8bKEG8fhcQwKoLHfjtJoKBzvUT5LFR3f8dEz86WdVe/name/fr` would have the name in French (note that even localized values need to have a default entry). Consumers of NFT metadata SHOULD look for a localized value before falling back to the default value. Consumers MUST NOT assume that all metadata subpaths for a particular NFT are localized similarly. For example, it will be common for the name and `image` objects to not be localized even when the description is.

You can explore the metadata package referenced in this example [here](https://ipfs.io/ipfs/QmZU8bKEG8fhcQwKoLHfjtJoKBzvUT5LFR3f8dEz86WdVe).


### 5.4 Events

#### 5.4.1 Transfer

This event MUST trigger when NFT ownership is transferred via any mechanism.

Additionally, the creation of new NFTs MUST trigger a Transfer event for each newly created NFTs, with a `_from` address of 0 and a `_to` address matching the owner of the new NFT (possibly the smart contract itself). The deletion (or burn) of any NFT MUST trigger a Transfer event with a `_to` address of 0 and a `_from` address of the owner of the NFT (now former owner!).

NOTE: A Transfer event with `_from == _to` is valid. See the `transfer()` documentation for details.

```event Transfer(address indexed _from, address indexed _to, uint256 _tokenId)```

#### 5.4.2 Approval

This event MUST trigger on any successful call to `approve(address _spender, uint256 _value)` (unless the caller is attempting to clear approval when there is no pending approval).

See the documentation for the `approve()` method above for further detail.

```event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId)```


## 6 | Rationale
### 6.1 Utility

There are many proposed uses of Ethereum smart contracts that depend on tracking individual, non-fungible tokens (NFTs). Examples of existing or planned NFTs are LAND in Decentraland, the eponymous punks in CryptoPunks, and in-game items using systems like Dmarket or EnjinCoin. Future uses include tracking real-world non-fungible assets, like real-estate (as envisioned by companies like Ubitquity or Propy). It is critical in each of these cases that these items are not "lumped together" as numbers in a ledger, but instead, each token must have its ownership individually and atomically tracked. Regardless of the nature of these items, the ecosystem will be stronger if we have a standardized interface that allows for cross-functional non-fungible token management and sales platforms.

### 6.2 NTF IDs

The basis of this standard is that every NFT is identified by a unique, 256-bit unsigned integer within its tracking contract. This ID number MUST NOT change for the life of the contract. The pair (contract address, asset ID) will then be a globally unique and fully-qualified identifier for a specific NFT within the Ethereum ecosystem. While some contracts may find it convenient to start with ID 0 and simply increment by one for each new NFT, callers MUST NOT assume that ID numbers have any specific pattern to them, and should treat the ID as a "black box".

## 7 | Backwards Compatibility

This standard follows the semantics of ERC-20 as closely as possible, but can't be entirely compatible with it due to the fundamental differences between fungible and non-fungible tokens.

Example non-fungible implementations as of September 2017:

* [CryptoPunks](https://www.larvalabs.com/cryptopunks) - Partially ERC-20 compatible, but not easily generalizable because it includes auction functionality directly in the contract and uses function names that explicitly refer to the NFTs as "punks".
* [Auctionhouse Asset Interface](https://github.com/dob/auctionhouse/blob/master/contracts/Asset.sol) - [@dob](https://github.com/dob) needed a generic interface for his Auctionhouse dapp (currently ice-boxed). His "Asset" contract is very simple, but is missing ERC-20 compatibility, approve() functionality, and metadata. This effort is referenced in the discussion for EIP-173.

(It should be noted that "limited edition, collectable tokens" like [Curio Cards](https://mycuriocards.com/) and [Rare Pepe](https://rarepepewallet.com/) are not non-fungible tokens. They're actually a collection of individual fungible tokens, each of which is tracked by its own smart contract with its own total supply (which may be 1 in extreme cases).)


## 8 | Implementation
Reference implementation forthcoming...

**Copyright**

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).


