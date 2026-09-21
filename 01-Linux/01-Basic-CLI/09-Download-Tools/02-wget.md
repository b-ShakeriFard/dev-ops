# 📥 wget

> Downloading files and web content from the command line, with support for resume and recursive retrieval.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Downloads](#basic-downloads)
- [Custom Filenames](#custom-filenames)
- [Resume Downloads](#resume-downloads)
- [Recursive Downloads](#recursive-downloads)
- [Useful Options](#useful-options)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `wget URL` | Download file |
| `wget -O FILE URL` | Save with custom filename |
| `wget -c URL` | Resume partial download |
| `wget -q URL` | Quiet mode |
| `wget --limit-rate=1m URL` | Limit download speed |
| `wget -r URL` | Recursive download |
| `wget --spider URL` | Check resource without downloading |
| `wget -i FILE` | Download URLs listed in a file |

---

## Overview

`wget` is designed primarily for retrieving files over protocols such as:

```text
HTTP
HTTPS
FTP
```

It works especially well for:

```text
Large downloads
Unattended downloads
Resuming interrupted transfers
Recursive retrieval
Batch downloads
```

---

## Basic Downloads

Download a file:

```bash
wget https://example.com/package.tar.gz
```

By default, `wget` saves the file using the remote filename.

Download into another directory:

```bash
wget -P /tmp \
https://example.com/package.tar.gz
```

---

## Custom Filenames

Save using a custom name:

```bash
wget -O package.tar.gz \
https://example.com/download?id=123
```

Be careful:

```bash
-O
```

means output file, not remote filename.

---

## Resume Downloads

Resume an interrupted download:

```bash
wget -c https://example.com/file.iso
```

This is especially useful for large files.

Without `-c`, a new download may restart from the beginning.

---

## Recursive Downloads

Download recursively:

```bash
wget -r https://example.com/docs/
```

Useful options include:

```bash
wget -r -np https://example.com/docs/
```

Where:

```text
-r  → recursive
-np → do not ascend to parent directory
```

For website mirroring:

```bash
wget --mirror https://example.com/docs/
```

Use recursive options responsibly.

---

## Useful Options

Quiet output:

```bash
wget -q URL
```

Limit speed:

```bash
wget --limit-rate=500k URL
```

Set timeout:

```bash
wget --timeout=10 URL
```

Retry several times:

```bash
wget --tries=3 URL
```

Check whether a resource exists:

```bash
wget --spider https://example.com/file.tar.gz
```

---

## Practical Examples

Download Linux ISO:

```bash
wget -c https://example.com/linux.iso
```

Download several URLs from a file:

```bash
wget -i urls.txt
```

Background download:

```bash
wget -b https://example.com/large-file.iso
```

Download with custom destination:

```bash
wget -O /tmp/app.tar.gz \
https://example.com/app.tar.gz
```

---

## Common Pitfalls

### URL Contains Special Characters

Quote URLs containing characters such as:

```text
&
?
=
```

Example:

```bash
wget "https://example.com/file?id=10&type=zip"
```

### Recursive Downloads Can Become Huge

This:

```bash
wget -r URL
```

may download far more than expected.

Consider:

```bash
-r
-np
--level
```

and inspect the target first.

### Overwriting with `-O`

This:

```bash
wget -O file.iso URL
```

writes directly to `file.iso`.

Make sure you are not replacing something important.

### Authentication or Redirect Problems

Use:

```bash
wget -S URL
```

to inspect server response headers.

---

## Related Topics

- `curl.md`
- `curl-vs-wget.md`
- `../../../06-Networking/`
- `../../../08-Shell-Scripting/`

---

## Conclusion

For most download-focused tasks, remember:

```bash
wget URL
wget -c URL
wget -O FILE URL
wget -i urls.txt
```

`wget` is especially strong when downloads need to be resumed, automated, repeated, or retrieved recursively.