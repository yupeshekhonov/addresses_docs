# Addresses conversion

## How to start

Import the `Address` object from the package.

``` ts 
import {UniqueUtils} from "@unique-nft/api";

const Address = UniqueUtils.Address
```

## Overview

Address is a set of functions (grouped by purpose) for checking, validation, conversion and boxing/unboxing of substrate and ethereum addresses. It's written with pure Typescript, which means no WASM dependencies or big libraries. Minified bundled version weights only 8Kb and never requires any other libraries.

Only necessary libraries included: SHA3 (256), blake2b and Base58/64.

CommonJS and IIFE builds are completely bundled, they have no any dependencies or imports.

ESM build has imports for tree shaking in modern building environments (webpack, vite, parcel, etc...)

## Contents

Address consists some useful utilities for substrate and ethereum addresses processing and conversion
- **constants**: ethereum addresses for static smart contracts and address prefixes
- **is**: set of functions checking whether passed param is substrate address, ethereum address, nested address, etc...
- **validate**: set of validators, i.e. functions throwing an error when passed param is not a valid substrate address, ethereum address, nested address, etc...
- **collection**: converters of collection id <-> collection ethereum address
- **nesting**: converters of {collectionId, tokenId} <-> nested ethereum address
- **to**: converters to CrossAccountId (boxed substrate/ethereum address) and other common converters
- **extract**: extractors of addresses with from CrossAccountId
- **mirror**: converters of substrate and ethereum mirrors
- **normalize**: address normalizers (substrate to 42 prefix and ethereum capitalizer)
- **compare**: address comparators
- **substrate**: substrate address helpers

### constants

Some constants ethereum addresses and address prefixes

Prefixes:
```ts
Address.constants.COLLECTION_ADDRESS_PREFIX          // '0x17c4e6453cc49aaaaeaca894e6d9683e'
Address.constants.NESTING_PREFIX                     // '0xf8238ccfff8ed887463fd5e0'
```

CollectionHelpers and ContractHelpers smart contracts addresses:
```ts
Address.constants.STATIC_ADDRESSES.collectionHelpers // '0x6C4E9fE1AE37a41E93CEE429e8E1881aBdcbb54F'
Address.constants.STATIC_ADDRESSES.contractHelpers   // '0x842899ECF380553E8a4de75bF534cdf6fBF64049'
```
_Note: these addresses are really static, i.e. immutable. They are meant to be same between releases._

### is

This object provides methods for checking whether some address meets the expectations.
All methods return `true` or `false`, never throws an error.

#### substrateAddress

The method checks whether an address is a valid substrate address (with checksum check).

```ts
substrateAddress: (address: string) => boolean
```

Examples:
``` ts 
Address.is.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') // true
Address.is.substrateAddress('123') // false
Address.is.substrateAddress([]) // false
```


#### ethereumAddress

The method checks whether an address is a valid ethereum address (input address capitalization doesn't matter).

```ts
ethereumAddress: (address: string) => boolean
```

Examples:
``` ts 

Address.is.ethereumAddress('0x34055Awqa8Cd2a82b656A3605AB058fB25E943A1') // true
Address.is.ethereumAddress('0x34055awqa8cd2a82b656a3605ab058fb25e943a1') // true
Address.is.ethereumAddress('0x17c4e6453cc49aaaaeaca894e6d9683e00000001') // true
Address.is.ethereumAddress('0x17C4e6453cC49AAaaEaCA894E6D9683e00000001') // true
Address.is.ethereumAddress('0x34055awqa8cd2a82b656a3605ab058fb25e943a') // false
Address.is.ethereumAddress('123') // false
Address.is.ethereumAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') // false
Address.is.ethereumAddress([]) // false
```

#### collectionAddress

The method checks whether an address is a valid collection address (input address capitalization doesn't matter).

```ts
collectionAddress: (address: string) => boolean
```

Examples:
``` ts 
Address.is.collectionAddress('0x17c4e6453cc49aaaaeaca894e6d9683e00000001') // true
Address.is.collectionAddress('0x17C4e6453cC49AAaaEaCA894E6D9683e00000001') // true
Address.is.collectionAddress('0x27C4e6453cC49AAaaEaCA894E6D9683e00000001') // false
Address.is.collectionAddress('123') // false
Address.is.collectionAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') // false
Address.is.collectionAddress([]) // false
```

#### nestingAddress

This method checks whether an address is a valid nesting address (input address capitalization doesn't matter).

```ts
nestingAddress: (address: string) => boolean
```

Examples:
``` ts 
Address.is.nestingAddress('0xF8238Ccfff8Ed887463Fd5E00000007f000000fe') // true
Address.is.nestingAddress('0xf8238ccfff8ed887463fd5e00000007f000000fe') // true
Address.is.nestingAddress('0x17C4e6453cC49AAaaEaCA894E6D9683e00000001')  // false
Address.is.nestingAddress('0x34055awqa8cd2a82b656a3605ab058fb25e943a')   // false
Address.is.nestingAddress('123') // false
Address.is.nestingAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') // false
Address.is.nestingAddress([]) // false
```

#### collectionId

This method checks whether the specified number looks like a valid collection id.

```ts
collectionId: (collectionId: number) => boolean
```

Examples:

``` ts 
Address.is.collectionId(1) // true
Address.is.collectionId(101) // true
Address.is.collectionId(0xffffffff) // true
Address.is.collectionId(0) // false
Address.is.collectionId(0xffffffff + 1) // false (out of range [1, 0xffffffff])
Address.is.collectionId(-5) // false
Address.is.collectionId('id') // false
Address.is.collectionId('0x17c4e6453cc49aaaaeaca894e6d9683e00000001') // false // for this case please refer to Address.is.collectionAddress
Address.is.collectionId([]) // false
```

#### tokenId

This method checks whether the specified number is a valid token id.

```ts
tokenId: (tokenId: number) => boolean
```

Examples: 

``` ts 
Address.is.tokenId(11) //  true
Address.is.tokenId(111) //  true
Address.is.collectionId(0xffffffff) //  true
Address.is.tokenId(-5) //  false
Address.is.tokenId('id') // false
Address.is.collectionId('0x17c4e6453cc49aaaaeaca894e6d9683e00000001') // false
Address.is.tokenId([]]) // false
```

#### crossAccountId 

This method checks whether a passed argument is a valid Cross Account Id object. 

``` ts
crossAccountId: (obj: any) => obj is CrossAccountId
```

Examples: 

``` ts
Address.is.crossAccountId({ Ethereum: '0x17c4E6453cC49aAaaEACA894e6A9683e00000005' })
// true

Address.is.crossAccountId({ Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' })
// true

Address.is.crossAccountId({ Substrate: '5HgvUDiRm5yjRSrrF9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' })
// false

Address.is.crossAccountId({ Ethereum: '5HgvUDiRm5yjRSrrF9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' })
// false

Address.is.crossAccountId('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// false (because it is a string even with a valid address)

Address.is.crossAccountId('5') // false
Address.is.crossAccountId(110) // false
Address.is.crossAccountId([]) // false
``` 

#### crossAccountIdUncapitalized

? 

``` ts
crossAccountIdUncapitalized: (obj: any) => obj is CrossAccountIdUncapitalized
```

#### substrateAddressObject

The method checks whether a passed argument is a valid Substrate address object. 

``` ts
substrateAddressObject: (obj: any) => obj is SubAddressObj
```

Examples: 

``` ts
Address.is.substrateAddressObject({ Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' })
// true

Address.is.substrateAddressObject({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// true

Address.is.substrateAddressObject({ Substrate: '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005' })
// false

Address.is.substrateAddressObject({ Ethereum: '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005' })
// false

Address.is.substrateAddressObject('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// false

Address.is.substrateAddressObject('address') // false
Address.is.substrateAddressObject(50) // false
Address.is.substrateAddressObject([]) // false
```

#### ethereumAddressObject

The method checks whether a passed argument is a valid Substrate address object.

``` ts
ethereumAddressObject: (obj: any) => obj is EthAddressObj;
```

Examples: 

``` ts
Address.is.ethereumAddressObject({ Ethereum: '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005' })
// true

Address.is.ethereumAddressObject({ Substrate: '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005' })
// false

Address.is.ethereumAddressObject('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// false

Address.is.ethereumAddressObject('address') // false
Address.is.ethereumAddressObject(5) // false
Address.is.ethereumAddressObject([]) // false
```

#### substrateAddressObjectUncapitalized

? 

``` ts
substrateAddressObjectUncapitalized: (obj: any) => obj is SubAddressObjUncapitalized
```

#### ethereumAddressObjectUncapitalized

? 

``` ts
ethereumAddressObjectUncapitalized: (obj: any) => obj is EthAddressObjUncapitalized
```

### validate

This object provides methods for checking whether some address meets the expectations. The difference from the [is](#is) object is that this method throws an error when the address cannot be checked. If the address is correct, the methods return `true`.

#### substrateAddress

This method checks whether the address is a valid Substrate Address. 

``` ts
substrateAddress: (address: string) => boolean
```

Examples: 

``` ts
Address.validate.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') 
// true

Address.validate.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7aazkXMxvFLHPZpA13pmwCJQ')
// throws the error (Invalid decoded address checksum)

Address.validate.substrateAddress('address') // throws the error 
Address.validate.substrateAddress(101) // throws the error 
Address.validate.substrateAddress([]) // throws the error 
```

#### ethereumAddress

This method checks whether an address is a valid Ethereum Address. 

``` ts
ethereumAddress: (address: string) => boolean
```

Examples: 

``` ts 
Address.validate.ethereumAddress('0x34097A6Aa8Cd2a82b656A3605AB058fB25E943A1') // true 

Address.validate.ethereumAddress('0x34055Awqa8Cd2a82b656A3605AB058fB25E943A1')
// throws the error (address "0x34055Awqa8Cd2a82b656A3605AB058fB25E943A1" is not valid ethereum address

Address.validate.ethereumAddress('address') // throws the error 

Address.validate.ethereumAddress(101) // throws the error 

Address.validate.ethereumAddress([]) // throws the error 
```

#### nestingAddress

This method checks whether an address is a valid nesting address (input address capitalization doesn't matter).

``` ts 
nestingAddress: (address: string) => boolean
```

Examples: 

``` ts 
Address.validate.nestingAddress('0xF8238Ccfff8Ed887463Fd5E00000007f000000fe') // true
Address.validate.nestingAddress('0xf8238ccfff8ed887463fd5e00000007f000000fe') // true
Address.validate.nestingAddress('0x17C4e6453cC49AAaaEaCA894E6D9683e00000001')  // throws the error
Address.validate.nestingAddress('0x34055awqa8cd2a82b656a3605ab058fb25e943a')   // throws the error
Address.validate.nestingAddress('123') // throws the error
Address.validate.nestingAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') // throws the error
Address.validate.nestingAddress([]) // throws the error
```

#### collectionId

This method checks whether a number is a token id. The method returns `true` when this is so, and throws an error when the validation fails.

``` ts
collectionId: (collectionId: number) => boolean
```

Examples: 

``` ts
Address.validate.collectionId(5) // true
Address.validate.collectionId(105) // true/
Address.validate.collectionId(0xffffffff) // true
Address.validate.collectionId(0) // throws the error
Address.validate.collectionId(0xffffffff + 1) // throws the error (out of range [1, 0xffffffff])
Address.validate.collectionId(-5) // throws the error
Address.validate.collectionId('id') // throws the error
Address.validate.collectionId([]) // throws the error
```

#### tokenId

This method checks whether the specified number is a valid token id. 

``` ts 
tokenId: (tokenId: number) => boolean
```

Examples: 

``` ts 
Address.validate.tokenId(11) //  true
Address.validate.tokenId(111) //  true
Address.validate.collectionId(0xffffffff) //  true
Address.validate.tokenId(-5) //  throws the error
Address.validate.tokenId('id') // throws the error
Address.validate.collectionId('0x17c4e6453cc49aaaaeaca894e6d9683e00000001') // throws the error
Address.validate.tokenId([]]) // throws the error
```


### collection

This object provides methods for converting between addresses and Ids of a collection in both directions. The method works with Ethereum addresses.

#### idToAddress

The method gets a collection address by its id. Returns an address in the Ethereum format if id is correct, and throws the error if this is not so.

``` ts
idToAddress: (collectionId: number) => string | null
```

Examples: 

``` ts
Address.collection.idToAddress(5) // '0x17c4E6453Cc49AAAaEACa894E6d9683e00000005'
Address.collection.idToAddress(105) // '0x17c4e6453CC49aaaAEAcA894e6d9683E00000069'
Address.collection.idToAddress(-5) // throws the error (collectionId should be a number between 0 and 0xffffffff)
Address.collection.idToAddress('0x17c4e6453cc49aaaaeaca894e6d9683e00000001') // throws the error
Address.collection.idToAddress('id') // throws the error
Address.collection.idToAddress([]) // throws the error
```

#### addressToId

The method gets a collection id by its address. Returns an id if an address is correct, and throws the error if this is not so.

``` ts
addressToId: (address: string) => number | null 
```

Examples: 

``` ts
Address.collection.addressToId('0x17c4E6453Cc49AAAaEACa894E6d9683e00000005') // 5
Address.collection.addressToId('0x17c4e6453CC49aaaAEAcA894e6d9683E00000069') // 105

Address.collection.addressToId('0x17c4E6453Cc49AAAEACa894E6a9683e00000005') 
// throws the error (address 0x17c4E6453Cc49AAAaEACa894E6a9683e00000005 is not a collection address)

Address.collection.addressToId('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCCD')
//throws the error

Address.collection.addressToId('address') //throws the error
Address.collection.addressToId(101) //throws the error
Address.collection.addressToId([]) //throws the error
```

### nesting

This object provides methods for converting nesting addresses to ids and vice versa. The methods work for Ethereum addresses.

#### addressToIds

The method gets a collectionId and a tokenId for by an address. Returns Ids if an address is a nesting address, and throws the error if this is not so.

``` ts
addressToIds: (address: string) => {
   collectionId: number;
   tokenId: number;
}
```

Examples:

``` ts
Address.nesting.addressToIds('0x17c4E6453Cc49AAAaEACa894E6d9683e00000005')
// {collectionId: 5, tokenId: 1}

Address.nesting.addressToIds('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// throws the error (address 0x17c4E6453Cc49AAAaEACa894E6a9683e00000005 is not a nesting addresst)

Address.nesting.addressToIds('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ') // throws the error
Address.nesting.addressToIds('address') // throws the error
Address.nesting.addressToIds(102) // throws the error
Address.nesting.addressToIds([]) // throws the error
```

#### idsToAddress

The method gets a nesting address by the collection and token ids.

``` ts
idsToAddress: (collectionId: number, tokenId: number) => string
```

Examples: 

``` ts
Address.nesting.idsToAddress(10, 5) // '0xf8238CCFff8ED887463fD5E00000000500000001'
Address.nesting.idsToAddress(101, 50) // '0xf8238ccFfF8ed887463Fd5E00000006500000032'
Address.nesting.idsToAddress(-1, 5) // throws the error (collectionId should be a number between 0 and 0xffffffff)
Address.nesting.idsToAddress(-10, -15) // throws the error
Address.nesting.idsToAddress('id', -15) // throws the error
Address.nesting.idsToAddress('id', 'id2') // throws the error
Address.nesting.idsToAddress([],15) // throws the error
Address.nesting.idsToAddress('id',[]) // throws the error
```

### to

The object provides methods for converting Substrate or Ethereum address to an object or to a normalized format (prefix 42)

#### crossAccountId

The method accepts a Substrate or Ethereum address as a parameter and converts it to a Cross Account Id object.
If a parameter is invalid, the method throws the error.

``` ts
crossAccountId: (address: string) => CrossAccountId
```

Examples: 

``` ts
Address.to.crossAccountId('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// { Ethereum: '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005' }

Address.to.crossAccountId('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

Address.to.crossAccountId('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJ') // throws the error
Address.to.crossAccountId('0x17c4E6453Cc49AAAaEACa894E6a9683e0000000') // throws the error
Address.to.crossAccountId(100) // throws the error
Address.to.crossAccountId([])  // throws the error
```

#### crossAccountIdNormalized

The method accepts a Substrate or Ethereum address as a parameter and converts it to an object with a normalized format (prefix 42). 
If a parameter is invalid, the method throws the error.

In case of Substrate address, the method normalizes it (prefix 42). In case of Ethereum address, it capitalizes letters.

``` ts
crossAccountIdNormalized: (address: string) => CrossAccountId
```

Examples:

``` ts
Address.to.crossAccountIdNormalized('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
//{ Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

Address.to.crossAccountIdNormalized('0x17c4e6453cc49aaaaeaca894E6a9683e00000005')
// { Ethereum: '0x17c4E6453cC49aAaaEACA894e6A9683e00000005' }

Address.to.crossAccountIdNormalized('0x17c4E6453Cc49aaaaeaca894e6a9683e000000')
// throws error 

Address.to.crossAccountIdNormalized('address') // throws error
Address.to.crossAccountIdNormalized(5) // throws error
Address.to.crossAccountIdNormalized([]) // throws error
```

#### substrateNormalizedOrMirrorIfEthereum

The method returns a normalized Substrate address, or a Substrate mirror if an Ethereum address is passed.

``` ts
substrateNormalizedOrMirrorIfEthereum: (address: string) => string
```

Examples: 

``` ts
Address.to.substrateNormalizedOrMirrorIfEthereum('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'

Address.to.substrateNormalizedOrMirrorIfEthereum('0xf8cC75F76d46c3b1c5F270Fe06c8FFdeAB8E5eaB')
// '5GwWnwbYRzwvcyAmQqCBB4h5JNspv8xPxpUm77wXbooxS3t5'

Address.to.substrateNormalizedOrMirrorIfEthereum('yGJMjes32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// throws error 

Address.to.substrateNormalizedOrMirrorIfEthereum('address') // throws the error
Address.to.substrateNormalizedOrMirrorIfEthereum(55) // throws the error
Address.to.substrateNormalizedOrMirrorIfEthereum([]) // throws the error
```

### extract

The object provides methods to get an address from an object.

#### normalizedAddressFromObject

The method returns an address (string) in the normalized format from an object or an address. If a conversion fails, the method throws an error.

``` ts
normalizedAddressFromObject: (address: string | object) => string
```

Examples: 

``` ts 
Address.extract.normalizedAddressFromObject('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'

Address.extract.normalizedAddressFromObject({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'

Address.extract.normalizedAddressFromObject('0x17c4E6453Cc49aaaaeaca894E6a9683e00000005')
// '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'

Address.extract.normalizedAddressFromObject({Ethereum: '0x17c4E6453cC49aAaaeaca894e6A9683e00000005'})
// '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'

Address.extract.normalizedAddressFromObject('yGJMj5z32dpBUigGVFgatC382Gi3FNVSKyfgi87UF7f786MJL')
// throws the error

Address.extract.normalizedAddressFromObject(11) // throws the error
Address.extract.normalizedAddressFromObject('511') // throws the error
Address.extract.normalizedAddressFromObject([]) // throws the error
```

#### normalizedAddressFromObjectSafe

The method returns an address (string) in the normalized format from an object or an address. The method does not throw an error. If conversion fails, a `null` object is returned.

``` ts
normalizedAddressFromObjectSafe: (address: string | object) => string | null
```

Examples: 

``` ts
Address.extract.normalizedAddressFromObjectSafe('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'

Address.extract.normalizedAddressFromObjectSafe({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'

Address.extract.normalizedAddressFromObjectSafe('0x17c4E6453Cc49aaaaeaca894E6a9683e00000005')
// '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'

Address.extract.normalizedAddressFromObjectSafe({Ethereum: '0x17c4E6453cC49aAaaeaca894e6A9683e00000005'})
// '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'

Address.extract.normalizedAddressFromObjectSafe('yGJMj5z32dpBUigGVFgatC382Gi3FNVSKyfgi87UF7f786MJL')
// null 

Address.extract.normalizedAddressFromObjectSafe(50) // null 
Address.extract.normalizedAddressFromObjectSafe('id') // null 
Address.extract.normalizedAddressFromObjectSafe([]) // null 
```

#### crossAccountIdFromObject

The method accepts an address or an address object, and converts it to a Cross Account Id object. The method throws an error if an address is invalid.

``` ts
crossAccountIdFromObject: (obj: any) => CrossAccountId
``` 

Examples: 

``` ts
Address.extract.crossAccountIdFromObject({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

Address.extract.crossAccountIdFromObject('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

Address.extract.crossAccountIdFromObject({Ethereum: '0x17c4E6453cC49aAaaeaca894e6A9683e00000005'})
// { Ethereum: '0x17c4E6453cC49aAaaEACA894e6A9683e00000005' }

Address.extract.crossAccountIdFromObject('yGJMj5z32ssBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// throws the error 

Address.extract.crossAccountIdFromObject('object') // throws the error
Address.extract.crossAccountIdFromObject(10) // throws the error
Address.extract.crossAccountIdFromObject([]) // throws the error
```

#### crossAccountIdFromObjectNormalized

The method accepts an address or an address object, and converts it to a Cross Account Id object with the normalized format. The method throws an error if an address is invalid.

``` ts
crossAccountIdFromObjectNormalized: (obj: any) => CrossAccountId
```

Examples: 

``` ts
Address.extract.crossAccountIdFromObjectNormalized( '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

Address.extract.crossAccountIdFromObjectNormalized('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

Address.extract.crossAccountIdFromObjectNormalized({Ethereum: '0x17c4E6453cC49aAaaeaca894e6A9683e00000005'})
// { Ethereum: '0x17c4E6453cC49aAaaEACA894e6A9683e00000005' }

Address.extract.crossAccountIdFromObjectNormalized('yGJMj5z32ssBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// throws the error

Address.extract.crossAccountIdFromObjectNormalized('object') // throws the error
Address.extract.crossAccountIdFromObjectNormalized(10) // throws the error
Address.extract.crossAccountIdFromObjectNormalized([]) // throws the error
```

## mirror

The object provides the methods for converting a Substrate address to an Ethereum mirror and vice versa.

#### substrateToEthereum

The method converts a Substrate address to an Ethereum mirror. The second optional parameter allows specifying whether it is needed to validate checksum. Checksum is a part of a Substrate address along with prefix and public key. 

``` ts
substrateToEthereum: (address: string, ignoreChecksum?: boolean | undefined) => string
```

Examples: 

``` ts
Address.mirror.substrateToEthereum('yGJMj5z32dpBUigGVFddtC382Ti3FNVSKyfgi87UF7f786MJL')
// '0xf8cC75F76d46c3b1c5F270Fe06c8FFdeAB8E5eaB'

Address.mirror.substrateToEthereum('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// '0xf8cC75F76d46c3b1c5F270Fe06c8FFdeAB8E5eaB'

Address.mirror.substrateToEthereum('yGJMj5z32dpBUigGVFddtC382Ti3FNVSKyfgi87UF7f786MJL')
// throws the error

Address.mirror.substrateToEthereum('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// throws the error

Address.mirror.substrateToEthereum('address') // throws the error
Address.mirror.substrateToEthereum(101) // throws the error
Address.mirror.substrateToEthereum([]) // throws the error
```

#### ethereumToSubstrate

This method converts an Ethereum mirror address to a Substrate address. It throws an error if a passed parameter is invalid.

As the second optional parameter, you can pass the SS58 format for encoding. The default value is 42.

``` ts
ethereumToSubstrate: (evmAddress: string, ss58Format?: number) => string
```

Examples: 

``` ts
Address.mirror.ethereumToSubstrate('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// '5Fp3dxhLyxkzrGE6Niqdvfy5B35duX7PcLqPpwJKaYE2bHah'

Address.mirror.ethereumToSubstrate('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005', 2)
// GKfJH3DcKmvbv3Y9RegpdL5KdMsiBva4ig9DbaH4LSXLbzS

Address.mirror.ethereumToSubstrate('0x17c4E6453Cc49AAAaEACa894E6a9683e0005')
// throws the error

Address.mirror.ethereumToSubstrate('yGJMj5z32dpBUigGVFddtC382Ti3FNVSKyfgi87UF7f786MJL')
// throws the error

Address.mirror.ethereumToSubstrate('address') // throws the error
Address.mirror.ethereumToSubstrate(5) // throws the error
Address.mirror.ethereumToSubstrate([]) // throws the error
```

### normalize

The object provides the methods for normalizing addresses.

#### substrateAddress

The method converts a Substrate address to a normalized format (by default). However, you can use the second optional parameter to convert an address to a format with needed prefix.

The method throws an error if conversion fails.

``` ts
substrateAddress: (address: string, prefix?: number) => string
```

Examples: 

``` ts
Address.normalize.substrateAddress('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL'
// '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'

Address.normalize.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ', 255)
// 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL'

address.normalize.substrateAddress('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL', 255)
// 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL'

address.normalize.substrateAddress('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL', 7391)
// 'unk9GwxLcJ7VHE75RgDYuRjuewZBGWHWvwgdVMSN3pPz9bY52'

Address.normalize.substrateAddress('yGJMj5z32dpBUigGVFgatC772Ti3FNVSKyfgi87UF7f786MJL')
// throws the error

Address.normalize.substrateAddress('address') // throws the error
Address.normalize.substrateAddress(101) // throws the error
Address.normalize.substrateAddress([]) // throws the error
```

#### ethereumAddress

The method normalizes an Ethereum address (capitalizes letters). 

``` ts
ethereumAddress: (address: string) => string
```

Examples: 

``` ts
Address.normalize.ethereumAddress('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'

Address.normalize.ethereumAddress('0x17c4E6453Cc49aaaaeaCa894E6a9683e00000005')
// '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'

Address.normalize.ethereumAddress('yGJMj5z32dpBUigGVFgatC772Ti3FNVSKyfgi87UF7f786MJL')
// throws the error

Address.normalize.ethereumAddress('address') // throws the error
Address.normalize.ethereumAddress(100) // throws the error
Address.normalize.ethereumAddress([]) // throws the error
```

### compare

The object provides the method for comparing addresses and methods return `true` or `false` depending on whether the addresses are equal. The methods never throws an error.

#### ethereumAddresses

The method compare two Ethereum addresses. 

``` ts 
ethereumAddresses: (address1: string | object | {
  Ethereum: string;
}, address2: string | object | {
  Ethereum: string;
}) => boolean
```
 
Examples: 

``` ts
Address.compare.ethereumAddresses('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005','0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// true

Address.compare.ethereumAddresses('0x17c4E6453Cc49aaaaeaca894E6a9683e00000005','0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// true

Address.compare.ethereumAddresses('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005','0x17c4E6454Cc49AAAaEACa894E6a9683e00000005')
// false

Address.compare.ethereumAddresses('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ', '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// false (for equal Substrate addresses)

Address.compare.ethereumAddresses(5, '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005') // false 
Address.compare.ethereumAddresses(5, 2) // false 
Address.compare.ethereumAddresses('address', '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005') // false 
Address.compare.ethereumAddresses(5, 'address') // false
Address.compare.ethereumAddresses(5, []) // false
Address.compare.ethereumAddresses([], 'address') // false
```

#### substrateAddresses

The method compares two Substrate addresses.

``` ts
substrateAddresses: (address1: string | object | {
  Substrate: string;
}, address2: string | object | {
  Substrate: string;
}) => boolean
```

Examples: 

``` ts
Address.compare.substrateAddresses('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ', '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// true

Address.compare.substrateAddresses('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL','5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ
// true

Address.compare.substrateAddresses('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCCD','5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// false

Address.compare.substrateAddresses('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005', '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005
// false (for identical Ethereum addresses)

Address.compare.substrateAddresses('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCCD',2)
// false 

Address.compare.substrateAddresses(5,2) // false 
Address.compare.substrateAddresses('address', '2') // false 
Address.compare.substrateAddresses([],2) // false 
Address.compare.substrateAddresses([],'address') // false 
```
