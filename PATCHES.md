# selfupdate & patch-manager

A comprehensive guide to the XVE WSL distro's built-in update and patch management tools for developers.

## Overview

The XVE WSL distro includes two powerful tools for managing system updates and patches:

- **`selfupdate`**: Downloads and applies patches from external GitHub repositories
- **`patch-manager`**: Core patch management system with backup/rollback capabilities

These tools work together to provide a Laravel-style migration system for safely applying system updates with automatic backup and rollback functionality.

## Quick Start

### Basic Usage

```bash
# Apply all available patches from the default repository
selfupdate

# Check what patches are available and their status
patch-manager list

# View current patch system status
patch-manager status
```

### Common Workflows

```bash
# Update your system with latest patches
selfupdate

# Check if updates were successful
patch-manager status

# List all available patches with their status
patch-manager list
```

## selfupdate Command

The `selfupdate` command is your primary tool for keeping the WSL distro up-to-date. It downloads patches from external GitHub repositories and applies them automatically.

### How it Works

1. **Downloads patches** from a configured GitHub repository
2. **Extracts patches** to the local patches directory
3. **Delegates to patch-manager** to apply the patches
4. **Provides feedback** on the update process

### Basic Usage

```bash
# Update from the default repository (latest release)
selfupdate

# Show help and configuration options
selfupdate --help
```

### Configuration

You can customize the patch source using environment variables:

```bash
# Use a different repository
XVE_PATCH_REPO_OWNER="your-org" XVE_PATCH_REPO_NAME="your-patches" selfupdate

# Use a specific release version
XVE_PATCH_RELEASE_TAG="v1.2.0" selfupdate

# Combine multiple options
XVE_PATCH_REPO_OWNER="mycompany" XVE_PATCH_RELEASE_TAG="v2.0.0" selfupdate
```

### Default Configuration

- **Repository**: `jonasvanderhaegen-xve/windows-powershell-wsl-distro-patches`
- **Release**: `latest`
- **Patches Directory**: `/opt/xve-patches/available`

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `XVE_PATCH_REPO_OWNER` | GitHub repository owner | `jonasvanderhaegen-xve` |
| `XVE_PATCH_REPO_NAME` | GitHub repository name | `windows-powershell-wsl-distro-patches` |
| `XVE_PATCH_RELEASE_TAG` | Release tag to download | `latest` |

### Dependencies

The `selfupdate` command requires these tools to be installed:
- `curl` - for downloading from GitHub
- `jq` - for parsing JSON responses
- `tar` - for extracting patch archives

## patch-manager Command

The `patch-manager` is the core patch management system that handles patch application, state tracking, and backup/rollback operations.

### Available Commands

```bash
# Initialize the patch management system
patch-manager init

# Apply all pending patches
patch-manager apply

# Apply a specific patch
patch-manager apply 001_example_patch

# List all available patches with status
patch-manager list

# Show current patch system status
patch-manager status

# Apply all pending patches (alias for apply)
patch-manager selfupdate
```

### Command Details

#### `patch-manager init`
Initializes the patch management system by creating necessary directories and state files.

#### `patch-manager apply [patch_name]`
- Without arguments: applies all pending patches
- With patch name: applies only the specified patch
- Skips patches that are already applied
- Creates backups before applying patches
- Attempts rollback on failure

#### `patch-manager list`
Shows all available patches with their current status:
- ✓ Green checkmark: patch is applied
- ○ Yellow circle: patch is pending

#### `patch-manager status`
Displays comprehensive status information:
- Last update timestamp
- Number of applied patches
- List of all applied patches

### State Management

The patch system tracks state in `/opt/xve-patches/applied_patches.json`:

```json
{
  "applied_patches": ["001_example_patch", "002_another_patch"],
  "last_update": "2024-01-15T10:30:00Z",
  "version": "1.0"
}
```

### Backup System

- **Automatic backups**: Created before applying each patch
- **Backup location**: `/opt/xve-patches/backups/`
- **Naming convention**: `{patch_name}_{filename}.backup`
- **Automatic cleanup**: Successful patches clean up their backups
- **Rollback support**: Failed patches attempt automatic rollback

## Directory Structure

```
/opt/xve-patches/
├── applied_patches.json    # State tracking file
├── available/              # Downloaded patches
│   ├── 001_example.sh
│   └── 002_another.sh
└── backups/               # Backup files
    ├── 001_example_file.backup
    └── 002_another_config.backup
```

## Patch Development

### Patch Structure

Patches are bash scripts with specific functions:

```bash
#!/bin/bash

PATCH_NAME="001_example_patch"
PATCH_DESCRIPTION="Description of what this patch does"

# Required: Apply the patch
patch_up() {
    echo "Applying patch: $PATCH_DESCRIPTION"
    
    # Create backup if modifying files
    local backup_path
    backup_path=$(create_backup "/path/to/file" "$PATCH_NAME")
    
    # Apply your changes here
    # ... patch logic ...
    
    return 0
}

# Optional: Rollback the patch
patch_down() {
    echo "Rolling back patch: $PATCH_DESCRIPTION"
    # ... rollback logic ...
    return 0
}

# Optional: Validate before applying
patch_validate() {
    # Pre-flight checks
    return 0
}
```

### Naming Convention

- Use 3-digit numbers: `001_`, `002_`, `003_`
- Descriptive names: `001_update_zsh_config.sh`
- Patches are applied in alphabetical order

### Best Practices

1. **Always create backups** for files you're modifying
2. **Implement rollback logic** in `patch_down()` function
3. **Make patches idempotent** - safe to run multiple times
4. **Use validation** to check prerequisites
5. **Test thoroughly** before deployment

## Troubleshooting

### Common Issues

#### Permission Denied
```bash
# Ensure proper permissions
sudo chown -R xve:xve /opt/xve-patches
```

#### Missing Dependencies
```bash
# Install required tools
sudo apk add curl jq tar
```

#### Failed Patch Application
```bash
# Check patch status
patch-manager status

# List patches to see which failed
patch-manager list

# Check system logs for errors
journalctl -u patch-manager
```

#### Network Issues
```bash
# Test GitHub connectivity
curl -s https://api.github.com/repos/jonasvanderhaegen-xve/windows-powershell-wsl-distro-patches/releases/latest

# Check DNS resolution
nslookup api.github.com
```

### Recovery

If the patch system becomes corrupted:

1. **Restore files manually** from `/opt/xve-patches/backups/`
2. **Reset state file**:
   ```bash
   echo '{"applied_patches": [], "last_update": null, "version": "1.0"}' | sudo tee /opt/xve-patches/applied_patches.json
   ```
3. **Reinitialize system**:
   ```bash
   patch-manager init
   ```

### Debug Mode

Enable verbose output in patches:

```bash
# Add to your patch functions
set -x  # Enable debug output
# ... your patch logic ...
set +x  # Disable debug output
```

## Advanced Usage

### Custom Patch Repositories

Set up your own patch repository:

1. **Create GitHub repository** with `patches/` directory
2. **Add patch files** following naming convention
3. **Create releases** with version tags
4. **Configure environment variables**:
   ```bash
   export XVE_PATCH_REPO_OWNER="your-org"
   export XVE_PATCH_REPO_NAME="your-patches"
   export XVE_PATCH_RELEASE_TAG="v1.0.0"
   ```

### Private Repositories

For private repositories, set up authentication:

```bash
# Using GitHub token
export GITHUB_TOKEN="your_github_token"

# Note: This requires manual modification of the selfupdate script
# to include authentication headers in curl requests
```

### Batch Operations

```bash
# Apply patches from specific release
XVE_PATCH_RELEASE_TAG="v1.5.0" selfupdate

# Apply patches from different repository
XVE_PATCH_REPO_OWNER="mycompany" XVE_PATCH_REPO_NAME="custom-patches" selfupdate

# Combine multiple environment variables
XVE_PATCH_REPO_OWNER="myorg" XVE_PATCH_RELEASE_TAG="v2.0.0" selfupdate
```

## Integration with Development Workflow

### CI/CD Integration

```bash
# In your deployment scripts
#!/bin/bash

# Update WSL distro as part of deployment
wsl -d xve-distro -u xve -- selfupdate

# Verify patches were applied successfully
wsl -d xve-distro -u xve -- patch-manager status
```

### Team Synchronization

```bash
# Ensure all team members are on the same patch level
XVE_PATCH_RELEASE_TAG="v1.2.0" selfupdate
```

### Development Environment Setup

```bash
# New team member onboarding
selfupdate  # Get latest patches
patch-manager status  # Verify system is up-to-date
```

## Security Considerations

1. **Verify patch sources** - Only use trusted repositories
2. **Review patches** before applying in production
3. **Test in development** environments first
4. **Backup critical data** before major updates
5. **Use specific release tags** for production deployments

## Performance Tips

1. **Use specific release tags** to avoid unnecessary downloads
2. **Clean up old backups** periodically
3. **Monitor disk space** in `/opt/xve-patches/`
4. **Batch updates** rather than frequent small updates

## Related Documentation

- [PATCH_SYSTEM.md](PATCH_SYSTEM.md) - Comprehensive patch system documentation
- [README.md](README.md) - Main project documentation
- [Example Patch Repository](https://github.com/jonasvanderhaegen-xve/windows-powershell-wsl-distro-patches) - Sample patches and repository structure