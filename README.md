# Shared GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows that can be shared across multiple repositories. These workflows provide standardized release management, tagging, and Python package building capabilities.

## Available Workflows

### 1. Release Workflow (`release.yml`)

**Purpose**: Complete release automation with semantic versioning, Python package building, and GitHub release creation.

**Usage in your repository**:
```yaml
name: Release and Tag

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Release version (e.g., 1.0.0 - without v prefix)'
        required: true
        type: string
      release_type:
        description: 'Type of release'
        required: false
        default: 'release'
        type: choice
        options:
          - release
          - prerelease
          - draft
      release_notes:
        description: 'Custom release notes (optional - will auto-generate if empty)'
        required: false
        type: string

jobs:
  release:
    uses: dahuuhad/github-shared-workflows/.github/workflows/release.yml@main
    with:
      version: ${{ inputs.version }}
      release_type: ${{ inputs.release_type }}
      release_notes: ${{ inputs.release_notes }}
      python_version: '3.11'  # Customize as needed
      package_name: 'your-package-name'
      run_tests: true
      test_command: 'python -c "import your_package; print(\"✓ Package imports successfully\")"'
```

**Features**:
- ✅ Semantic version validation
- ✅ Python package building (wheel + source distribution)
- ✅ Automatic version updates in `pyproject.toml`
- ✅ Git tag creation and pushing
- ✅ GitHub release with auto-generated changelog
- ✅ Customizable test execution
- ✅ Release asset attachment

### 2. Tag Workflow (`tag.yml`)

**Purpose**: Simple git tag creation and management.

**Usage in your repository**:
```yaml
name: Create Tag

on:
  workflow_dispatch:
    inputs:
      tag_name:
        description: 'Tag name (e.g., v1.0.0)'
        required: true
        type: string
      message:
        description: 'Tag message'
        required: false
        default: 'Tag created via GitHub Actions'
        type: string

jobs:
  create-tag:
    uses: dahuuhad/github-shared-workflows/.github/workflows/tag.yml@main
    with:
      tag_name: ${{ inputs.tag_name }}
      message: ${{ inputs.message }}
      push_to_remote: true
```

**Features**:
- ✅ Tag name validation
- ✅ Duplicate tag prevention
- ✅ Optional push to remote
- ✅ Custom tag messages

### 3. Composite Action (`composite-action.yml`)

**Purpose**: Reusable action for Python setup and package building.

**Usage in your workflow**:
```yaml
steps:
  - uses: actions/checkout@v4
  - uses: dahuuhad/github-shared-workflows/.github/workflows/composite-action.yml@main
    with:
      version: '1.0.0'
      python-version: '3.11'
      package-name: 'your-package'
```

## Getting Started

### For Repository Owners

1. **Create a caller workflow** in your repository at `.github/workflows/release.yml`
2. **Customize the inputs** to match your project needs
3. **Test the workflow** by running it manually from GitHub Actions tab

### Example Repository Setup

Create `.github/workflows/shared-release.yml` in your project:

```yaml
name: Release using Shared Workflow

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Release version (e.g., 1.0.0)'
        required: true
        type: string

jobs:
  release:
    uses: dahuuhad/github-shared-workflows/.github/workflows/release.yml@main
    with:
      version: ${{ inputs.version }}
      python_version: '3.11'
      package_name: ${{ github.event.repository.name }}
      test_command: 'python -c "print(\"Tests passed\")"'
```

## Workflow Inputs Reference

### Release Workflow Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `version` | string | ✅ | - | Release version (semantic versioning) |
| `release_type` | string | ❌ | `release` | Type: release, prerelease, draft |
| `release_notes` | string | ❌ | auto-generated | Custom release notes |
| `python_version` | string | ❌ | `3.14` | Python version for building |
| `package_name` | string | ❌ | repo name | Name of the Python package |
| `run_tests` | boolean | ❌ | `true` | Whether to run tests |
| `test_command` | string | ❌ | basic import test | Command to run tests |

### Tag Workflow Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `tag_name` | string | ✅ | - | Tag name (e.g., v1.0.0) |
| `message` | string | ❌ | default message | Tag annotation message |
| `push_to_remote` | boolean | ❌ | `true` | Push tag to remote repository |

## Versioning

### Using Specific Versions

Pin to a specific version for stability:
```yaml
uses: dahuuhad/github-shared-workflows/.github/workflows/release.yml@v1.0.0
```

### Using Latest

Use `@main` for latest features (less stable):
```yaml
uses: dahuuhad/github-shared-workflows/.github/workflows/release.yml@main
```

## Contributing

1. **Make changes** in feature branches
2. **Test workflows** in a test repository before merging
3. **Create releases** when ready to publish new versions
4. **Update documentation** for any input changes

## License

This project is open source and available under the same license as the projects that use it.