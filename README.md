# Verify Shai-Hulud

A shell script to detect compromised NPM packages from the Shai-Hulud supply chain attack on macOS systems.

## Overview

This script scans your system for malicious NPM packages that were compromised during the Shai-Hulud supply chain attack. It searches for known compromised package versions and reports any findings.

## Usage

Run the script:

```bash
./verify-shai-hulud.sh
```

## Output

- **CLEAN**: No compromised packages found
- **JSON Report**: If compromised packages are detected, outputs detailed information including:
  - Hostname and serial number
  - Scan timestamp
  - Count of compromised packages
  - List of packages with names, versions, and file paths

## Configuration

Edit the script to customize:

- `SCAN_DIRS`: Directories to scan (default: `/Users /Applications /opt /var`)
- `MAX_DEPTH`: Maximum directory depth (default: 10)

## Requirements

- macOS
- Standard shell utilities (find, grep, awk, sed)
