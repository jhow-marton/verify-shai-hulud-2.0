# Verify Shai-Hulud 2.0

A fast, optimized shell script to detect compromised NPM packages from the Shai-Hulud supply chain attack on macOS systems.

## Overview

This script scans your system for malicious NPM packages that were compromised during the Shai-Hulud supply chain attack. It uses an optimized strategy that checks `package-lock.json` and `package.json` files instead of scanning thousands of files in `node_modules`, making it significantly faster.

## Quick Start

Run the script (silent mode):

```bash
./verify-shai-hulud.sh
```

**Output:**
- `CLEAN` - No compromised packages found (exit code 0)
- JSON report with details if packages found (exit code 1)

## Usage Examples

### Silent Mode (Default - Recommended)
Just shows the result, no progress messages:

```bash
./verify-shai-hulud.sh
```

Example output if clean:
```
CLEAN
```

Example output if infected:
```json
{"status":"INFECTED","hostname":"Mac","serial":"VJYQ331PJH","scan_date":"2025-11-27T22:41:04Z","count":4,"packages":[{"package":"@mcp-use/mcp-use","version":"1.0.1","path":"/Users/user/project"},...]}
```

### Verbose Mode
Shows scan progress and statistics:

```bash
VERBOSE=true ./verify-shai-hulud.sh
```

### Different Search Modes

**Lockfile Mode (Default - Fast):**
Checks package-lock.json and package.json files:
```bash
./verify-shai-hulud.sh
# or explicitly
SEARCH_MODE=lockfile ./verify-shai-hulud.sh
```

**Installed Mode (Slow - Thorough):**
Scans actual node_modules directories:
```bash
SEARCH_MODE=installed ./verify-shai-hulud.sh
```

## Configuration

You can customize the scan using environment variables:

```bash
# Set search depth (default: 15)
MAX_DEPTH=10 ./verify-shai-hulud.sh

# Set directories to scan (default: /Users /Applications /opt /var)
SCAN_DIRS="/Users /Projects" ./verify-shai-hulud.sh

# Combine options
VERBOSE=true MAX_DEPTH=20 ./verify-shai-hulud.sh
```

Or edit the script directly:
- `SCAN_DIRS`: Directories to scan (default: `/Users /Applications /opt /var`)
- `MAX_DEPTH`: Maximum directory depth (default: `15`)
- `SEARCH_MODE`: Search strategy (default: `lockfile`)

## Performance

### Optimizations in v2.0
- **10-50x faster**: Parses lockfiles instead of scanning node_modules
- **Reduced I/O**: Reads only package manifests, not installed files
- **Smart filtering**: Skips .git, .cache, .npm, and other irrelevant directories
- **Efficient parsing**: Uses AWK for fast JSON extraction

### Benchmarks
On a typical developer machine:
- **Lockfile mode**: ~1-2 minutes
- **Installed mode**: 10+ minutes

## Output Format

### Clean System
```
CLEAN
```

### Infected System
```json
{
  "status": "INFECTED",
  "hostname": "Mac",
  "serial": "VJYQ331PJH",
  "scan_date": "2025-11-27T22:41:04Z",
  "count": 4,
  "packages": [
    {
      "package": "@mcp-use/mcp-use",
      "version": "1.0.1",
      "path": "/Users/user/project"
    }
  ]
}
```

## Requirements

- macOS or Linux
- Standard shell utilities (sh, find, grep, awk, sed)
- Linux only: `/sys/class/dmi/id/product_serial` or `dmidecode` (optional, for serial number detection)

## How It Works

1. **Lockfile Mode (Default)**:
   - Finds all `package-lock.json` files
   - Parses dependency trees using AWK
   - Checks `package.json` files without lockfiles
   - Matches against known malicious package:version pairs

2. **Installed Mode**:
   - Locates all `node_modules` directories
   - Reads `package.json` from each installed package
   - Verifies actual installed versions

## Exit Codes

- `0` - Clean (no malicious packages found)
- `1` - Infected (malicious packages detected)
