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

### Example with Deploy Key

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
          deploy-key: ${{ secrets.DEPLOY_KEY }}
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
          deploy-key: ${{ secrets.DEPLOY_KEY }}
          s2dm-path: s2dm
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
| `repository-path` | Path to the git repository root | Yes | - |
| `spec-path` | Path to the spec directory | No | `./spec` |
| `github-token` | GitHub token for creating releases and checking out repositories | Yes | - |
| `deploy-key` | SSH deploy key for checking out repositories (optional, takes precedence over github-token) | No | `''` |
| `s2dm-path` | Path where S2DM repository will be checked out | No | `s2dm` |
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

## Authentication

The action supports two authentication methods for checking out the S2DM repository:

### Using GitHub Token (Default)

By default, the action uses the `github-token` for both:
- Checking out the COVESA/s2dm repository
- Creating releases via GitHub API

```yaml
- name: Run S2DM migration
  uses: ahmed/s2dm-migration-action@main
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Using SSH Deploy Key (Optional)

If you provide a `deploy-key`, it will be used for checking out the S2DM repository instead of the token. The `github-token` is still required for creating releases.

```yaml
- name: Run S2DM migration
  uses: ahmed/s2dm-migration-action@main
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    deploy-key: ${{ secrets.DEPLOY_KEY }}
```

### Protected Branches

**Important:** If your branch is protected with push restrictions, you must provide either:
- A `github-token` with permissions to bypass branch protection, OR
- A `deploy-key` (SSH key) with permissions to bypass branch protection

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
