# Linux Permissions and Ownership Cheat Sheet

Quick reference for reading, changing, and safely applying Linux file permissions and ownership.

## Inspect Permissions

```bash
ls -l
```

Example:

```text
drwxr-xr-x 2 achmad developers 4096 Jun  1 10:00 app
-rw-r--r-- 1 achmad developers  123 Jun  1 10:01 app.go
```

The first character shows the file type:

| Symbol | Type |
| --- | --- |
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device |
| `b` | Block device |

The next nine characters are permissions:

```text
rwxr-xr-x
|  |  |
|  |  +-- Others
|  +----- Group
+-------- Owner
```

## Permission Basics

| Permission | Symbol | Numeric value | Meaning |
| --- | --- | --- | --- |
| Read | `r` | 4 | View file contents or list directory names |
| Write | `w` | 2 | Modify file contents or create/delete entries in a directory |
| Execute | `x` | 1 | Run a file or enter/traverse a directory |

## Single Digit Values

Each permission digit is the sum of read, write, and execute values for one user class.

| Digit | Calculation | Permission | Meaning |
| --- | --- | --- | --- |
| `0` | none | `---` | No access |
| `1` | `x` | `--x` | Execute only |
| `2` | `w` | `-w-` | Write only |
| `3` | `w + x` | `-wx` | Write and execute |
| `4` | `r` | `r--` | Read only |
| `5` | `r + x` | `r-x` | Read and execute |
| `6` | `r + w` | `rw-` | Read and write |
| `7` | `r + w + x` | `rwx` | Full access |

Examples:

```text
7 = 4 + 2 + 1 = rwx
6 = 4 + 2     = rw-
5 = 4 + 1     = r-x
4 = 4         = r--
0 = 0         = ---
```

## Three Digit Modes

A complete numeric mode has three digits:

```text
755
| | |
| | +-- Others
| +---- Group
+------ Owner
```

So `755` means:

| User class | Digit | Permission |
| --- | --- | --- |
| Owner | `7` | `rwx` |
| Group | `5` | `r-x` |
| Others | `5` | `r-x` |

And `644` means:

| User class | Digit | Permission |
| --- | --- | --- |
| Owner | `6` | `rw-` |
| Group | `4` | `r--` |
| Others | `4` | `r--` |

## Common chmod Values

### `755` - Directories and Executable Scripts

```text
rwxr-xr-x
```

```bash
chmod 755 path
```

| User class | Permission |
| --- | --- |
| Owner | Read, write, execute |
| Group | Read, execute |
| Others | Read, execute |

Use for:

- Directories that users need to enter
- Executable scripts
- Application folders

### `644` - Regular Project Files

```text
rw-r--r--
```

```bash
chmod 644 file
```

| User class | Permission |
| --- | --- |
| Owner | Read, write |
| Group | Read |
| Others | Read |

Use for:

- Source code
- Text files
- Non-secret config files

### `640` - Group-Readable Config Files

```text
rw-r-----
```

```bash
chmod 640 config.env
```

Use when the owner can edit the file and the group can read it, but others should have no access.

### `600` - Secrets and Private Keys

```text
rw-------
```

```bash
chmod 600 secret.txt
```

Use for:

- SSH private keys
- Credential files
- Local secret files

### `700` - Private Directories

```text
rwx------
```

```bash
chmod 700 ~/.ssh
```

Use for:

- Private directories
- Personal scripts
- SSH directories

### `777` - Avoid by Default

```text
rwxrwxrwx
```

```bash
chmod 777 path
```

Everyone can read, write, and execute. Avoid this in production unless there is a specific and temporary reason.

## Ownership

Check owner and group:

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 achmad developers 123 app.go
```

In this example:

| Field | Value |
| --- | --- |
| Owner | `achmad` |
| Group | `developers` |

Change owner:

```bash
sudo chown achmad file.txt
```

Change owner recursively:

```bash
sudo chown -R achmad app
```

Change owner and group:

```bash
sudo chown achmad:developers file.txt
```

Change owner and group recursively:

```bash
sudo chown -R achmad:developers app
```

## Groups

View current user:

```bash
whoami
```

View current user's groups:

```bash
groups
```

Change file group:

```bash
sudo chgrp developers file.txt
```

## Symbolic chmod

| Symbol | Meaning |
| --- | --- |
| `u` | User/owner |
| `g` | Group |
| `o` | Others |
| `a` | All |

Examples:

```bash
# Add execute permission for everyone.
chmod +x script.sh

# Add execute permission for owner only.
chmod u+x script.sh

# Add write permission for group.
chmod g+w file.txt

# Remove write permission from others.
chmod o-w file.txt

# Remove all permissions from others.
chmod o-rwx file.txt
```

## Useful Recipes

Set directories to `755`:

```bash
find . -type d -exec chmod 755 {} \;
```

Set regular files to `644`:

```bash
find . -type f -exec chmod 644 {} \;
```

Make a script executable:

```bash
chmod +x deploy.sh
```

Equivalent numeric form:

```bash
chmod 755 deploy.sh
```

Remove execute permission:

```bash
chmod -x deploy.sh
```

Add write permission to owner:

```bash
chmod u+w file.txt
```

Remove write permission from others:

```bash
chmod o-w file.txt
```

## Recommended Defaults

### Most Software Projects

```bash
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;
```

### Web Applications

| Path type | Permission |
| --- | --- |
| Directories | `755` |
| Files | `644` |
| Sensitive config files | `640` or `600` |

### SSH

| Path | Permission |
| --- | --- |
| `~/.ssh` | `700` |
| Private key | `600` |
| Public key | `644` |

### Docker and Deployment Scripts

```bash
chmod 755 script.sh
```

## Quick Reference

| Permission | Symbolic form | Common use |
| --- | --- | --- |
| `777` | `rwxrwxrwx` | Avoid by default |
| `755` | `rwxr-xr-x` | Directories, scripts |
| `750` | `rwxr-x---` | Team directories |
| `700` | `rwx------` | Private directories |
| `666` | `rw-rw-rw-` | Rarely needed |
| `644` | `rw-r--r--` | Source code, normal files |
| `640` | `rw-r-----` | Group-readable config |
| `600` | `rw-------` | Secrets, private keys |
| `400` | `r--------` | Read-only private files |

## Safe Defaults

For most projects:

```bash
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;
```

For executable scripts:

```bash
chmod +x script.sh
```

For SSH private keys:

```bash
chmod 600 ~/.ssh/id_rsa
```

For project ownership:

```bash
sudo chown -R "$(whoami):$(whoami)" .
```
