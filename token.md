# Native Token
## Introduction
*Native token* is a new feature that enables the transacting of multi-assets on Novo. Users can transact with novo, and an unlimited number of user-defined (custom) tokens natively.

Native support offers distinct advantages for developers: there is no need to create smart contracts to handle custom tokens, smart contracts can focus on implementing business functions, which removes a layer of added complexity and potential for manual errors since the ledger handles all token-related functionality. Transaction fees will also be lower when minting and using tokens.

Native tokens (customly created) represent some value and act as an accounting unit, which can be used for payments, transactions, and can be used with smart contracts.

We extend the structure of UTXO to make token equivalent to novo.

In each transaction output (TxOut), there is an optional contract section that records information related to the native token. The fields are as follows:

    ContractType       (uint64, Defined with: FT, NFT, FT_MINT, NFT_MINT)
    ContractID         (txid+vout 36 bytes, the outpoint when the contract is published)
    ContractValue      (uint256, the value of FT, the tokenID of NFT, and the total issued amount of MINT.)
    ContractMaxSupply  (uint256, the FT/NFT maximum circulation)
    ContractMetadata   (string n bytes, any data less than 1KB, initialized once when created, and then read-only)

- The newly added token data is optional and still compatible with the original structure. An output can contain no or only one type of token.
- After the contract is created, the ContractID is globally unique and is used to distinguish different tokens.
- When transferring tokens between addresses, transaction fees need to be paid with novo, and a minimum of 0.4368 novo (dust limit) is required in the token output.


## Using

### Example: Creating your own fungible token

Creating a fungible token using the node command tool is as easy as sending Novo to an address. Creating tokens requires novo for transaction fees.

First of all, we need to ensure that the wallet balance is sufficient, we can execute the following command to create a total of 21000000 FT, and simply mark it as TESTCOIN, the creator's address is `miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6`：

```
./novo-cli createtoken FT 21000000 '{"name":"TESTCOIN","symbol":"TC","decimal":8}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0
```

The unique identifier of the token is `c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0`. That is, contractID.

Tokens when initially created have no supply. Let's mint some. Mint 1000000 and 50 tokens into the address.

```
./novo-cli minttoken FT c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0 1000000 msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

./novo-cli minttoken FT c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0 50 miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
```

###  Example: Transferring tokens to another user

The receiver obtains their wallet address by running `novo-cli getnewaddress` and provides it to the sender.The sender then runs:

    $ ./novo-cli transfertoken FT c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0 100 msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

### Example: Create a non-fungible token

Using different types in the createtoken command, we can create NFTs. For example, create an NFT with a total circulation of 100, and simply mark it as TESTCARD, and the creator's address is `miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6`：

```
./novo-cli createtoken NFT 100 '{"name":"TESTCARD",description:""}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0
```

The unique identifier of the token is `d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0`.That is, contractID.

Non-fungible tokens when initially created have no supply. Let's mint one by one. Mint 3 tokens into the address, with different metadata D1/D2/D3:

```
./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"D1",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"D2",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx

./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"D3",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx
```

The auto-incremented number for NFT minting as its tokenID.

### Example: Transferring NFT to another user

When a certain NFT needs to be transferred, it needs to be specified through contractID and tokenID.

The receiver obtains their wallet address by running `novo-cli getnewaddress` and provides it to the sender.The sender then runs:

```
./novo-cli transfertoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 1 n1RZhxvsNRVXFgB1GKbE9XtscYUKMMcSGx
```

### Example: View all Tokens that you own

After receiving the token, you can use the `listcontractunspent` command to get the UTXO of all tokens.
```
./novo-cli listcontractunspent
[
  {
	"txid": "7e305ff908f3251967b5026ed38311bf513f86873368389f1f083dbfb0d8f601",
	"vout": 0,
	"address": "n1RZhxvsNRVXFgB1GKbE9XtscYUKMMcSGx",
	"account": "",
	"scriptPubKey": "76a914da5d94a04ada05253448c6cee108c67cfdc25d0588ac",
	"amount": 0.4368,
	"contractType": "NFT",
	"contractID": "d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0",
	"contractValue": "0",
	"contractMaxSupply": "100",
	"contractMetadata": '{"name":"D1",description:""}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
  {
	"txid": "df8194645691a3d65613ed3b2cae8c9b8797121d53d384d2e0d65c8ca7c4f908",
	"vout": 1,
	"address": "miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6",
	"account": "",
	"scriptPubKey": "76a91421aab58a9e80ca6a7f28c5ebcbcc94286d410f6988ac",
	"amount": 0.4368,
	"contractType": "NFT_MINT",
	"contractID": "d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0",
	"contractValue": "3",
	"contractMaxSupply": "100",
	"contractMetadata": '{"name":"NFT_CARD",description:""}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
  {
	"txid": "478ef48c0e753c2956c681c92a02ed101059b11044f59dbdf1254b3c84711e53",
	"vout": 1,
	"address": "miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6",
	"account": "",
	"scriptPubKey": "76a91421aab58a9e80ca6a7f28c5ebcbcc94286d410f6988ac",
	"amount": 0.4368,
	"contractType": "FT_MINT",
	"contractID": "c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0",
	"contractValue": "4300",
	"contractMaxSupply": "21000000",
	"contractMetadata": '{"name":"TESTCOIN","symbol":"TC","decimal":8}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
  {
	"txid": "29506301f26f6fe593440d197e427d9212875c32203e3629e1235260f61c417b",
	"vout": 1,
	"address": "msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx",
	"account": "",
	"scriptPubKey": "76a91485487c71ae9a6ab0652df0a95dfd681d6285236388ac",
	"amount": 0.4368,
	"contractType": "FT",
	"contractID": "c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0",
	"contractValue": "100",
	"contractMaxSupply": "21000000",
	"contractMetadata": '{"name":"TESTCOIN","symbol":"TC","decimal":8}',
	"confirmations": 63,
	"spendable": true,
	"solvable": true
  },
...
```

*In addition to this, custom creation of rawtransactions can also be used to achieve these goals. At this time, it is necessary to understand the output structure of the native token.*

* createrawtransaction
* signrawtransaction
* sendrawtransaction
* decoderawtransaction


## Contracts
### FT_MINT
When you use the command `createtoken FT`, a minting contract of type FT_MINT will be created. The FT_MINT contract can only be used to mint FT, it is not FT itself. The contract rules are as follows:
- `ContractType`: Is `FT_MINT`, immutable.
- `ContractID`: Unique, immutable.
- `ContractValue`: The amount of tokens minted, which increases with the issuance of tokens.
- `ContractMaxSupply`: The maximum circulation, immutable
- `ContractMetadata`: The metadata, immutable. Please refer to the [Standard](#the-fungible-token-standard)

### FT
An output of type FT means that there is an FT attached to the output. When you use the command `minttoken FT`, the UTXO of FT_MINT will be spent, and a new UTXO of FT_MINT and a UTXO of newly minted FT will be generated. The contract rules of the FT are as follows:
- `ContractType`: Is `FT`, immutable.
- `ContractID`: Unique, immutable. Inherited from FT_MINT, consistent with it.
- `ContractValue`: The amount of this token, changes when a transfer occurs.
- `ContractMaxSupply`: The maximum circulation, immutable. Inherited from FT_MINT, consistent with it.
- `ContractMetadata`: The metadata, immutable. Inherited from FT_MINT, consistent with it.

### NFT_MINT
When you use the command `createToken NFT`, a minting contract of type NFT_MINT will be created. The NFT_MINT contract can only be used to mint NFT, it is not an NFT itself. The contract rules are as follows:
- `ContractType`: Is `NFT_MINT`, immutable.
- `ContractID`: Unique, immutable.
- `ContractValue`: The number of minted NFTs, which increases with the issuance of NFTs.
- `ContractMaxSupply`: The maximum circulation, immutable
- `ContractMetadata`: The metadata of the series of NFTs, this is optional. Please refer to the [Standard](#the-non-fungible-collection-standard)

### NFT
An output of type NFT means that there is an NFT attached to the output. When you use the command `minttoken`, the UTXO of NFT_MINT will be spent, and a new UTXO of NFT_MINT and a UTXO of newly minted NFT will be generated. The contract rules of the NFT are as follows:
- `ContractType`: Is `NFT`, immutable.
- `ContractID`: Unique, immutable. Inherited from NFT_MINT, consistent with it.
- `ContractValue`: The tokenID of this NFT, immutable. Start from 1.
- `ContractMaxSupply`: The maximum circulation, immutable. Inherited from NFT_MINT, consistent with it.
- `ContractMetadata`: The metadata of this NFT. Please refer to the [Standard](#the-non-fungible-standard)

## Token Standard
### The Fungible Token Standard
We recommend a serialized JSON with the following structure.

|Field|Type|Description|
|--|--|--|
|name|string|Name of the token.|
|symbol|string|Symbol of the token.|
|decimal|number|Decimal of the token.|

Example:
```
./novo-cli createtoken FT 21000000 '{"name":"TESTCOIN","symbol":"TC","decimal":8}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    c841d5322f7553517e19e833420048bf55473be7faea7b54ce00ca1c8afcdc2a:0
```

### The Non-Fungible Standard
We recommend that the metadata for NFTs can be A uri:
`https://ipfs.io/ipfs/QmeSjSinHpPnmXmspMjwiXyN6zS4E9zccariGR3jxcaWtq/1`
or a serialized JSON with the following structure:

|Field|Type|Description|
|--|--|--|
|name|string|Name of the NFT.|
|description|string|Description of the NFT.|
|image|string|URI pointing to the NFT.|

Example:
```
./novo-cli minttoken NFT d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0 '{"name":"TESTCARD-01",description:""}' msfgzzQqGeQi2nakiiqh42MS1ueELzbyNx
```

### The Non-Fungible Collection Standard
When creating an collection of NFT, a general message can be set to show the basic info.
We recommend a serialized JSON with the following structure.

|Field|Type|Description|
|--|--|--|
|name|string|Name of the collection.|
|description|string|Description of the collection.|
|image|string|URI pointing to the collection.|

Example:
```
./novo-cli createtoken NFT 100 '{"name":"TESTCARD",description:""}' miay6TrfZgGfbCuMrJtCmUe9sPCbFurng6
    d6692f2b87c2aac24fd0f512aad1b92430ae33778f2c21b5505ac82246eceebd:0
```


## Developer Guide
In order to support native tokens, the following points need to be paid attention to.

### TxOutput
The output structure of the transaction has changed. A certain output includes a native token, which means that in addition to the value and script, there is also a token data segment in the output.

### SignatureHash
During transaction serialization, the transaction output containing the native token will serialize the token data before serializing the value. When calculating the signed SignatureHash, if the spent UTXO contains the native token, the token data will also be added before the serialized value. Please refer to bip143, where `6. value` and `8. hashOutputs` may be affected by the token data.
