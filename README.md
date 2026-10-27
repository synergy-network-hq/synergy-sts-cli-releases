# Synergy STS CLI Releases

Public binary releases for `synergy-sts`, the command-line interface for the Synergy Token System (STS).

> **Current release:** [`synergy-sts-v20.0.0`](https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/tag/synergy-sts-v20.0.0)  
> **Network:** `synergy-testnet-v3`  
> **Chain ID:** `1266`

The CLI builds deterministic STS payloads for review, estimates their gas and fees, signs typed ML-DSA-87 Aegis transaction envelopes, submits them through the native Chain 1266 ingress, and verifies finalized receipts. Token-creation submissions also verify the expected deterministic token ID in finalized STS state before reporting success.

## Creator Guide

[Download the Synergy STS Testnet Creator Guide (PDF)](https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/download/synergy-sts-v20.0.0/Testnet_Tokens_Creator_Guide.pdf)

This 48-page guide covers Testnet-v3 setup, payload preview and fee estimation, token and NFT creation, signed submission, finalized-state verification, troubleshooting, and the complete v20 command and option reference.

## Install

### macOS and Linux

Install the latest release:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh | bash
```

The installer detects the operating system and CPU architecture and installs the binary to:

```text
$HOME/.local/bin/synergy-sts
```

Add the install directory to your shell profile when needed:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh \
  | bash -s -- --add-to-path
```

Install the current release explicitly:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh \
  | bash -s -- --version synergy-sts-v20.0.0
```

Install into another directory:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh \
  | bash -s -- --install-dir /usr/local/bin
```

Verify the installation:

```bash
synergy-sts version
synergy-sts native-info
```

Expected version:

```text
synergy-sts 20.0.0
```

## Published Release Assets

The `synergy-sts-v20.0.0` release contains:

- `install-synergy-sts.sh`
- `RELEASE_NOTES.md`
- `Testnet_Tokens_Creator_Guide.pdf`
- `synergy-sts-linux-amd64`
- `synergy-sts-linux-amd64.sha256`
- `synergy-sts-linux-arm64`
- `synergy-sts-linux-arm64.sha256`
- `synergy-sts-macos-amd64`
- `synergy-sts-macos-amd64.sha256`
- `synergy-sts-macos-arm64`
- `synergy-sts-macos-arm64.sha256`

The installer verifies the matching SHA-256 sidecar by default.

Supported published platforms are:

| Operating system | Architecture | Asset |
|---|---|---|
| Linux | AMD64 / x86_64 | `synergy-sts-linux-amd64` |
| Linux | ARM64 / AArch64 | `synergy-sts-linux-arm64` |
| macOS | Intel / AMD64 | `synergy-sts-macos-amd64` |
| macOS | Apple Silicon / ARM64 | `synergy-sts-macos-arm64` |

A native Windows executable is not included in the v20.0.0 release assets yet.

## What Changed In v20.0.0

v20 moves the CLI to the current Chain 1266 Testnet-v3 runtime and transaction path:

- Binds every STS payload to Chain ID `1266` and network `synergy-testnet-v3`.
- Uses `synergy_submitAegisTransaction` for typed ML-DSA-87 Aegis envelopes.
- Validates the RPC chain identity, incarnation, network ID, Genesis hash, Aegis capability, and native SNRG definition before submission.
- Reads the finalized head, account nonce, and native SNRG balance before signing.
- Fails closed when the wallet key does not derive the declared sender.
- Pays transaction fees in native SNRG.
- Waits for a successful finalized receipt by default.
- Verifies newly created token IDs in finalized STS state.
- Adds complete B1, B2, B3, NF1, NF2, multi-asset, and credential command coverage.
- Supports metadata and image file hashing with SHA3-256.
- Supports pretty JSON, compact JSON, payload-only JSON, and raw payload-hex output.
- Supports saved-artifact decode, fee estimation, review-first submission, and direct command submission.
- Includes `send` as an alias for `submit`, `multi-asset` as an alias for `ma`, and `credentials` as an alias for `credential`.

## Safe By Default

Payload-producing commands do **not** broadcast by default. They emit a deterministic review artifact containing the decoded payload, encoded payload hex, local gas estimate, estimated fee, and deterministic object ID where applicable.

Broadcast only when one of these is supplied:

```text
--submit
--send
```

Submission additionally requires a decrypted Synergy ML-DSA-87 wallet file through `--wallet`, `--wallet-file`, or `SYNERGY_WALLET_FILE`.

## Quick Start: Preview A B1 Token

This command creates a payload artifact only. It does not submit a transaction:

```bash
synergy-sts token create \
  --network synergy-testnet-v3 \
  --class b1 \
  --name "Example Token" \
  --symbol EXAMPLE \
  --decimals 9 \
  --initial-supply 1000000000 \
  --max-supply 1000000000 \
  --from synw1creator... \
  --creator-nonce 1 \
  --out ./example-token-create.json
```

Review the payload:

```bash
synergy-sts decode --file ./example-token-create.json
synergy-sts estimate --file ./example-token-create.json
```

## Submit A Reviewed Artifact

```bash
synergy-sts submit \
  --file ./example-token-create.json \
  --wallet ./wallet.dec.json \
  --out ./example-token-submit-result.json
```

The sender in the saved payload must match the submitting wallet.

## Create And Submit Directly

When a wallet is provided, the CLI can derive `--from` from it. Creation commands also default `--creator-nonce` to the creation timestamp when it is omitted.

```bash
synergy-sts token create \
  --network synergy-testnet-v3 \
  --class b1 \
  --name "Example Token" \
  --symbol EXAMPLE \
  --decimals 9 \
  --initial-supply 1000000000 \
  --max-supply 1000000000 \
  --submit \
  --wallet ./wallet.dec.json \
  --out ./example-token-submit-result.json
```

## Supported STS Object Types

| Type | Prefix | Creation path | Purpose |
|---|---|---|---|
| B1 fungible | `synb1` | `token create --class b1` | Standard fungible asset |
| B2 fungible | `synb2` | `token create --class b2` | Managed asset with declared freeze, pause, or clawback powers |
| B3 fungible | `synb3` | `token create --class b3` | Policy asset using approved native templates |
| NF1 NFT | `synn1` | `nft create-collection --class nf1`, then `nft mint` | Standard transferable NFT |
| NF2 NFT | `synn2` | `nft create-collection --class nf2`, then `nft mint` | Restricted, expiring, revocable, or single-use NFT |
| Multi-asset | `synj` | `ma create`, `ma create-item`, then `ma mint` | Collection containing fungible, non-fungible, or semi-fungible items |
| Credential | `synk` | `credential schema create`, then `credential issue` | Non-transferable identity or credential object |

## Command Families

### General

```text
synergy-sts help
synergy-sts version
synergy-sts native-info
synergy-sts decode
synergy-sts estimate
synergy-sts submit
synergy-sts send
```

### Fungible Tokens

```text
synergy-sts token create
synergy-sts token mint
synergy-sts token transfer
synergy-sts token burn
synergy-sts token set-image
synergy-sts token freeze
synergy-sts token thaw
synergy-sts token pause
synergy-sts token unpause
synergy-sts token clawback
synergy-sts token snapshot
```

B2-only managed operations are `freeze`, `thaw`, `pause`, `unpause`, and `clawback`. B3 `snapshot` requires the `snapshot_v1` policy.

### NFTs

```text
synergy-sts nft create-collection
synergy-sts nft mint
synergy-sts nft transfer
synergy-sts nft burn
synergy-sts nft freeze
synergy-sts nft thaw
synergy-sts nft revoke
synergy-sts nft use
synergy-sts nft update-metadata
synergy-sts nft verify-collection
```

### Multi-Asset

```text
synergy-sts ma create
synergy-sts ma create-item
synergy-sts ma mint
synergy-sts ma transfer
synergy-sts ma burn
synergy-sts ma batch-mint
synergy-sts ma batch-transfer
synergy-sts ma batch-burn
```

`multi-asset` is an alias for `ma`. Batch operations are atomic. Item types can be selected with `--type fungible|non_fungible|semi_fungible` or with `--fungible`, `--non-fungible`, and `--semi-fungible`.

### Credentials

```text
synergy-sts credential schema create
synergy-sts credential issue
synergy-sts credential revoke
synergy-sts credential suspend
synergy-sts credential restore
synergy-sts credential expire
synergy-sts credential verify-status
synergy-sts credential verify
```

`credentials` is an alias for `credential`, and `verify` is an alias for `verify-status`. These verification commands create an on-chain status-verification transaction; use the STS RPC methods for read-only checks.

## B3 Policy Templates

Repeat `--policy` to combine supported templates:

```text
--policy snapshot_v1
--policy transfer_fee_v1:fee_bps=<0-1000>,recipient=<synw1...>
--policy vesting_v1:start_at=<u64>,cliff_at=<u64>,end_at=<u64>
--policy max_wallet_v1:max_balance=<base-units>
```

Unsupported policy templates fail closed.

## Output Modes

All payload-producing commands support:

```text
--output json
--output compact-json
--output payload-hex
--output payload-json
--out <path>
```

If `--out` is supplied, the CLI writes the selected output to the file and also prints it to standard output.

## Metadata And Images

The CLI accepts metadata and image files and calculates their SHA3-256 hashes locally:

```text
--metadata-uri <ipfs://|ar://|https:// URI>
--metadata-hash <64-character lowercase SHA3-256 hex>
--metadata-file <path>
--image-uri <ipfs://|ar://|https:// URI>
--image-hash <64-character lowercase SHA3-256 hex>
--image-file <path>
```

When both a file and explicit hash are provided, they must match. A URI requires its corresponding hash. SVG image URIs are rejected for explorer safety.

Fungible metadata is immutable in the current protocol surface. The parser recognizes metadata-mutability, allowlist, denylist, and transfer-approval flags, but the runtime rejects those fungible creation requests until those controls are completely enforced.

## Wallet And RPC Configuration

A submitting wallet must be decrypted Synergy ML-DSA-87 wallet JSON containing its address and private key. Its public key may be:

- in the same wallet JSON;
- provided with `--wallet-public` or `--public-wallet`; or
- loaded from the matching sibling `.pub.json` file.

The CLI verifies that the public key derives the wallet address and refuses mismatched `--from` values.

Default RPC:

```text
https://testnet-rpc.synergy-network.io
```

Environment variables:

```bash
export SYNERGY_RPC_URL=https://testnet-rpc.synergy-network.io
export SYNERGY_WALLET_FILE=/path/to/wallet.dec.json
```

Common submission controls:

```text
--rpc-url <url>
--nonce <u64>
--gas-price-nwei <u64>
--max-fee-nwei <u128>
--gas-limit <u64>
--carrier-amount-nwei <u64>
--receiver <synw1...>
--epoch <u64>
--ttl-height <u64>
--timeout-seconds <u64>
--no-wait
```

Defaults:

- Gas price input: `40` nWei.
- Carrier amount: `1` nWei sent back to the sender unless `--receiver` is supplied.
- Aegis lifecycle epoch: `0`.
- TTL: current finalized height plus `120`.
- Receipt timeout: `30` seconds.
- Receipt polling: enabled unless `--no-wait` is supplied.

## Installer Options

Run the downloaded installer with `--help` for the full list. Supported options include:

```text
--from-source <dir>
--from-file <path>
--url <url>
--version <tag|latest>
--repo <owner/repo>
--install-dir <dir>
--target <platform>
--sha256 <hex>
--skip-sha256
--add-to-path
--no-path-check
--no-locked
--dry-run
```

For pinned `--url` or `--from-file` installs, provide `--sha256` whenever possible. Release downloads use their published checksum sidecar automatically.

## Native SNRG And Token Addresses

Native SNRG is the gas and fee asset. It is not an STS object and has no canonical token address.

Compatibility-only placeholder:

```text
00000000000000000000000000000000000000000
```

That value is not a token contract, STS object ID, or signable address.

Every non-native STS object has a deterministic Bech32m identifier using its object-type prefix, such as `synb1`, `synb2`, `synb3`, `synn1`, `synn2`, `synj`, or `synk`.

## Important Operational Notes

- Token amounts and supplies are integer base units. Floating-point token amounts are not accepted.
- Symbols are 2-12 uppercase ASCII letters or digits and must begin with a letter.
- A mint authority requires a bounded `--max-supply`.
- Use `--no-mint-authority` for a fixed-supply fungible token.
- B2 powers must be explicitly declared at creation.
- B3 transfer fees are capped at 1000 basis points.
- `--no-wait` reports ingress acceptance only; it does not prove finality.
- The CLI does not unlock encrypted wallets or prompt for a seed phrase.
- Hardware-wallet and interactive Synergy Wallet application signing are not part of v20.0.0.
- Never put private keys, seed phrases, or decrypted wallet contents into shell history, documentation, issue comments, or chat.

## Help

Use the built-in command reference:

```bash
synergy-sts --help
```

For release-specific details, download or open `RELEASE_NOTES.md` from the selected GitHub release.
