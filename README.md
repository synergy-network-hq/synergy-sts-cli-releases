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
  | bash -s -- --version synergy-sts-v15.0.6-alpha.1
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

## Token Address Rule

Native `SNRG` does not have a token address. Compatibility surfaces that require a string may use:

```text
00000000000000000000000000000000000000000
```

All non-native STS tokens have deterministic Synergy token addresses, such as `synb...` for fungible tokens.

## Source

The CLI source lives in the Synergy testnet repository:

https://github.com/synergy-network-hq/testnet

