# setup-arctl

GitHub Action to install and verify the [`arctl`](https://github.com/agentregistry-dev/agentregistry) CLI.

Downloads the binary from the official GitHub release, verifies the SHA256 checksum from the release's `checksums.txt`, and installs it to PATH.

## Usage

```yaml
- uses: aairey/setup-arctl@v1
```

### With a specific version

```yaml
- uses: aairey/setup-arctl@v1
  with:
    version: '0.3.3'
```

### Full example

```yaml
jobs:
  publish:
    runs-on: self-hosted
    container:
      image: public.ecr.aws/docker/library/debian:bookworm-slim
    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: apt-get update && apt-get install -y --no-install-recommends curl ca-certificates

      - uses: aairey/setup-arctl@v1
        with:
          version: '0.3.3'

      - name: Publish prompts
        env:
          AGENTREGISTRY_URL: ${{ secrets.AGENTREGISTRY_URL }}
          AREGISTRY_TOKEN: ${{ secrets.AREGISTRY_TOKEN }}
        run: |
          arctl auth login --token "$AREGISTRY_TOKEN" --url "$AGENTREGISTRY_URL"
          arctl prompt publish prompts/my-prompt.yaml --registry-url "$AGENTREGISTRY_URL"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | arctl version to install (without leading `v`) | No | `0.3.3` |
| `install-dir` | Directory to install arctl into | No | `/usr/local/bin` |

## How it works

1. Downloads `checksums.txt` from the GitHub release
2. Downloads the `arctl` binary for the current OS/architecture
3. Verifies the SHA256 checksum against `checksums.txt`
4. Installs the verified binary to `install-dir`

## Supported platforms

| OS | Architecture |
|----|-------------|
| Linux | amd64, arm64 |
| macOS | amd64, arm64 |
| Windows | amd64 |
