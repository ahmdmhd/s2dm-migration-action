# S2DM Migration Action

GitHub Action for automated migration workflow of S2DM specifications.

## Features

- Automatic version bump detection
- GraphQL schema composition
- JSON schema generation
- Registry management (init/update)
- SHACL generation
- SKOS RDF generation
- VSpec generation
- Automated release creation

## Usage

### Basic Example

```yaml
name: Migration
on:
  push:
    branches: [main]
    paths: ['spec/**']

jobs:
  migrate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Run S2DM migration
        uses: ahmed/s2dm-migration-action@main
        with:
          spec-path: ./spec
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Advanced Example with All Options

```yaml
name: Migration
on:
  push:
    branches: [main]
    paths: ['spec/**']

jobs:
  migrate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Run S2DM migration
        uses: ahmed/s2dm-migration-action@main
        with:
          spec-path: ./spec
          github-token: ${{ secrets.GITHUB_TOKEN }}
          s2dm-repository: COVESA/s2dm
          s2dm-version: main
          concept-namespace: 'https://example.com/concepts/'
          concept-prefix: 'ex'
          shacl-serialization-format: 'turtle'
          shacl-shapes-namespace: 'https://example.com/shapes/'
          shacl-shapes-prefix: 'sh'
          shacl-model-namespace: 'https://example.com/model/'
          shacl-model-prefix: 'model'
          skos-namespace: 'https://example.com/skos/'
          skos-prefix: 'skos'
          skos-language: 'en'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `spec-path` | Path to the spec directory | No | `./spec` |
| `github-token` | GitHub token for creating releases | Yes | - |
| `s2dm-repository` | S2DM repository to use | No | `COVESA/s2dm` |
| `s2dm-version` | S2DM version (branch, tag, or commit) | No | `main` |
| `concept-namespace` | Concept namespace for registry | No | `''` |
| `concept-prefix` | Concept prefix for registry | No | `''` |
| `shacl-serialization-format` | SHACL serialization format | No | `''` |
| `shacl-shapes-namespace` | SHACL shapes namespace | No | `''` |
| `shacl-shapes-prefix` | SHACL shapes prefix | No | `''` |
| `shacl-model-namespace` | SHACL model namespace | No | `''` |
| `shacl-model-prefix` | SHACL model prefix | No | `''` |
| `skos-namespace` | SKOS namespace | No | `''` |
| `skos-prefix` | SKOS prefix | No | `''` |
| `skos-language` | SKOS language | No | `''` |

## Outputs

| Output | Description |
|--------|-------------|
| `version-bump` | Type of version bump (major, minor, patch, none) |
| `latest-tag` | The latest git tag after release |
| `continue` | Whether migration should continue |

## Requirements

Your repository must have:

1. A `spec/` directory with S2DM specification files
2. A `.bumpversion.toml` configuration file for version management
3. Proper permissions: `contents: write` in the workflow

### Example `.bumpversion.toml`

```toml
[tool.bumpversion]
current_version = "0.1.0"
commit = true
tag = true

[[tool.bumpversion.files]]
filename = "VERSION"
```

## How It Works

1. **Version Check**: Compares current spec with previous release to determine version bump
2. **Artifact Generation**: Generates all required artifacts (GraphQL, JSON Schema, Registry, SHACL, SKOS, VSpec)
3. **Version Bump**: Updates version and creates git tag
4. **Release Creation**: Creates GitHub release with generated artifacts

## License

This action is provided as-is for use with S2DM projects.
