# Homebrew Packaging

Use this Homebrew tap to install Relay from its tagged source releases.

## Recommended Tap Layout

Create a separate tap repository, for example:

```text
homebrew-relay/
  Formula/
    relay.rb
```

Users install with:

```bash
brew tap <owner>/relay
brew install relay
relay doctor
relay install
```

## Release Flow

1. Tag and push a Relay release:

   ```bash
   git tag v0.1.0
   git push origin v0.1.0
   ```

2. Create a source archive URL, usually from GitHub:

   ```text
   https://github.com/<owner>/relay/archive/refs/tags/v0.1.0.tar.gz
   ```

3. Calculate the archive hash:

   ```bash
   curl -L -o relay-v0.1.0.tar.gz \
     https://github.com/<owner>/relay/archive/refs/tags/v0.1.0.tar.gz
   shasum -a 256 relay-v0.1.0.tar.gz
   ```

4. Copy the checked-in formula into the tap repository:

   ```bash
   mkdir -p ../homebrew-relay/Formula
   cp packaging/homebrew/Formula/relay.rb ../homebrew-relay/Formula/relay.rb
   ```

5. Commit and push the tap repository.

6. Test locally:

   ```bash
   brew install --build-from-source ../homebrew-relay/Formula/relay.rb
   relay doctor
   ```

## Python Dependencies

Relay depends on the PyPI package `mcp`. Homebrew formulas should not download dependencies dynamically during install, so the final formula needs `resource` blocks for `mcp` and its transitive dependencies.

`Formula/relay.rb` already includes the resource blocks generated for the current release. Regenerate them whenever Relay's Python dependencies change.

Homebrew can also print resource blocks:

```bash
brew update-python-resources --print-only --ignore-non-pypi-packages \
  --package-name relay-mcp --version 0.1.0 \
  --extra-packages mcp Formula/relay.rb
```

## Optional acpx Dependency

Relay can run without `acpx` for event logging, search, wiki, and local reports. `acpx` is only needed for:

- `discuss`
- `generate_report(synthesis="auto")` LLM synthesis
- `generate_report(synthesis="llm")`

Install `acpx` separately if you want LLM-backed discussion and report synthesis:

```bash
npm install -g acpx
```

Do not make `acpx` a hard Homebrew dependency unless Relay should require LLM-backed features by default.
