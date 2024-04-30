---
title: XCM SDK Reference for v1
description: A reference for the interfaces and methods in the Moonbeam XCM SDK that can be used to send XCM transfers between chains within the Polkadot/Kusama ecosystems. TODO-update
---

# Core Methods

The XCM SDK is based on the premise of defining an asset, source chain, and destination chain, which collectively form the transfer data for a cross-chain transfer via XCM. The following sections cover the methods for interacting with assets, building transfer data, and initiating cross-chain transfers.

<script setup>import {DividePage} from 'vitepress-theme-api';</script>

## Initialize the SDK

The `Sdk` function acts as an initializer, as it **must be called first to expose the methods of the XCM SDK**. It returns an entry point into the SDK to start interacting with the supported assets and building the transfer data required to initiate a cross-chain transfer.

<DividePage :top="63">
<template #left>

### Parameters {#sdk-parameters}

|    Name    |     Type     |                                      Description                                      |
|:----------:|:------------:|:-------------------------------------------------------------------------------------:|
| `options?` | *SdkOptions* | Allows you to specify additional options when initializing the SDK, such as a signer |

### Returns {#sdk-returns}

|       Name        |   Type   |                                                        Description                                                         |
|:-----------------:|:--------:|:--------------------------------------------------------------------------------------------------------------------------:|
|     `assets`      | *function* | Provides an entry point to building the data necessary to transfer an asset between a source chain and a destination chain |
| `getTransferData` | *function* |               Builds the data necessary to transfer an asset between a source chain and a destination chain                |

</template>
<template #right>

::: info Example
```js
import { Sdk } from '@moonbeam-network/xcm-sdk';

const sdkInstance = new Sdk();
console.log(sdkInstance);
```
:::

::: info Response
```js
{
  assets: [Function: assets],
  getTransferData: [AsyncFunction: getTransferData]
}
```
:::

</template>
</DividePage>

## Get Transfer Data

The `getTransferData` function builds the data necessary to transfer an asset between a source chain and a destination chain.

<DividePage :top="63">
<template #left>

### Parameters {#get-transfer-data-parameters}

|          Name           |               Type               |                                                               Description                                                               |
|:-----------------------:|:--------------------------------:|:---------------------------------------------------------------------------------------------------------------------------------------:|
|  `destinationAddress`   |             *string*             |                                      The address of the receiving account on the destination chain                                      |
| `destinationKeyorChain` |       *string \| AnyChain*       |                                            The key or `Chain` data for the destination chain                                            |
|      `evmSigner?`       | *EthersSigner  \| WalletClient*  | The signer for Ethereum-compatible chains that use H160 Ethereum-style accounts. Can be either an Ethers signer or a viem Wallet Client |
|      `keyOrAsset`       |        *string \| Asset*         |                                         The key or `Asset` data for the asset being transferred                                         |
|    `polkadotSigner?`    | *PolkadotSigner \| IKeyringPair* |                                                   The Polkadot signer or Keyring pair                                                   |
|     `sourceAddress`     |             *string*             |                                         The address of the sending account on the source chain                                          |
|   `sourceKeyOrChain`    |       *string \| AnyChain*       |                                              The key or `Chain` data for the source chain                                               |

### Returns {#get-transfer-data-returns}

|       Name       |                                     Type                                      |                                       Description                                       |
|:----------------:|:-----------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------:|
|  `destination`   | [*DestinationChainTransferData*](#the-destination-chain-transfer-data-object) |                 The assembled destination chain and address information                 |
|  `getEstimate`   |                                   function                                    | Gets the estimated amount of the asset that will be received by the destination address |
| `isSwapPossible` |                                   *boolean*                                   |                       Returns whether or not the swap is possible                       |
|      `max`       |                   [*AssetAmount*](#the-asset-amount-object)                   |                 The maximum amount of the asset that can be transferred                 |
|      `min`       |                   [*AssetAmount*](#the-asset-amount-object)                   |                 The minimum amount of the asset that can be transferred                 |
|     `source`     |      [*SourceChainTransferData*](#the-source-chain-transfer-data-object)      |                   The assembled source chain and address information                    |
|      `swap`      |                                   function                                    |    Swaps the destination and the source chains and returns the swapped transfer data    |
|    `transfer`    |                                   function                                    |  Transfers a given amount of the asset from the source chain to the destination chain   |

</template>
<template #right>

::: info Example
```js
import { Sdk } from '@moonbeam-network/xcm-sdk';

const sdkInstance = new Sdk();

const main = async () => {
  const data = await sdkInstance.getTransferData({
    destinationAddress: evmSigner.address
    destinationKeyOrChain: 'moonbeam',
    keyOrAsset: 'dot',
    polkadotSigner: pair,
    sourceAddress: pair.address,
    sourceKeyOrChain: 'polkadot',
    evmSigner,
  });
};

main();
```
:::

::: info Response
```js
{
  destination: {
    balance: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    },
    chain: l {
      ecosystem: 'polkadot',
      isTestChain: false,
      key: 'moonbeam',
      name: 'Moonbeam',
      type: 'evm-parachain',
      assetsData: [Map],
      genesisHash: '0xfe58ea77779b7abda7da4ec526d14db9b1e9cd40a217c34892af80a9b332b76d',
      parachainId: 2004,
      ss58Format: 1284,
      usesChainDecimals: false,
      weight: 1000000000,
      ws: 'wss://wss.api.moonbeam.network',
      id: 1284,
      rpc: 'https://rpc.api.moonbeam.network'
    },
    existentialDeposit: {
      key: 'glmr',
      originSymbol: 'GLMR',
      amount: 0n,
      decimals: 18,
      symbol: 'GLMR'
    },
    fee: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 33068783n,
      decimals: 10,
      symbol: 'DOT'
    },
    min: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    }
  },
  getEstimate: [Function: getEstimate],
  isSwapPossible: true,
  max: {
    key: 'dot',
    originSymbol: 'DOT',
    amount: 0n,
    decimals: 10,
    symbol: 'DOT'
  },
  min: {
    key: 'dot',
    originSymbol: 'DOT',
    amount: 33068783n,
    decimals: 10,
    symbol: 'DOT'
  },
  source: {
    balance: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    },
    chain: {
      ecosystem: 'polkadot',
      isTestChain: false,
      key: 'polkadot',
      name: 'Polkadot',
      type: 'parachain',
      assetsData: Map(0) {},
      genesisHash: '0x91b171bb158e2d3848fa23a9f1c25182fb8e20313b2c1eb49219da7a70ce90c3',
      parachainId: 0,
      ss58Format: 0,
      usesChainDecimals: false,
      weight: 1000000000,
      ws: 'wss://rpc.polkadot.io'
    },
    destinationFeeBalance: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    },
    existentialDeposit: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 10000000000n,
      decimals: 10,
      symbol: 'DOT'
    },
    fee: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 169328990n,
      decimals: 10,
      symbol: 'DOT'
    },
    feeBalance: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    },
    max: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    },
    min: {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    }
  },
  swap: [AsyncFunction: swap],
  transfer: [AsyncFunction: transfer]
}
```
:::

</template>
</DividePage>

## Retrieve Supported Assets

The `assets` function provides an entry point to building the data necessary to transfer an asset between a source chain and a destination chain.

### Parameters {#assets-parameters}

|     Name     |                        Type                        |                                     Description                                      |
|:------------:|:--------------------------------------------------:|:------------------------------------------------------------------------------------:|
| `ecosystem?` | [*Ecosystem*](/core-interfaces#the-ecosystem-type) | Specify the ecosystem for a set of assets: `polkadot`, `kusama`, or `alphanet-relay` |

### Returns {#assets-returns}

|   Name   |   Type    |           Description            |
|:--------:|:---------:|:--------------------------------:|
| `assets` | *Asset[]* |  A list of the supported assets  |
| `asset`  | function  | Sets the asset to be transferred |

Refer to the following section on how to continue to build the transfer data using the `asset` function.

## Select an Asset to Transfer

The `asset` function that is exposed when calling `Sdk().assets()` allows you to set the asset to be transferred.

### Parameters {#asset-parameters}

|     Name     |       Type        |                       Description                       |
|:------------:|:-----------------:|:-------------------------------------------------------:|
| `keyOrAsset` | *string \| Asset* | The key or `Asset` data for the asset being transferred |

### Returns {#asset-returns}

|      Name      |     Type     |                          Description                          |
|:--------------:|:------------:|:-------------------------------------------------------------:|
| `sourceChains` | *AnyChain[]* | A list of the supported source chains for the specified asset |
|    `source`    |   function   |       Sets the source chain to transfer the asset from        |

## Select a Source Chain for the Asset

The `source` function sets the source chain to transfer the asset from. **Must call the `asset` function first**.

### Parameters {#source-parameters}

|     Name     |         Type         |                 Description                  |
|:------------:|:--------------------:|:--------------------------------------------:|
| `keyOrChain` | *string \| AnyChain* | The key or `Chain` data for the source chain |

### Returns {#source-returns}

|        Name         |     Type     |                                     Description                                     |
|:-------------------:|:------------:|:-----------------------------------------------------------------------------------:|
| `destinationChains` | *AnyChain[]* | A list of the supported destination chains for the specified asset and source chain |
|    `destination`    |   function   |                Sets the destination chain to transfer the asset from                |

## Select a Destination Chain for the Asset

The `destination` function sets the destination chain to transfer the asset to. **Must call the `source` function first**.

### Parameters {#destination-parameters}

|     Name     |         Type         |                    Description                    |
|:------------:|:--------------------:|:-------------------------------------------------:|
| `keyOrChain` | *string \| AnyChain* | The key or `Chain` data for the destination chain |

### Returns {#destination-returns}

|    Name    |   Type   |                                          Description                                          |
|:----------:|:--------:|:---------------------------------------------------------------------------------------------:|
| `accounts` | function | Sets the source address, the destination address, and the signer(s) required for the transfer |

## Set the Source and Destination Addresses

The `accounts` function sets the source address, the destination address, and the signer(s) required for the transfer. **Must call the `destination` function first**.

### Parameters {#accounts-parameters}
|         Name         |        Type        |                          Description                          |
|:--------------------:|:------------------:|:-------------------------------------------------------------:|
|   `sourceAddress`    |      *string*      |    The address of the sending account on the source chain     |
| `destinationAddress` |      *string*      | The address of the receiving account on the destination chain |
|      `signers?`      | *Partial(signers)* | The Ethers or Polkadot signers required to sign transactions  |

### Returns {#accounts-returns}

Please refer to the returns section of the [`getTransferData()` method](#:~:text=getTransferData()) for information on the returned transfer data.

## Get the Transfer Data for a Swap

The `swap` function returns the transfer data necessary to swap the asset from the destination chain back to the source chain. This function is exposed in the response of calling the `getTransferData` function or by building the transfer data starting with the `assets` function.

### Parameters {#swap-parameters}

None.

### Returns {#swap-returns}

Please refer to the returns section of the [`getTransferData()` method](#:~:text=getTransferData()) for information on the returned transfer data. Keep in mind that with the `swap` function, the `source` and `destination` in the original transfer data has been swapped.

## Initiate a Transfer

The `transfer` function transfers a given amount of the asset from the source chain to the destination chain.

### Parameters {#transfer-parameters}

|   Name   |             Type             |                                  Description                                  |
|:--------:|:----------------------------:|:-----------------------------------------------------------------------------:|
| `amount` | *bigint \| number \| string* | The amount of the asset to transfer between the source and destination chains |

### Returns {#transfer-returns}

| Name |       Type        |                        Description                        |
|:----:|:-----------------:|:---------------------------------------------------------:|
|  -   | *Promise(string)* | The transaction hash for the transfer on the source chain |

## Get Estimate of the Asset Received on the Destination Chain

The `getEstimate()` function returns an estimated amount of the asset that will be received on the destination chain, less any destination fees.

### Parameters {#get-estimate-parameters}

|   Name   |        Type        |                                  Description                                  |
|:--------:|:------------------:|:-----------------------------------------------------------------------------:|
| `amount` | *number \| string* | The amount of the asset to transfer between the source and destination chains |

### Returns {#get-estimate-returns}

| Name |                   Type                    |                                    Description                                    |
|:----:|:-----------------------------------------:|:---------------------------------------------------------------------------------:|
|  -   | [*AssetAmount*](#the-asset-amount-object) | An estimated amount of the asset that will be received by the destination address |

## Convert A Value to A Decimal

The `toDecimal()` function converts an `AssetAmount` to a decimal. The number to convert to decimal format and the number of decimals the asset uses are pulled automatically from the `AssetAmount`.

### Parameters {#to-decimal-parameters}

|     Name      |      Type      |                                                                    Description                                                                     |
|:-------------:|:--------------:|:--------------------------------------------------------------------------------------------------------------------------------------------------:|
| `maxDecimal?` |    *number*    |                                          The maximum number of decimal places to use. The default is `6`                                           |
| `roundType?`  | *RoundingMode* | Accepts an index that dictates the [rounding method](https://mikemcl.github.io/big.js/#rm){target=\_blank} to use based on the `RoundingMode` enum |

Where the `RoundingMode` enum is defined as:

```js
enum RoundingMode {
  RoundDown = 0,
  RoundHalfUp = 1,
  RoundHalfEven = 2,
  RoundUp = 3
}
```

### Returns {#to-decimal-returns}

| Name |   Type   |            Description             |
|:----:|:--------:|:----------------------------------:|
|  -   | *string* | The given amount in decimal format |

## Convert a Value to a Big Number

The `toBig` function converts an `AssetAmount` to a big number.

### Parameters {#to-big-parameters}

None.

### Returns {#to-big-returns}

| Name | Type  |              Description              |
|:----:|:-----:|:-------------------------------------:|
|  -   | *Big* | The given amount in big number format |

## Convert a Value to a Big Number Decimal

The `toBigDecimal` function converts an `AssetAmount` to a decimal and then to a big number. The number to convert to decimal format and the number of decimals the asset uses are pulled automatically from the `AssetAmount`.

### Parameters {#to-big-decimal-parameters}

|     Name      |      Type      |                                                                    Description                                                                     |
|:-------------:|:--------------:|:--------------------------------------------------------------------------------------------------------------------------------------------------:|
| `maxDecimal?` |    *number*    |                                          The maximum number of decimal places to use. The default is `6`                                           |
| `roundType?`  | *RoundingMode* | Accepts an index that dictates the [rounding method](https://mikemcl.github.io/big.js/#rm){target=\_blank} to use based on the `RoundingMode` enum |

Where the `RoundingMode` enum is defined as:

```js
enum RoundingMode {
  RoundDown = 0,
  RoundHalfUp = 1,
  RoundHalfEven = 2,
  RoundUp = 3
}
```

### Returns {#to-big-decimal-returns}

| Name | Type  |                  Description                  |
|:----:|:-----:|:---------------------------------------------:|
|  -   | *Big* | The given amount in big number decimal format |
