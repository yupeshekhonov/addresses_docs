# Addresses conversion

## To start 

Import the `Address` object from the package. 

``` ts 
import {UniqueUtils} from "@unique-nft/api";

const address = UniqueUtils.Address
```

## constants 

This object provides some constant values for quick access. 

#### STATIC_ADDRESSES

This constant returns the following object: 

``` ts 
{
  contractHelpers: '0x842899ECF380553E8a4de75bF534cdf6fBF64049',
  collectionHelpers: '0x6C4E9fE1AE37a41E93CEE429e8E1881aBdcbb54F'
}
```

#### COLLECTION_ADDRESS_PREFIX

This constant returns the following string: 

``` ts 
'0x17c4e6453cc49aaaaeaca894e6d9683e'
```

#### NESTING_PREFIX

This constant returns the following string:

``` ts 
'0xf8238ccfff8ed887463fd5e0'
```

## is

This object provides methods for checking whether some address meets the expectations. 

### substrateAddress

The method checks whether an address is a Substrate address. 

``` ts 
const result = address.is.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// result = true
```

#### [collectionAddress]()

The method checks whether an address belongs to a collection.

#### ethereumAddress

The method checks whether an address is an Ethereum address.

``` ts 
const result = address.is.ethereumAddress('0x34097A6Aa8Cd2a82b656A3605AB058fB25E943A1'))
// result = true
```

#### nestingAddress

This method checks whether a specified NFT has any items inside. 

``` ts 
address.is.nestingAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// result = false
```

#### [collectionId]()

This method checks whether the specified number can be a collection Id.

``` ts 
address.is.collectionId(101)
// result = true
address.is.collectionId(-5)
// result = false
address.is.collectionId('id')
// result = false
```

#### [tokenId]()

This method checks whether the specified number can be a token Id.

``` ts 
let result = address.is.tokenId(11)
// result = true
result = address.is.tokenId(-5)
// result = false
result = address.is.tokenId('id')
// result = false
```

## validate

This object provides methods for checking whether an address or Id is valid.

#### substrateAddress

This method checks whether the address is a valid Substrate address.  The method returns `true` when this is so, and throws an exception when the validation fails.

``` ts
try {
   const result = address.validate.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7aazkXMxvFLHPZpA13pmwCJQ')
} catch (e) {
   console.log(e.message)
}
// output: Invalid decoded address checksum


const result = address.validate.substrateAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// result = true
```

#### [tokenId]()

This method checks whether the specified number is a valid token Id. The method returns `true` when this is so, and throws an exception when the validation fails.

``` ts 
try {
  const result = address.validate.tokenId(-5)
} catch (e) {
  console.log(e.message)


// output: collectionId should be a number between 0 and 0xffffffff
const result = address.validate.tokenId(11)
// result = true
```

#### nestingAddress 

This method checks whether a nested NFT is located by the address. The method returns `true` when this is so, and throws an exception when the validation fails.

``` ts 
try {
   let res = address.validate.nestingAddress('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
} catch (e) {
   console.log(e.message)
}
// output: address 5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ is not a nesting address

```

#### ethereumAddress

This method checks whether an address is a valid Ethereum address. The method returns `true` when this is so, and throws an exception when the validation fails.

``` ts 
try {
  const result = address.validate.ethereumAddress('0x34055Awqa8Cd2a82b656A3605AB058fB25E943A1')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: address "0x34055Awqa8Cd2a82b656A3605AB058fB25E943A1" is not valid ethereum address

const result = address.validate.ethereumAddress('0x34097A6Aa8Cd2a82b656A3605AB058fB25E943A1')
// result = true 
```

#### collectionId

This method checks whether a number is a token Id. The method returns `true` when this is so, and throws an exception when the validation fails.

``` ts
try {
  const result = address.validate.collectionId(-51)
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: collectionId should be a number between 0 and 0xffffffff

const result = address.validate.collectionId(5)
// result = true
```

## collection

This object provides methods for checking links between addresses and Ids in both directions. 

#### idToAddress

The method gets a collection address by its Id. Returns an address in the Ethereum format if Id is correct, and throws the error if this is not so.

``` ts
try {
  const result = address.collection.idToAddress(-5)
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: collectionId should be a number between 0 and 0xffffffff

const result = address.collection.idToAddress(5)
// result = '0x17c4E6453Cc49AAAaEACa894E6d9683e00000005'
```

#### addressToId

The method gets a collection Id by its address. Returns an Id if an address is correct, and throws the error if this is not so.


``` ts
try {
  const result = address.collection.addressToId('0x17c4E6453Cc49AAAEACa894E6a9683e00000005')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: address 0x17c4E6453Cc49AAAaEACa894E6a9683e00000005 is not a collection address

const result = address.collection.addressToId('0x17c4E6453Cc49AAAaEACa894E6d9683e00000005')
// result = 5
```

## nesting 

This object provides methods for checking the NFT nesting. 

#### addressToIds

The method gets a collectionId and a tokenId for nesting NFT by an address. Returns Ids if an address is a nesting address, and throws the error if this is not so.

``` ts
try {
  const result = address.nesting.addressToIds('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: address 0x17c4E6453Cc49AAAaEACa894E6a9683e00000005 is not a nesting address

const result = address.nesting.addressToIds('0x17c4E6453Cc49AAAaEACa894E6d9683e00000005')
// result = {collectionId: 5, tokenId: 1}
```

#### idsToAddress

The method gets a nesting address by the collection and token Ids.  

``` ts
try {
  const result = address.nesting.idsToAddress(-1,5)
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: collectionId should be a number between 0 and 0xffffffff

const result = address.nesting.idsToAddress(10,5)
// result = '0xf8238CCFff8ED887463fD5E00000000500000001'
```

## to 

The object provides methods for converting addresses Substrate and Ethereum address to an object or to a normalized format (prefix 42)

#### crossAccountId

The method accepts a Substrate or Ethereum address as a parameter and converts it to an object.
If a parameter is invalid, the method throws the exception. 

``` ts
try {
  const result = address.to.crossAccountId(100)
} catch (e) {
  console.log(e.message)
}
// output: Passed address 100 is not substrate nor ethereum address

const result = address.to.crossAccountId('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
const result2 = address.to.crossAccountId('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// result1 = { Ethereum: '0x17c4E6453Cc49AAAaEACa894E6a9683e00000005' }
// result2 = { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }

```

#### crossAccountIdNormalized

The method accepts a Substrate or Ethereum address as a parameter and converts it to an object with a normalized format (prefix 42).
If a parameter is invalid, the method throws the exception.

``` ts
try {
  const result = address.to.crossAccountIdNormalized('yGJMj1d32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: Passed address yGJMj1d32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL is not substrate nor ethereum address

const result = address.to.crossAccountIdNormalized('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }
```

#### substrateNormalizedOrMirrorIfEthereum

The method returns a normalized address, or mirror if an Ethereum address is passed.

``` ts
try {
  const result = address.to.substrateNormalizedOrMirrorIfEthereum('yGJMjes32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: Passed address yGJMjes32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL is not substrate nor ethereum address

const result = address.to.substrateNormalizedOrMirrorIfEthereum('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'
const result = address.to.substrateNormalizedOrMirrorIfEthereum('0xf8cC75F76d46c3b1c5F270Fe06c8FFdeAB8E5eaB')
// result = '5GwWnwbYRzwvcyAmQqCBB4h5JNspv8xPxpUm77wXbooxS3t5'
```

## extract

The object provides methods to get an address from an object. 

#### crossAccountIdFromObject

The method accepts an address or an address object, and returns an object with an normalized address. The method throws an exception if an address is invalid.

``` ts
try {
  const result = address.extract.crossAccountIdFromObject('yGJMj5z32ssBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)}
// output: Passed address is not a valid address string or object: "yGJMj5z32ssBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL"

const result = address.extract.crossAccountIdFromObject({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// result = { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }
const result = address.extract.crossAccountIdFromObject('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }
```

#### [crossAccountIdFromObjectNormalized]()

``` ts
try {
  const result = address.extract.crossAccountIdFromObject('yGJMj5z32ssBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)}
// output: Passed address is not a valid address string or object: "yGJMj5z32ssBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL"

const result = address.extract.crossAccountIdFromObjectNormalized( '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// result = { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }
const result = address.extract.crossAccountIdFromObjectNormalized('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = { Substrate: '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ' }
```

#### normalizedAddressFromObject

The method returns an address (string) in the normalized format from an object or an address. If a conversion fails, the method throws an exception.

``` ts 
try {
  const result = address.extract.normalizedAddressFromObject('yGJMj5z32dpBUigGVFgatC382Gi3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: Passed address is not a valid address string or object: "yGJMj5z32dpBUigGVFgatC382Gi3FNVSKyfgi87UF7f786MJL"

const result = address.extract.normalizedAddressFromObject('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'
const result = address.extract.normalizedAddressFromObject({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// result = '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'
```


#### normalizedAddressFromObjectSafe

The method returns an address (string) in the normalized format from an object or an address. The method does not throw an exception. If conversion fails, a `null` object is returned.

``` ts

const result = address.extract.normalizedAddressFromObject('yGJMj5z32dpBUigGVFgatC382Gi3FNVSKyfgi87UF7f786MJL')
// result = null 
const result = address.extract.normalizedAddressFromObject('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'
const result = address.extract.normalizedAddressFromObject({ Substrate: 'yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL' })
// result = '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'
```

## mirror 

The object provides the methods for converting an address to a mirror. 

#### substrateToEthereum

The method converts a Substrate address to an Ethereum mirror.

``` ts
try {
  const result = address.mirror.substrateToEthereum('yGJMj5z32dpBUigGVFddtC382Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: Invalid decoded address checksum

const result = address.mirror.substrateToEthereum('yGJMj5z32dpBUigGVFddtC382Ti3FNVSKyfgi87UF7f786MJL')
// result = '0xf8cC75F76d46c3b1c5F270Fe06c8FFdeAB8E5eaB'
```

#### ethereumToSubstrate

This method converts an Ethereum address to a Substrate mirror. It throws an exception if a passed parameter is invalid.

``` ts
try {
  const result = address.mirror.ethereumToSubstrate('0x17c4E6453Cc49AAAaEACa894E6a9683e0005')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: address "0x17c4E6453Cc49AAAaEACa894E6a9683e0005" is not valid ethereum address

const result = address.mirror.ethereumToSubstrate('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// result = '5Fp3dxhLyxkzrGE6Niqdvfy5B35duX7PcLqPpwJKaYE2bHah'
```

## normalize 

The object provides the methods for normalizing addresses. 

#### substrateAddress

The method converts a Substrate address to a normalized format. The method throws an exception if conversion fails.

``` ts
try {
  const result = address.normalize.substrateAddress('yGJMj5z32dpBUigGVFgatC772Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: Invalid decoded address checksum

const result = address.normalize.substrateAddress('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL')
// result = '5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ'
```

#### [ethereumAddress]()

The method

``` ts
try {
  const result = address.normalize.ethereumAddress('yGJMj5z32dpBUigGVFgatC772Ti3FNVSKyfgi87UF7f786MJL')
  console.log(result)
} catch (e) {
  console.log(e.message)
}
// output: address "yGJMj5z32dpBUigGVFgatC772Ti3FNVSKyfgi87UF7f786MJL" is not valid ethereum address

const result = address.normalize.ethereumAddress('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// result = '0x17c4E6453cC49aAaaEACA894e6A9683e00000005'
```

## compare 

The object provides the method for comparing addresses. 

#### ethereumAddresses

The method compare two Ethereum addresses and return `true` or `false` depending on whether they are equal.

``` ts
const result = address.compare.ethereumAddresses('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005','0x17c4E6453Cc49AAAaEACa894E6a9683e00000005')
// result = true
const result = address.compare.ethereumAddresses('0x17c4E6453Cc49AAAaEACa894E6a9683e00000005','0x17c4E6454Cc49AAAaEACa894E6a9683e00000005')
// result = false
```

#### substrateAddresses

The method compare two Substrate addresses and return `true` or `false` depending on whether they are equal.

``` ts
const result = address.compare.substrateAddresses('yGJMj5z32dpBUigGVFgatC382Ti3FNVSKyfgi87UF7f786MJL','5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ
// result = true
const result = address.compare.substrateAddresses('5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCCD','5HgvUDiRm5yjRSrrG9B6q6km7KLzkXMxvFLHPZpA13pmwCJQ')
// result = false
```