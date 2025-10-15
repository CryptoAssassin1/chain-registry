# Comprehensive chain.json Guide

## Overview
`chain.json` is the core metadata file that contains information about a blockchain in the Cosmos Chain Registry. This guide documents all properties, validation rules, and best practices based on the schema and real-world examples.

---

## Table of Contents
1. [Required Properties](#required-properties)
2. [Core Identification Properties](#core-identification-properties)
3. [Network & Status Properties](#network--status-properties)
4. [Blockchain Configuration](#blockchain-configuration)
5. [Fees & Staking](#fees--staking)
6. [Codebase Information](#codebase-information)
7. [Network Connectivity](#network-connectivity)
8. [Images & Branding](#images--branding)
9. [Validation Rules](#validation-rules)
10. [Common Patterns](#common-patterns)
11. [Examples](#examples)

---

## Required Properties

According to `chain.schema.json` lines 7-7, only **2 properties** are REQUIRED:

### ✅ Always Required
```json
{
  "chain_name": "example",     // REQUIRED
  "chain_type": "cosmos"       // REQUIRED
}
```

### ⚠️ Conditionally Required

**IF `chain_type` is "cosmos":**
```json
{
  "chain_type": "cosmos",
  "bech32_prefix": "osmo"     // REQUIRED for cosmos chains
}
```

**IF `chain_type` is "cosmos" OR "eip155":**
```json
{
  "chain_type": "cosmos",
  "chain_id": "osmosis-1"     // REQUIRED for cosmos and eip155 chains
}
```

---

## Core Identification Properties

### 1. `$schema` (Optional)
**Type:** `string`
**Pattern:** `^(\\.\\./)+chain\\.schema\\.json$`
**Purpose:** Points to the schema file for validation

```json
"$schema": "../chain.schema.json"        // Mainnet
"$schema": "../../chain.schema.json"     // Testnet
```

---

### 2. `chain_name` (REQUIRED)
**Type:** `string`
**Pattern:** `[a-z0-9]+` (lowercase alphanumeric only, no hyphens/underscores)
**Purpose:** Unique identifier for the chain

```json
"chain_name": "osmosis"      // ✅ Valid
"chain_name": "cosmoshub"    // ✅ Valid
"chain_name": "cosmos-hub"   // ❌ Invalid (no hyphens)
"chain_name": "Osmosis"      // ❌ Invalid (no uppercase)
```

---

### 3. `chain_type` (REQUIRED)
**Type:** `string` (enum)
**Options:**
- `cosmos` - Cosmos SDK chains
- `eip155` - Ethereum/EVM chains
- `bip122` - Bitcoin-based chains
- `polkadot`, `solana`, `algorand`, `arweave`, `ergo`, `fil`, `hedera`, `monero`, `reef`, `stacks`, `starknet`, `stellar`, `tezos`, `vechain`, `waves`, `xrpl`, `unknown`

```json
"chain_type": "cosmos"       // Most common for Cosmos chains
```

---

### 4. `chain_id` (Conditionally Required)
**Type:** `string`
**Purpose:** On-chain identifier
**Required for:** `cosmos` and `eip155` chains

```json
"chain_id": "osmosis-1"      // Osmosis mainnet
"chain_id": "cosmoshub-4"    // Cosmos Hub
"chain_id": "osmo-test-5"    // Osmosis testnet
```

---

### 5. `pretty_name` (Optional)
**Type:** `string`
**Purpose:** Human-readable display name

```json
"chain_name": "osmosis",
"pretty_name": "Osmosis"
```

---

### 6. `website` (Optional)
**Type:** `string` (URI format)
**Purpose:** Official website URL

```json
"website": "https://osmosis.zone/"
```

---

## Network & Status Properties

### 7. `status` (Optional)
**Type:** `string` (enum)
**Options:** `"live"`, `"upcoming"`, `"killed"`

```json
"status": "live"      // Production network
"status": "upcoming"  // Not yet launched
"status": "killed"    // Deprecated/shut down
```

---

### 8. `network_type` (Optional)
**Type:** `string` (enum)
**Options:** `"mainnet"`, `"testnet"`, `"devnet"`

```json
"network_type": "mainnet"
```

---

## Blockchain Configuration

### 9. `bech32_prefix` (Required for Cosmos)
**Type:** `string`
**Purpose:** Human-readable part of addresses
**Must be:** Registered with SLIP-0173

```json
"bech32_prefix": "osmo"     // Osmosis addresses: osmo1...
"bech32_prefix": "cosmos"   // Cosmos Hub addresses: cosmos1...
```

---

### 10. `bech32_config` (Optional)
**Type:** `object`
**Purpose:** Override bech32_prefix for specific use cases
**Properties:** (all optional)
- `bech32PrefixAccAddr` - Account addresses (e.g., "cosmos")
- `bech32PrefixAccPub` - Account public keys (e.g., "cosmospub")
- `bech32PrefixValAddr` - Validator addresses (e.g., "cosmosvaloper")
- `bech32PrefixValPub` - Validator public keys (e.g., "cosmosvaloperpub")
- `bech32PrefixConsAddr` - Consensus addresses (e.g., "cosmosvalcons")
- `bech32PrefixConsPub` - Consensus public keys (e.g., "cosmosvalconspub")

```json
"bech32_config": {
  "bech32PrefixAccAddr": "cosmos",
  "bech32PrefixAccPub": "cosmospub",
  "bech32PrefixValAddr": "cosmosvaloper",
  "bech32PrefixValPub": "cosmosvaloperpub",
  "bech32PrefixConsAddr": "cosmosvalcons",
  "bech32PrefixConsPub": "cosmosvalconspub"
}
```

---

### 11. `daemon_name` (Optional)
**Type:** `string`
**Purpose:** Name of the blockchain daemon/binary

```json
"daemon_name": "osmosisd"
"daemon_name": "gaiad"
```

---

### 12. `node_home` (Optional)
**Type:** `string`
**Purpose:** Default home directory for node data

```json
"node_home": "$HOME/.osmosisd"
"node_home": "$HOME/.gaia"
```

---

### 13. `key_algos` (Optional)
**Type:** `array` of strings (enum)
**Options:** `"secp256k1"`, `"ethsecp256k1"`, `"ed25519"`, `"sr25519"`, `"bn254"`
**Unique items only**

```json
"key_algos": ["secp256k1"]                    // Most common
"key_algos": ["secp256k1", "ethsecp256k1"]   // Multiple algos
```

---

### 14. `slip44` (Optional but Recommended for Mainnets)
**Type:** `number`
**Purpose:** Coin type for HD wallet derivation path
**Common values:**
- `118` - Standard Cosmos chains
- `60` - Ethereum-compatible chains
- `330` - Terra chains

```json
"slip44": 118   // Standard Cosmos
```

**⚠️ Important:** Live mainnets should always include `slip44`. Missing this on a mainnet is a common validation error.

---

### 15. `alternative_slip44s` (Optional)
**Type:** `array` of numbers
**Purpose:** Alternative coin types supported

```json
"alternative_slip44s": [60, 330]
```

---

## Fees & Staking

### 16. `fees` (Optional)
**Type:** `object`
**Required subproperty:** `fee_tokens` (array)

#### Fee Token Structure:
**Required:** `denom`
**Optional:** `fixed_min_gas_price`, `low_gas_price`, `average_gas_price`, `high_gas_price`, `gas_costs`

```json
"fees": {
  "fee_tokens": [
    {
      "denom": "uosmo",
      "fixed_min_gas_price": 0.01,
      "low_gas_price": 0.01,
      "average_gas_price": 0.1,
      "high_gas_price": 0.16
    },
    {
      "denom": "ibc/1234ABCD...",
      // Gas prices are optional for additional fee tokens
    }
  ]
}
```

#### Gas Costs (Optional):
```json
"gas_costs": {
  "cosmos_send": 100000,
  "ibc_transfer": 200000
}
```

---

### 17. `staking` (Optional)
**Type:** `object`
**Required subproperty:** `staking_tokens` (array)
**Optional subproperty:** `lock_duration`

```json
"staking": {
  "staking_tokens": [
    {
      "denom": "uosmo"    // Required
    }
  ],
  "lock_duration": {
    "blocks": 403200,           // Optional: number of blocks
    "time": "1209600s"          // Optional: approximate time
  }
}
```

---

## Codebase Information

### 18. `codebase` (Optional)
**Type:** `object`
**Properties:** (all optional)

```json
"codebase": {
  "git_repo": "https://github.com/osmosis-labs/osmosis",  // URI
  "recommended_version": "30.0.0",                        // Current version
  "compatible_versions": ["30.0.0", "29.0.1"],           // Array
  "tag": "v30.0.0",                                       // Git tag (pattern: ^[A-Za-z0-9._/@-]+$)
  "language": { ... },     // See below
  "binaries": { ... },     // See below
  "sdk": { ... },          // See below
  "consensus": { ... },    // See below
  "cosmwasm": { ... },     // See below
  "ibc": { ... },          // See below
  "genesis": { ... }       // See below
}
```

---

#### 18a. `language` (Optional)
**Required:** `type` (enum: `"go"`, `"rust"`, `"solidity"`, `"other"`)
**Optional:** `version`, `repo`, `tag`

```json
"language": {
  "type": "go",
  "version": "1.23.4"
}
```

---

#### 18b. `binaries` (Optional)
**Supported platforms:** `linux/amd64`, `linux/arm64`, `darwin/amd64`, `darwin/arm64`, `windows/amd64`, `windows/arm64`

```json
"binaries": {
  "linux/amd64": "https://github.com/.../osmosisd-30.0.0-linux-amd64",
  "linux/arm64": "https://github.com/.../osmosisd-30.0.0-linux-arm64",
  "darwin/amd64": "https://github.com/.../osmosisd-30.0.0-darwin-amd64",
  "darwin/arm64": "https://github.com/.../osmosisd-30.0.0-darwin-arm64"
}
```

---

#### 18c. `sdk` (Optional)
**Required:** `type` (enum: `"cosmos"`, `"penumbra"`, `"other"`)
**Optional:** `version`, `repo`, `tag`

```json
"sdk": {
  "type": "cosmos",
  "version": "0.50.14",
  "repo": "https://github.com/osmosis-labs/cosmos-sdk",
  "tag": "v0.50.14-v30-osmo"
}
```

---

#### 18d. `consensus` (Optional)
**Required:** `type` (enum: `"tendermint"`, `"cometbft"`, `"sei-tendermint"`, `"cometbls"`)
**Optional:** `version`, `repo`, `tag`

```json
"consensus": {
  "type": "cometbft",
  "version": "0.38.17",
  "repo": "https://github.com/osmosis-labs/cometbft",
  "tag": "v0.38.17-v28-osmo-1"
}
```

---

#### 18e. `cosmwasm` (Optional)
**Properties:** `version`, `repo`, `tag`, `enabled` (boolean), `path`

```json
"cosmwasm": {
  "version": "0.53.3",
  "repo": "https://github.com/CosmWasm/wasmd",
  "tag": "v0.53.3",
  "enabled": true,
  "path": "$HOME/.osmosisd/data/wasm"  // Must match pattern: ^\\$HOME.*$
}
```

---

#### 18f. `ibc` (Optional)
**Required:** `type` (enum: `"go"`, `"rust"`, `"other"`)
**Optional:** `version`, `repo`, `tag`, `ics_enabled`

```json
"ibc": {
  "type": "go",
  "version": "8.7.0",
  "repo": "https://github.com/cosmos/ibc-go",
  "tag": "v8.7.0",
  "ics_enabled": ["ics20-1", "ics27-1"]  // Enum: "ics20-1", "ics27-1", "mauth"
}
```

---

#### 18g. `genesis` (Optional)
**Required:** `genesis_url` (URI)
**Optional:** `name`, `ics_ccv_url`

```json
"genesis": {
  "name": "v3",
  "genesis_url": "https://github.com/osmosis-labs/networks/raw/main/osmosis-1/genesis.json",
  "ics_ccv_url": "https://example.com/ccv-state.json"  // For consumer chains
}
```

---

## Network Connectivity

### 19. `peers` (Optional)
**Type:** `object`
**Properties:** `seeds`, `persistent_peers` (both arrays)

#### Peer Object Structure:
**Required:** `id`, `address`
**Optional:** `provider`

```json
"peers": {
  "seeds": [
    {
      "id": "f515a8599b40f0e84dfad935ba414674ab11a668",
      "address": "osmosis.blockpane.com:26656",
      "provider": "blockpane"
    }
  ],
  "persistent_peers": [
    {
      "id": "4d9ac3510d9f5cfc975a28eb2a7b8da866f7bc47",
      "address": "37.187.38.191:26656",
      "provider": "stakelab"
    }
  ]
}
```

---

### 20. `apis` (Optional)
**Type:** `object`
**Properties:** `rpc`, `rest`, `grpc`, `wss`, `grpc-web`, `evm-http-jsonrpc` (all arrays)

#### Endpoint Object Structure:
**Required:** `address`
**Optional:** `provider`, `archive` (boolean, default false)

```json
"apis": {
  "rpc": [
    {
      "address": "https://rpc.osmosis.zone/",
      "provider": "Osmosis Foundation",
      "archive": false
    }
  ],
  "rest": [
    {
      "address": "https://lcd.osmosis.zone/",
      "provider": "Osmosis Foundation"
    }
  ],
  "grpc": [
    {
      "address": "osmosis.lavenderfive.com:443",
      "provider": "Lavender.Five Nodes 🐝"
    }
  ],
  "wss": [],
  "grpc-web": [],
  "evm-http-jsonrpc": []
}
```

---

### 21. `explorers` (Optional)
**Type:** `array` of explorer objects
**Properties:** `kind`, `url`, `tx_page`, `account_page`, `validator_page`, `proposal_page`, `block_page`

```json
"explorers": [
  {
    "kind": "mintscan",
    "url": "https://www.mintscan.io/osmosis",
    "tx_page": "https://www.mintscan.io/osmosis/transactions/${txHash}",
    "account_page": "https://www.mintscan.io/osmosis/accounts/${accountAddress}",
    "validator_page": "https://www.mintscan.io/osmosis/validators/${validatorAddress}",
    "proposal_page": "https://www.mintscan.io/osmosis/proposals/${proposalId}",
    "block_page": "https://www.mintscan.io/osmosis/blocks/${blockHeight}"
  }
]
```

**Template variables:**
- `${txHash}` - Transaction hash
- `${accountAddress}` - Account address
- `${validatorAddress}` - Validator address
- `${proposalId}` - Proposal ID
- `${blockHeight}` - Block height


---

## Images & Branding

### 22. `logo_URIs` (Optional)
**Type:** `object`
**Properties:** `png`, `svg` (URI references)
**Pattern:** Must follow GitHub raw URL format

```json
"logo_URIs": {
  "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/osmosis/images/osmo.png",
  "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/osmosis/images/osmo.svg"
}
```

**Pattern:**
```
^https://raw\\.githubusercontent\\.com/cosmos/chain-registry/master/(|testnets/|_non-cosmos/)[a-z0-9]+/images/.+\\.(png|svg)$
```

---

### 23. `images` (Optional)
**Type:** `array` of image objects
**Must have:** At least one of `png` or `svg`
**Optional:** `image_sync`, `theme`

```json
"images": [
  {
    "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/osmosis/images/osmo.png",
    "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/osmosis/images/osmo.svg",
    "theme": {
      "circle": true,
      "dark_mode": false,
      "monochrome": false
    }
  }
]
```

#### Using `image_sync` (for cross-chain references):
```json
"images": [
  {
    "image_sync": {
      "chain_name": "osmosis",
      "base_denom": "uosmo"
    },
    "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/osmosis/images/osmo.png"
  }
]
```

---

### 24. `description` (Optional)
**Type:** `string`
**Min length:** 1
**Max length:** 3000 characters

```json
"description": "Osmosis (OSMO) is the premier DEX and cross-chain DeFi hub within the Cosmos ecosystem..."
```

---

### 25. `keywords` (Optional)
**Type:** `array` of strings
**Max items:** 20

```json
"keywords": ["dex", "defi", "amm"]
```

---

### 26. `extra_codecs` (Optional)
**Type:** `array` of strings (enum)
**Options:** `"ethermint"`, `"injective"`
**Unique items only**

```json
"extra_codecs": ["ethermint"]
```

---

### 27. `pre_fork_chain_name` (Optional)
**Type:** `string`
**Pattern:** `[a-z0-9]+`
**Purpose:** Reference to chain before a hard fork

```json
"pre_fork_chain_name": "terra"   // For Terra 2.0 referencing Terra Classic
```

---

## Validation Rules

### Pattern Validations

1. **chain_name:** `[a-z0-9]+` (lowercase alphanumeric only)
2. **version:** `^v?\\d+(\\.\\d+){0,2}$` (e.g., "v1.0.0" or "1.0.0")
3. **tag:** `^[A-Za-z0-9._/@-]+$` (Git tags)
4. **cosmwasm path:** `^\\$HOME.*$` (Must start with $HOME)
5. **image URLs:** Must match GitHub raw URL pattern

### Conditional Requirements

```javascript
// IF chain_type = "cosmos" THEN bech32_prefix IS REQUIRED
if (chain_type === "cosmos") {
  required.push("bech32_prefix");
}

// IF chain_type = "cosmos" OR "eip155" THEN chain_id IS REQUIRED
if (chain_type === "cosmos" || chain_type === "eip155") {
  required.push("chain_id");
}
```

### Nested Object Requirements

- `fees.fee_tokens[].denom` - REQUIRED if fees object exists
- `staking.staking_tokens[].denom` - REQUIRED if staking object exists
- `codebase.genesis.genesis_url` - REQUIRED if genesis object exists
- `codebase.sdk.type` - REQUIRED if sdk object exists
- `codebase.consensus.type` - REQUIRED if consensus object exists
- `codebase.ibc.type` - REQUIRED if ibc object exists
- `codebase.language.type` - REQUIRED if language object exists
- `peers.seeds[].id` - REQUIRED for each seed
- `peers.seeds[].address` - REQUIRED for each seed
- `peers.persistent_peers[].id` - REQUIRED for each persistent peer
- `peers.persistent_peers[].address` - REQUIRED for each persistent peer
- `apis.*.address` - REQUIRED for each endpoint
- `images[]` - Must have at least one of `png` or `svg`

---

## Common Patterns

### Minimal Valid chain.json (Non-Cosmos)
```json
{
  "chain_name": "bitcoin",
  "chain_type": "bip122"
}
```

### Minimal Valid chain.json (Cosmos)
```json
{
  "chain_name": "examplechain",
  "chain_type": "cosmos",
  "chain_id": "example-1",
  "bech32_prefix": "example"
}
```

### Typical Mainnet chain.json
```json
{
  "$schema": "../chain.schema.json",
  "chain_name": "examplechain",
  "chain_type": "cosmos",
  "chain_id": "example-1",
  "website": "https://example.com",
  "pretty_name": "Example Chain",
  "status": "live",
  "network_type": "mainnet",
  "bech32_prefix": "example",
  "daemon_name": "exampled",
  "node_home": "$HOME/.exampled",
  "key_algos": ["secp256k1"],
  "slip44": 118,
  "fees": {
    "fee_tokens": [
      {
        "denom": "uexample",
        "fixed_min_gas_price": 0.01,
        "low_gas_price": 0.01,
        "average_gas_price": 0.025,
        "high_gas_price": 0.03
      }
    ]
  },
  "staking": {
    "staking_tokens": [
      {
        "denom": "uexample"
      }
    ]
  },
  "codebase": {
    "git_repo": "https://github.com/example/example",
    "recommended_version": "v1.0.0",
    "compatible_versions": ["v1.0.0"],
    "consensus": {
      "type": "cometbft",
      "version": "0.38.11"
    },
    "sdk": {
      "type": "cosmos",
      "version": "0.50.9"
    },
    "ibc": {
      "type": "go",
      "version": "8.5.1"
    },
    "genesis": {
      "genesis_url": "https://example.com/genesis.json"
    }
  },
  "logo_URIs": {
    "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/examplechain/images/example.png"
  },
  "peers": {
    "seeds": [
      {
        "id": "abc123...",
        "address": "seed.example.com:26656",
        "provider": "Example Foundation"
      }
    ]
  },
  "apis": {
    "rpc": [
      {
        "address": "https://rpc.example.com",
        "provider": "Example Foundation"
      }
    ],
    "rest": [
      {
        "address": "https://api.example.com",
        "provider": "Example Foundation"
      }
    ]
  },
  "explorers": [
    {
      "kind": "mintscan",
      "url": "https://www.mintscan.io/examplechain",
      "tx_page": "https://www.mintscan.io/examplechain/transactions/${txHash}"
    }
  ]
}
```

---

## Common Mistakes

### 1. ❌ Using uppercase or hyphens in chain_name
```json
"chain_name": "Example-Chain"   // WRONG
"chain_name": "examplechain"    // CORRECT
```

### 2. ❌ Missing required fields for Cosmos chains
```json
{
  "chain_name": "example",
  "chain_type": "cosmos"
  // Missing: chain_id, bech32_prefix
}
```

### 3. ❌ Missing slip44 for live mainnets
```json
{
  "status": "live",
  "network_type": "mainnet"
  // Should have: "slip44": 118
}
```

### 4. ❌ Wrong image URL format
```json
"png": "https://example.com/logo.png"  // WRONG - must be GitHub raw URL
"png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/examplechain/images/example.png"  // CORRECT
```

### 5. ❌ Invalid version format
```json
"version": "version-1.0.0"    // WRONG
"version": "v1.0.0"           // CORRECT
"version": "1.0.0"            // ALSO CORRECT
```

### 6. ❌ Missing required nested properties
```json
"fees": {
  "fee_tokens": [
    {
      // Missing "denom" - REQUIRED
      "low_gas_price": 0.01
    }
  ]
}
```

---

## Schema Location

**Mainnet:** `/chain.schema.json` (root level)
**Reference:** `"$schema": "../chain.schema.json"`

**Testnet:** `/chain.schema.json` (root level)
**Reference:** `"$schema": "../../chain.schema.json"`

---

## Additional Resources

- **Schema file:** `chain.schema.json` (lines 1-742)
- **Real examples:**
  - Osmosis: `osmosis/chain.json`
  - Cosmos Hub: `cosmoshub/chain.json`
- **SLIP-0173:** https://github.com/satoshilabs/slips/blob/master/slip-0173.md
- **CAIP-2 Namespaces:** https://github.com/ChainAgnostic/namespaces

---

## Quick Reference Table

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `chain_name` | ✅ | string | Lowercase alphanumeric only |
| `chain_type` | ✅ | enum | "cosmos", "eip155", etc. |
| `chain_id` | ⚠️ | string | Required for cosmos/eip155 |
| `bech32_prefix` | ⚠️ | string | Required for cosmos chains |
| `slip44` | ⚠️ | number | Highly recommended for mainnets |
| `status` | ❌ | enum | "live", "upcoming", "killed" |
| `network_type` | ❌ | enum | "mainnet", "testnet", "devnet" |
| `pretty_name` | ❌ | string | Display name |
| `website` | ❌ | URI | Official URL |
| `fees` | ❌ | object | Fee token information |
| `staking` | ❌ | object | Staking token information |
| `codebase` | ❌ | object | Version & build information |
| `peers` | ❌ | object | Seeds & persistent peers |
| `apis` | ❌ | object | RPC, REST, gRPC endpoints |
| `explorers` | ❌ | array | Block explorer links |
| `logo_URIs` | ❌ | object | PNG/SVG logos |
| `images` | ❌ | array | Extended image metadata |
| `description` | ❌ | string | Max 3000 chars |
| `keywords` | ❌ | array | Max 20 items |

Legend:
- ✅ = Always required
- ⚠️ = Conditionally required
- ❌ = Optional

---

**Last Updated:** 2025-10-15

