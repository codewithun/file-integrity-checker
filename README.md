# File Integrity Checker

A lightweight Bash utility to monitor file integrity using SHA-256 checksums.

This project is built for the [roadmap.sh File Integrity Checker challenge](https://roadmap.sh/projects/file-integrity-checker).

## Overview

File Integrity Checker detects changes to files by comparing their current SHA-256 checksums against a stored baseline.

It can monitor individual files or directories and report new, modified, or missing files.

## Features

- Generate a baseline of SHA-256 checksums.
- Check the integrity of individual files or directories.
- Detect modified files.
- Detect newly added files.
- Detect missing files.
- Update the baseline for an individual file.
- Support filenames containing spaces through tab-separated baseline entries.
- Restrict access to baseline files using Linux file permissions.
- Use temporary files when creating or updating the baseline.

## Requirements

- Linux
- Bash
- `sha256sum`
- Standard command-line utilities, including `find`, `cut`, and `mktemp`

## Project Structure

```text
file-integrity-checker/
├── integrity-check
├── baseline/
│   └── hashes.txt
├── logs/
│   ├── app.log
│   ├── auth.log
│   └── new.log
└── README.md
```

## Usage

Make the script executable:

```bash
chmod +x integrity-check
```

### 1. Initialize the baseline

Calculate and store the initial checksums for a file or directory:

```bash
./integrity-check init ./logs
```

The baseline is saved to `baseline/hashes.txt`.

The script does not overwrite an existing baseline automatically. To initialize a new baseline, first back up the existing baseline and deliberately remove it before running `init` again.

### 2. Check file integrity

Check all files in a directory:

```bash
./integrity-check check ./logs
```

Check a single file:

```bash
./integrity-check check ./logs/auth.log
```

Possible output statuses:

| Status | Meaning |
|---|---|
| `[OK]` | File checksum matches the baseline. |
| `[NEW]` | File is not recorded in the baseline. |
| `[MODIFIED]` | File checksum differs from the baseline. |
| `[MISSING]` | A previously recorded file no longer exists. |

The check command returns a nonzero exit status if it detects changes, new files, or missing files.

### 3. Update the baseline

After verifying that a change is authorized, update the baseline for an individual file:

```bash
./integrity-check update ./logs/auth.log
```

This replaces the stored checksum for that file while preserving other baseline entries.

Updating the baseline should be done only after reviewing the change.

## Example Output

```text
[OK]       ./logs/new.log
[OK]       ./logs/app.log
[OK]       ./logs/auth.log
```

This output indicates that the monitored files match their recorded checksums.

## How It Works

1. Calculate SHA-256 checksums for the target files.
2. Store each checksum and its file path in the baseline.
3. Recalculate checksums when integrity checks are performed.
4. Compare current checksums with the recorded values.
5. Report new, modified, and missing files.
6. Update an individual file's baseline entry when requested.

## Security Considerations

- Protect the baseline against unauthorized changes.
- Review unexpected modifications before updating checksums.
- The baseline directory is restricted to its owner, and the baseline file is created with restrictive permissions.
- Store the baseline on a separate, protected location for stronger protection against attackers who can modify both the monitored files and their baseline.

## Limitations

- The tool detects checksum differences but does not prevent file modifications.
- The integrity of the comparison depends on the security of the stored baseline.
- The `update` command updates one existing file at a time; it does not initialize a new baseline or automatically add newly discovered files.

## Learning Objectives

- Bash scripting and command-line argument handling.
- SHA-256 hashing.
- File integrity monitoring.
- Baseline creation and management.
- Linux file permissions and secure temporary-file handling.

## Challenge

[roadmap.sh — File Integrity Checker](https://roadmap.sh/projects/file-integrity-checker)
