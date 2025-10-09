# 📸 Image Guidelines for Cosmos Chain Registry

Complete guide for adding, managing, and optimizing images in the chain registry.

---

## Table of Contents

1. [Quick Rules](#quick-rules)
2. [Image Requirements](#image-requirements)
3. [Before You Upload: Check for Duplicates](#before-you-upload-check-for-duplicates)
4. [When to Upload New Images](#when-to-upload-new-images)
5. [When to Use image_sync](#when-to-use-image_sync)
6. [Image Optimization Guide](#image-optimization-guide)
7. [Testing Images](#testing-images)
8. [Common Image Mistakes](#common-image-mistakes)
9. [Examples](#examples)

---

## Quick Rules

**✅ DO:**
- Make images square (width = height)
- Keep images under 250 KB
- Check for existing images first
- Use `image_sync` for cross-chain tokens
- Provide both PNG and SVG when possible
- Use descriptive filenames (atom.png, osmo.svg)

**❌ DON'T:**
- Upload non-square images
- Upload images larger than 250 KB
- Duplicate existing images
- Use screenshots or low-quality images
- Forget to add images to assetlist.json

---

## Image Requirements

### 1. Dimensions: MUST Be Square

**Rule:** Width MUST equal Height (±1px tolerance)

```bash
# Check dimensions
file image.png
identify image.png

# Examples:
✅ 256x256   - GOOD
✅ 512x512   - GOOD
✅ 1024x1024 - GOOD
✅ 2000x2000 - GOOD (but check file size)

❌ 512x256   - REJECTED (not square)
❌ 1920x1080 - REJECTED (not square)
❌ 800x600   - REJECTED (not square)
```

**Why?** Ensures consistent display across all applications and wallets.

---

### 2. File Size: MUST Be < 250 KB

**Rule:** Maximum 250 KB (251 KB with 1KB tolerance)

```bash
# Check file size
ls -lh image.png

# Examples:
✅ 48 KB    - EXCELLENT
✅ 120 KB   - GOOD
✅ 248 KB   - GOOD
❌ 350 KB   - TOO LARGE
❌ 1.2 MB   - WAY TOO LARGE
```

**Why?** Keeps repository size manageable and loads fast for users.

---

### 3. Supported Formats

**Supported:**
- ✅ **PNG** - Preferred for logos with transparency
- ✅ **SVG** - Preferred for scalable vector graphics

**Not Supported:**
- ❌ JPG/JPEG - Not recommended (no transparency)
- ❌ GIF - Not supported
- ❌ WebP - Not widely supported yet

**Best Practice:** Provide both PNG and SVG when possible
```json
"images": [
  {
    "png": "https://raw.githubusercontent.com/.../atom.png",
    "svg": "https://raw.githubusercontent.com/.../atom.svg"
  }
]
```

---

### 4. File Naming

**Convention:** Use lowercase, descriptive names

```bash
✅ atom.png          - Token symbol
✅ osmo.svg          - Token symbol
✅ cosmoshub.png     - Chain name
✅ usdc.png          - Common token name

❌ ATOM.PNG          - Don't use uppercase
❌ token-1.png       - Not descriptive
❌ image123.png      - Not meaningful
❌ my logo final.png - Spaces not recommended
```

---

### 5. Directory Structure

**Where images go:**

```
chain-registry/
├── cosmoshub/
│   └── images/
│       ├── atom.png              ← Native token
│       ├── atom.svg
│       └── cosmoshub-chain.png   ← Chain logo
├── osmosis/
│   └── images/
│       ├── osmo.png
│       ├── osmo.svg
│       └── ion.png               ← Other tokens
├── terra/
│   └── images/
│       └── luna.png
└── testnets/
    └── cosmoshubtestnet/
        └── images/
            └── atom.png
```

---

## Before You Upload: Check for Duplicates

**IMPORTANT:** Always search for existing images before uploading!

### Step 1: Search for Existing Images

```bash
# Search by filename
find . -name "usdc.png"
find . -name "atom.svg"
find . -name "btc.*"

# Search in specific directories
find ./noble -name "*.png"
find ./_non-cosmos/ethereum -name "*.png"
```

### Step 2: Check Common Locations

**Popular tokens often exist in:**
- `_non-cosmos/ethereum/images/` - ETH, USDC, USDT, WBTC, DAI
- `noble/images/` - USDC
- `bitcoin/images/` - BTC
- `cosmoshub/images/` - ATOM
- `osmosis/images/` - OSMO

### Step 3: If Image Exists → Use `image_sync`

**DO NOT upload a duplicate!** See [When to Use image_sync](#when-to-use-image_sync)

---

## When to Upload New Images

### ✅ Upload New Images When:

1. **Adding a brand new chain**
   - Chain logo
   - Native token logo

2. **Adding a new native token**
   - Token didn't exist before
   - First appearance in the registry

3. **Adding chain-specific branding**
   - Custom chain variations
   - Special commemorative logos

### Example: New Chain

```bash
# Directory structure for new chain
mychain/
├── chain.json
├── assetlist.json
└── images/
    ├── mychain.png        ← Upload new chain logo
    ├── mychain.svg
    ├── mytoken.png        ← Upload new token logo
    └── mytoken.svg
```

```json
// mychain/assetlist.json
{
  "assets": [
    {
      "base": "umytoken",
      "name": "My Token",
      "symbol": "MYTOKEN",
      "logo_URIs": {
        "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.png",
        "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.svg"
      },
      "images": [
        {
          "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.png",
          "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.svg"
        }
      ]
    }
  ]
}
```

---

## When to Use image_sync

### ❌ DO NOT Upload Duplicates When:

1. **Adding IBC tokens** (ATOM on Osmosis, OSMO on Cosmos Hub)
2. **Adding testnet versions of mainnet tokens**
3. **Adding wrapped/bridged tokens** (wBTC, wETH, axlUSDC)
4. **Adding CW20 versions of existing tokens**
5. **Token originated from another chain**

### ✅ Use image_sync Instead

**What is `image_sync`?**
- Automatically references images from the origin chain
- Avoids duplicate image files
- Keeps images in sync with the source
- Reduces repository size

---

### How to Use image_sync

#### Step 1: Find the Origin Chain

**Question:** Where did this token originally come from?

Examples:
- USDC → `noble` (for mainnet) or `nobletestnet` (for testnet)
- ATOM → `cosmoshub`
- OSMO → `osmosis`
- BTC → `bitcoin` or `_non-cosmos/bitcoin`
- ETH → `_non-cosmos/ethereum`

#### Step 2: Find the Base Denom on Origin Chain

```bash
# Look at the origin chain's assetlist
cat noble/assetlist.json | jq '.assets[] | select(.symbol=="USDC") | .base'
# Output: "uusdc"
```

#### Step 3: Add traces Field

**Required!** `image_sync` needs `traces` to work.

```json
{
  "traces": [
    {
      "type": "ibc",
      "counterparty": {
        "chain_name": "noble",
        "base_denom": "uusdc",
        "channel_id": "channel-0"
      },
      "chain": {
        "channel_id": "channel-123",
        "path": "transfer/channel-123/uusdc"
      }
    }
  ]
}
```

#### Step 4: Add image_sync to images

```json
{
  "images": [
    {
      "image_sync": {
        "chain_name": "noble",
        "base_denom": "uusdc"
      },
      "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.png",
      "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.svg"
    }
  ]
}
```

---

### Complete image_sync Example

**Scenario:** Adding testnet USDC to Terra Classic testnet

```json
{
  "description": "USD Coin (USDC) — testnet CW20 token on Terra Classic",
  "type_asset": "cw20",
  "address": "terra154hadnff5fu6lj2nlf0zpqlfmaquy0qelhvd8qrpgtlsyvvtywkqsd05p3",
  "denom_units": [
    {
      "denom": "cw20:terra154hadnff5fu6lj2nlf0zpqlfmaquy0qelhvd8qrpgtlsyvvtywkqsd05p3",
      "exponent": 0
    },
    {
      "denom": "usdc",
      "exponent": 6
    }
  ],
  "base": "cw20:terra154hadnff5fu6lj2nlf0zpqlfmaquy0qelhvd8qrpgtlsyvvtywkqsd05p3",
  "name": "USD Coin (Testnet)",
  "display": "usdc",
  "symbol": "USDC",
  "traces": [
    {
      "type": "ibc",
      "counterparty": {
        "chain_name": "nobletestnet",
        "base_denom": "uusdc",
        "channel_id": "channel-123"
      },
      "chain": {
        "channel_id": "channel-456",
        "path": "transfer/channel-456/uusdc"
      }
    }
  ],
  "images": [
    {
      "image_sync": {
        "chain_name": "nobletestnet",
        "base_denom": "uusdc"
      },
      "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.png",
      "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.svg"
    }
  ],
  "logo_URIs": {
    "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.png",
    "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.svg"
  }
}
```

**Notice:**
- ✅ `traces` field present (shows origin)
- ✅ `image_sync` references noble + uusdc
- ✅ Image URLs point to noble/images/usdc.png (NOT terra/images/usdc.png)
- ❌ NO duplicate usdc.png uploaded to terra/images/

---

## Image Optimization Guide

### Check Current Image Specs

```bash
# Get dimensions and format
file image.png

# Get file size
ls -lh image.png
```

**Recommended Sizes:**
- **Small logos:** 256x256 or 512x512
- **Standard logos:** 1024x1024
- **Maximum size:** 2000x2000 (only if < 250 KB)

**Note:** Images must be square (width = height) and < 250 KB. If your images don't meet these requirements, optimize them before uploading.

---

## Testing Images

### Test Locally Before Committing

```bash
# 1. Check dimensions (must be square)
identify mychain/images/token.png
# Expected: PNG image data, 1024 x 1024

# 2. Check file size (must be < 250 KB)
ls -lh mychain/images/token.png
# Expected: 48K, 120K, 200K, etc. (not 300K+)

# 3. Verify image loads
open mychain/images/token.png  # macOS
xdg-open mychain/images/token.png  # Linux
```

### Run Validation Script

```bash
# Install validator
npm install -g @chain-registry/cli@1.47.0

# Run validation
chain-registry validate --registryDir . --logLevel error

# Check for image-specific errors:
# ❌ "Chain PNG ... isn't square!"
# ❌ "Asset PNG ... isn't square!"
# ❌ "Image file size exceeds 250KB"
```

### Test in PR

Once you create a PR:
1. CI will automatically check image dimensions
2. CI will check file sizes
3. Fix any errors before maintainer review

---

## Common Image Mistakes

### Mistake 1: Non-Square Images ❌

**Error:**
```
Asset PNG at terratestnet, usdc isn't square! Width: 1920, Height: 1080
```

**Problem:** Image dimensions are 1920x1080 (not square)

**Solution:** Make your image square (width = height) before uploading.

---

### Mistake 2: Oversized Images ❌

**Error:**
```
Image file size exceeds 250KB limit: 512KB
```

**Problem:** Image is 512 KB (too large)

**Solution:** Optimize or reduce the image file size to under 250 KB before uploading.

---

### Mistake 3: Uploading Duplicates ❌

**Problem:** Uploading `btc.png` to `mychain/images/` when it already exists in `bitcoin/images/`

**Solution:**
```bash
# 1. Delete the duplicate file
rm mychain/images/btc.png

# 2. Use image_sync instead
# Add to assetlist.json:
{
  "traces": [ /* ... */ ],
  "images": [
    {
      "image_sync": {
        "chain_name": "bitcoin",
        "base_denom": "sat"
      }
    }
  ]
}
```

---

### Mistake 4: Missing Images in assetlist.json ❌

**Problem:** Uploaded `token.png` but forgot to reference it in `assetlist.json`

**Solution:** Add image references to every asset

```json
{
  "assets": [
    {
      "base": "utoken",
      "name": "My Token",
      "symbol": "TOKEN",
      "logo_URIs": {
        "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/token.png"
      },
      "images": [
        {
          "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/token.png"
        }
      ]
    }
  ]
}
```

---

### Mistake 5: Wrong Image URLs ❌

**Problem:** Hardcoded GitHub username in URL

```json
❌ "png": "https://raw.githubusercontent.com/myusername/chain-registry/master/..."
✅ "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/..."
```

**Solution:** Always use `cosmos/chain-registry` in image URLs (not your fork)

---

### Mistake 6: Using image_sync Without traces ❌

**Problem:**
```json
{
  "images": [
    {
      "image_sync": {
        "chain_name": "noble",
        "base_denom": "uusdc"
      }
    }
  ]
  // ❌ Missing traces field!
}
```

**Solution:** Always add `traces` when using `image_sync`

```json
{
  "traces": [
    {
      "type": "ibc",
      "counterparty": {
        "chain_name": "noble",
        "base_denom": "uusdc"
      }
    }
  ],
  "images": [
    {
      "image_sync": {
        "chain_name": "noble",
        "base_denom": "uusdc"
      }
    }
  ]
}
```

---

## Examples

### Example 1: Native Token (New Upload)

**Scenario:** Adding a brand new chain with native token

**Files:**
```
mychain/
├── chain.json
├── assetlist.json
└── images/
    ├── mytoken.png  ← 512x512, 45 KB
    └── mytoken.svg  ← 12 KB
```

**assetlist.json:**
```json
{
  "$schema": "../assetlist.schema.json",
  "chain_name": "mychain",
  "assets": [
    {
      "description": "The native token of My Chain",
      "denom_units": [
        {
          "denom": "umytoken",
          "exponent": 0
        },
        {
          "denom": "mytoken",
          "exponent": 6
        }
      ],
      "base": "umytoken",
      "name": "My Token",
      "display": "mytoken",
      "symbol": "MYTOKEN",
      "logo_URIs": {
        "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.png",
        "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.svg"
      },
      "images": [
        {
          "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.png",
          "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/mychain/images/mytoken.svg"
        }
      ]
    }
  ]
}
```

---

### Example 2: IBC Token (image_sync)

**Scenario:** Adding ATOM to Osmosis (ATOM originated from Cosmos Hub)

**Files:**
```
osmosis/
├── assetlist.json  ← Add ATOM here
└── images/
    └── (NO atom.png needed - using image_sync)
```

**osmosis/assetlist.json:**
```json
{
  "assets": [
    {
      "description": "The native staking token of the Cosmos Hub",
      "denom_units": [
        {
          "denom": "ibc/27394FB092D2ECCD56123C74F36E4C1F926001CEADA9CA97EA622B25F41E5EB2",
          "exponent": 0
        },
        {
          "denom": "atom",
          "exponent": 6
        }
      ],
      "base": "ibc/27394FB092D2ECCD56123C74F36E4C1F926001CEADA9CA97EA622B25F41E5EB2",
      "name": "Cosmos Hub Atom",
      "display": "atom",
      "symbol": "ATOM",
      "traces": [
        {
          "type": "ibc",
          "counterparty": {
            "chain_name": "cosmoshub",
            "base_denom": "uatom",
            "channel_id": "channel-0"
          },
          "chain": {
            "channel_id": "channel-0",
            "path": "transfer/channel-0/uatom"
          }
        }
      ],
      "images": [
        {
          "image_sync": {
            "chain_name": "cosmoshub",
            "base_denom": "uatom"
          },
          "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/cosmoshub/images/atom.png",
          "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/cosmoshub/images/atom.svg"
        }
      ],
      "logo_URIs": {
        "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/cosmoshub/images/atom.png",
        "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/cosmoshub/images/atom.svg"
      }
    }
  ]
}
```

**Notice:** No `osmosis/images/atom.png` uploaded - using image_sync to reference `cosmoshub/images/atom.png`

---

### Example 3: Testnet Token (image_sync)

**Scenario:** Adding testnet USDC (references mainnet Noble USDC)

**Files:**
```
testnets/nobletestnet/
├── assetlist.json  ← Add testnet USDC
└── images/
    └── (NO usdc.png needed - using image_sync)
```

**testnets/nobletestnet/assetlist.json:**
```json
{
  "assets": [
    {
      "description": "USD Coin (testnet)",
      "denom_units": [
        {
          "denom": "uusdc",
          "exponent": 0
        },
        {
          "denom": "usdc",
          "exponent": 6
        }
      ],
      "base": "uusdc",
      "name": "USD Coin",
      "display": "usdc",
      "symbol": "USDC",
      "traces": [
        {
          "type": "synthetic",
          "counterparty": {
            "chain_name": "forex",
            "base_denom": "USD"
          },
          "provider": "Circle"
        }
      ],
      "images": [
        {
          "image_sync": {
            "chain_name": "noble",
            "base_denom": "uusdc"
          },
          "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.png",
          "svg": "https://raw.githubusercontent.com/cosmos/chain-registry/master/noble/images/usdc.svg"
        }
      ]
    }
  ]
}
```

---

## Quick Reference

### Image Requirements Checklist

- [ ] Square dimensions (width = height)
- [ ] File size < 250 KB
- [ ] PNG or SVG format
- [ ] Descriptive filename (atom.png, osmo.svg)
- [ ] Checked for duplicates (`find . -name "token.png"`)
- [ ] Added to assetlist.json
- [ ] Used `image_sync` if cross-chain token
- [ ] Added `traces` if using image_sync
- [ ] Correct GitHub URL (cosmos/chain-registry)
- [ ] Ran local validation

---

## Tools & Resources

### Validation
```bash
# Install validator
npm install -g @chain-registry/cli@1.47.0

# Run validation
chain-registry validate --registryDir . --logLevel error

# Check dimensions and size
file image.png
ls -lh image.png
```

---

## Need Help?

**Common Questions:**

**Q: My image is 1920x1080. How do I make it square?**
A: You need to edit your image to make it square (width = height) before uploading.

**Q: Can I use JPG instead of PNG?**
A: Not recommended. PNG supports transparency and is preferred.

**Q: How do I know if I should use image_sync?**
A: If the token originated from another chain, use image_sync. See [When to Use image_sync](#when-to-use-image_sync)

**Q: Do I need both PNG and SVG?**
A: SVG is preferred but not required. Provide both when possible.

**Q: My PR failed CI with "Image isn't square". What do I do?**
A: Fix the image dimensions to be square, then push the changes.

---

**Questions?** Join the [Cosmos Chain Registry Working Group](https://t.me/+OkM0SDZ-M0liNDdh) on Telegram.

---

**Last Updated:** October 2025
