# Synergy STS CLI Releases

Public release repository for the `synergy-sts` command-line tool.

`synergy-sts` builds Synergy Token System payloads for Synergy Testnet. The current testnet chain ID is `1264`.

## Install

macOS and Linux:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh | bash
```

The installer places the binary at:

```text
$HOME/.local/bin/synergy-sts
```

If that directory is not already on your `PATH`, run:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh \
  | bash -s -- --add-to-path
```

Install a specific release:

```bash
curl -fsSL https://github.com/synergy-network-hq/synergy-sts-cli-releases/releases/latest/download/install-synergy-sts.sh \
  | bash -s -- --version synergy-sts-v15.0.13
```

Verify the install:

```bash
synergy-sts version
synergy-sts native-info
```

## Release Assets

Each release publishes:

- `install-synergy-sts.sh`
- `synergy-sts-linux-amd64`
- `synergy-sts-linux-arm64`
- `synergy-sts-macos-amd64`
- `synergy-sts-macos-arm64`
- Matching `.sha256` files for each binary
- `latest.json` release metadata

The installer verifies release checksums by default.

Supported installer platforms are macOS and Linux on `amd64` and `arm64`.
Windows is not published from this release repository yet.

`synergy-sts-v15.0.13` is the first expanded STS CLI release with native fungible, NFT, multi-asset, credential, and chain-1264 metadata/image payload support.

## Token Address Rule

Native `SNRG` does not have a token address. Compatibility surfaces that require a string may use:

```text
00000000000000000000000000000000000000000
```

All non-native STS tokens have deterministic Synergy token addresses, such as `synb...` for fungible tokens.
