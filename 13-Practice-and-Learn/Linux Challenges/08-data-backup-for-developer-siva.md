# Data Backup for Developer — Siva

## Overview

This KodeKloud challenge involved creating a compressed backup of developer **Siva's** data and placing the archive in `/home` on the Jump Host.

Source directory:

```bash
/data/siva
```

Required archive:

```text
siva.tar.gz
```

Destination:

```bash
/home
```

The workflow was:

```text
/data/siva
   |
   | tar + gzip
   v
siva.tar.gz
   |
   | copy
   v
/home/siva.tar.gz
```

## Step 1 — Inspect the Source

```bash
ls -l /data/siva/
```

In the lab, the directory contained files such as:

```text
nautilus1.txt
nautilus2.txt
nautilus3.txt
```

## Step 2 — Create the Compressed Archive

```bash
tar -czvf siva.tar.gz /data/siva/
```

### Options

| Option | Meaning |
|---|---|
| `-c` | Create a new archive |
| `-z` | Compress with gzip |
| `-v` | Verbose output |
| `-f` | Specify archive filename |

So:

```bash
tar -czvf siva.tar.gz /data/siva/
```

means: create a gzip-compressed tar archive called `siva.tar.gz` from `/data/siva/`.

## About the `tar` Warning

You may see:

```text
tar: Removing leading '/' from member names
```

This is normal.

Instead of storing absolute paths like:

```text
/data/siva/nautilus1.txt
```

`tar` stores them as relative paths:

```text
data/siva/nautilus1.txt
```

This makes extraction safer.

## Step 3 — Verify the Archive

Check that it exists:

```bash
ls -l siva.tar.gz
```

Inspect its contents without extracting:

```bash
tar -tzf siva.tar.gz
```

Expected output should include entries such as:

```text
data/siva/
data/siva/nautilus1.txt
data/siva/nautilus2.txt
data/siva/nautilus3.txt
```

## Step 4 — Copy It to `/home`

A normal copy may fail:

```bash
cp siva.tar.gz /home/
```

with:

```text
Permission denied
```

because `/home` is normally root-controlled.

Use:

```bash
sudo cp siva.tar.gz /home/
```

## Step 5 — Verify the Destination

```bash
ls -l /home/
```

You should see:

```text
/home/siva.tar.gz
```

You can also verify the final archive directly:

```bash
sudo tar -tzf /home/siva.tar.gz
```

## Final Procedure

```bash
ls -l /data/siva/

tar -czvf siva.tar.gz /data/siva/

tar -tzf siva.tar.gz

sudo cp siva.tar.gz /home/

ls -l /home/
```

## Cleaner Alternative with `-C`

A cleaner archive path can be created with:

```bash
tar -czvf siva.tar.gz -C /data siva
```

This stores:

```text
siva/
siva/nautilus1.txt
siva/nautilus2.txt
siva/nautilus3.txt
```

instead of:

```text
data/siva/
...
```

## If the Destination Were Another Server

For a remote transfer, `scp` could be used:

```bash
scp siva.tar.gz user@server:/destination/
```

For repeated or larger transfers, `rsync` is often preferable:

```bash
rsync -av siva.tar.gz user@server:/destination/
```

## Common Mistakes

### Forgetting gzip compression

```bash
tar -cvf siva.tar /data/siva/
```

creates a tar archive, but not a `.tar.gz`.

Use:

```bash
tar -czvf siva.tar.gz /data/siva/
```

### Using the wrong filename

KodeKloud often validates exact names, so use:

```text
siva.tar.gz
```

### Forgetting permissions on `/home`

If normal `cp` fails, use:

```bash
sudo cp siva.tar.gz /home/
```

### Not verifying the archive

Always check:

```bash
tar -tzf siva.tar.gz
```

## Useful `tar` Commands

```bash
# Create gzip archive
tar -czvf archive.tar.gz directory/

# List contents
tar -tzf archive.tar.gz

# Extract
tar -xzvf archive.tar.gz

# Extract elsewhere
tar -xzvf archive.tar.gz -C /destination/
```

## Interview Questions

### What does `tar` do?

It combines files and directories into one archive while preserving directory structure and metadata.

### What does `-z` do?

It compresses the tar archive using gzip.

### What does `-t` do?

It lists archive contents without extracting them.

### Why did `tar` remove the leading `/`?

To avoid storing absolute paths, which makes extraction safer.

### Why was `sudo` needed for `/home`?

Because normal users typically cannot create files directly under `/home`.

### What is the difference between `tar` and `scp`?

`tar` creates archives; `scp` transfers files between systems over SSH.

## Key Takeaway

The essential solution was:

```bash
tar -czvf siva.tar.gz /data/siva/
sudo cp siva.tar.gz /home/
```

This is a classic Linux backup workflow: **archive, compress, move, verify**.
