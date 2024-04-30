---
title: XCM SDK Reference for v1
description: A reference for the interfaces and methods in the Moonbeam XCM SDK that can be used to send XCM transfers between chains within the Polkadot/Kusama ecosystems. TODO-update
---

# Core Interfaces and Types

Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.

## Assets

### The Asset Object

<div class="grid" markdown>
<div markdown>
The Asset object defines an asset's key and symbol used on the asset's origin chain.

**Attributes**

- `key` ++"string"++ - Identifies an asset
- `originSymbol` ++"string"++ - The symbol of the asset on the asset's origin chain
</div>

```js title="Example"
// The Asset object
{
  key: 'glmr',
  originSymbol: 'GLMR'
}
```
</div>

---

### The Asset Amount Object

<div class="grid" markdown>
<div markdown>

The Asset Amount object defines properties related to an asset, including `Asset` properties, the decimals and symbol of the asset, and the amount an associated source or destination address has of the asset.

!!! note
    There are a few utility methods that are available for working with the `AssetAmount` class that convert the amount to various formats. Please refer to the [Methods for Asset Conversions](#utility-functions) section.

**Attributes**

- `key` ++"string"++ - Identifies an asset
- `originSymbol` ++"string"++ - The symbol of the asset on the asset's origin chain
- `amount` ++"bigint"++ - Identifies a particular amount of the asset (i.e., balance, minimum, maximum, etc.)
- `decimals` ++"number"++ - The number of decimals the asset has
- `symbol` ++"string"++ - The symbol of the asset

</div>

```js title="Example"
// The Asset Amount object
{
  key: 'glmr',
  originSymbol: 'GLMR',
  amount: 0n,
  decimals: 18,
  symbol: 'GLMR'
}
```

</div>

---

## Chains

### The Chain Object

<div class="grid" markdown>
<div markdown>
The Chain object defines properties related to a chain, used to define the source and destination chains. If a chain is an EVM parachain, there are a couple of additional properties.

**Attributes**

- `ecosystem` ++"Ecosystem"++ - Identifies the ecosystem the chain belongs to: `polkadot`, `kusama`, or `alphanet-relay`
- `isTestChain` ++"boolean"++ - Whether the chain is a TestNet
- `key` ++"string"++ - Identifies a chain
- `name` ++"string"++ - The name of the chain
- `type` ++"ChainType"++ - The type of the chain: `parachain` or `evm-parachain`
- `assetsData` ++"Map<string, ChainAssetsData>"++ - A list of the assets that the chain supports
- `genesisHash` ++"string"++ - The hash of the genesis block
- `parachainId` ++"number"++ - The ID of the parachain
- `ss58Format` ++"number"++ - The [ss58 format](https://polkadot.js.org/docs/keyring/start/ss58/) for the chain
- `usesChainDecimals` ++"boolean"++ - A flag indicating if the chain uses its own decimals in balance queries for all the assets. Defaults to `false`
- `ws` ++"string"++ - The WebSocket endpoint for the chain
- `id` ++"number"++ - **For EVM parachains only** - The chain ID
- `rpc` ++"string"++ - **For EVM parachains only** - The HTTP RPC endpoint for the chain

**The Ecosystem Type**

The _Ecosystem_ type is used to specify the relay chain ecosystem a chain belongs to and can be any of the following:

```ts
enum Ecosystem {
  Polkadot = 'polkadot',
  Kusama = 'kusama',
  AlphanetRelay = 'alphanet-relay',
}
```

**The Chain Type**

The _ChainType_ type is used to specify what kind of parachain a chain is, as defined below:

```ts
export enum ChainType {
  'Parachain' = 'parachain',
  'EvmParachain' = 'evm-parachain',
}
```
</div>

```js title="Example"
// The Chain object
{
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
}
```

</div>

---

### The Chain Assets Data Object

<div class="grid" markdown>
<div markdown>

The Chain Assets Data object defines the information needed to target the asset on the chain. This is mostly for internal usage to accommodate how different chains store their assets. The SDK defaults to the asset ID if certain properties do not apply to the given chain.

**Attributes**

- `asset` ++"Asset"++ - The asset's key and origin symbol
- `balanceId` ++"ChainAssetId"++ - The balance ID of the asset. Defaults to the asset ID
- `decimals` ++"number"++ - The number of decimals the asset has
- `id` ++"ChainAssetId"++ - The asset ID
- `metadataId` ++"ChainAssetId"++ - The metadata ID of the asset
- `minId` ++"ChainAssetId"++ -  The minimum ID of the asset
- `palletInstance` ++"number"++ - The number of the pallet instance the asset belongs to
- `min` - ++"number"++ - The minimum amount of the asset that is required to be left in the account for it to be active. Similar to the existential deposit except it is for non-native assets

**The Chain Asset ID Type**

The _ChainAssetId_ is a generic type used to specify the location of the asset on the chain, which is different on every chain and is defined as:

```ts
type ChainAssetId =
  | string
  | number
  | bigint
  | { [key: string]: ChainAssetId };
```

</div>

```js title="Example"
// The Chain Assets Data object
{

}
```

</div>

---

## Transfer Data

### The Transfer Data Object

The Transfer Data object defines the complete transfer data for transferring an asset, including asset, source chain, and destination chain information, and a few helper functions for the transfer process.

#### Attributes {: #transfer-data-attributes}

|       Name       |                                     Type                                      |                                       Description                                       |
|:----------------:|:-----------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------:|
|  `destination`   | [*DestinationChainTransferData*](#the-destination-chain-transfer-data-object) |                 The assembled destination chain and address information                 |
|  `getEstimate`   |                                   function                                    | Gets the estimated amount of the asset that will be received by the destination address |
| `isSwapPossible` |                                   *boolean*                                   |                       Returns whether or not the swap is possible                       |
|      `max`       |                   [*AssetAmount*](#the-asset-amount-object)                   |                The maximum amount of the asset that *can* be transferred                |
|      `min`       |                   [*AssetAmount*](#the-asset-amount-object)                   |                The minimum amount of the asset that *can* be transferred                |
|     `source`     |      [*SourceChainTransferData*](#the-source-chain-transfer-data-object)      |                   The assembled source chain and address information                    |
|      `swap`      |                                   function                                    |    Swaps the destination and the source chains and returns the swapped transfer data    |
|    `transfer`    |                                   function                                    |  Transfers a given amount of the asset from the source chain to the destination chain   |

#### Example {: #transfer-data-example}

```js
// The Transfer Data object
// for sending DOT from Polkadot to Moonbeam
{
  destination: {
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
    destinationFeeBalance: e {
      key: 'dot',
      originSymbol: 'DOT',
      amount: 0n,
      decimals: 10,
      symbol: 'DOT'
    },
    existentialDeposit: e {
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

### The Destination Chain Transfer Data Object

The Destination Chain Transfer Data object defines the destination chain data for the transfer.

#### Attributes {: #destination-chain-transfer-data-attributes}

|         Name         |                   Type                    |                                                                                 Description                                                                                  |
|:--------------------:|:-----------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|      `balance`       | [*AssetAmount*](#the-asset-amount-object) |                                                    The balance of the asset being transferred on the destination address                                                     |
|       `chain`        |                *AnyChain*                 |                                                                      The destination chain information                                                                       |
| `existentialDeposit` | [*AssetAmount*](#the-asset-amount-object) |                                               The existential deposit for the asset being transferred on the destination chain                                               |
|        `fee`         | [*AssetAmount*](#the-asset-amount-object) |                                                 The amount of fees for the asset being transferred on the destination chain                                                  |
|        `min`         | [*AssetAmount*](#the-asset-amount-object) | The minimum amount of the asset to transfer. This is different than `TransferData.min`, as this dictates the minimum amount that should be received on the destination chain |

#### Example {: #destination-chain-transfer-data-example}

```js
// The Destination Chain Transfer Data object
// For Polkadot 
{
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
}
```

### The Source Chain Transfer Data Object

The Source Chain Transfer Data object defines the source chain data for the transfer.

#### Attributes {: #source-chain-transfer-data-attributes}

|          Name           |                   Type                    |                                                                      Description                                                                       |
|:-----------------------:|:-----------------------------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------:|
|        `balance`        | [*AssetAmount*](#the-asset-amount-object) |                                           The balance of the asset being transferred for the source address                                            |
|         `chain`         |      [*AnyChain*](#the-chain-object)      |                                                              The source chain information                                                              |
| `destinationFeeBalance` | [*AssetAmount*](#the-asset-amount-object) |                                         The balance of the asset used to pay for fees in the destination chain                                         |
|  `existentialDeposit`   | [*AssetAmount*](#the-asset-amount-object) |                                      The existential deposit for the asset being transferred on the source chain                                       |
|          `fee`          | [*AssetAmount*](#the-asset-amount-object) |                                         The amount of fees for the asset being transferred on the source chain                                         |
|      `feeBalance`       | [*AssetAmount*](#the-asset-amount-object) |                                             The balance of the asset being transferred on the source chain                                             |
|          `min`          | [*AssetAmount*](#the-asset-amount-object) | The minimum amount of the asset that should be kept on the source chain, taking into consideration the `existentialDeposit` and `fee` for the transfer |
|          `max`          | [*AssetAmount*](#the-asset-amount-object) |                                               The maximum amount of the asset that *can* be transferred                                                |

#### Example {: #source-chain-transfer-data-example}

```js
// The Source Chain Transfer Data object
{
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
}
```
