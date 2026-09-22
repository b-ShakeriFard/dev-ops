# Find and Copy Files While Preserving Directory Structure

## Objective

Find every `.css` file beneath `/var/www/html/ecommerce` and copy it to `/ecommerce` while preserving its original directory structure.

Preserving the structure matters because separate directories can contain files with the same name. A flat copy may cause filename collisions and skipped files.

## Successful Solution

First, move into the source directory:

```bash
cd /var/www/html/ecommerce
```

Then find and copy the files:

```bash
sudo find . -type f -name "*.css" -exec cp --parents {} /ecommerce/ \;
```

## Command Breakdown

- `find .` searches from the current directory.
- `-type f` selects regular files only.
- `-name "*.css"` selects filenames ending in `.css`.
- `-exec` runs a command for every matching file.
- `cp --parents` copies each file and recreates its parent directories.
- `{}` is replaced by the current matching pathname.
- `/ecommerce/` is the destination directory.
- `\;` terminates the `-exec` action. The backslash prevents the shell from interpreting the semicolon.

Using `find .` is important here. The returned paths begin with `./`, so `--parents` creates paths such as:

```text
/ecommerce/wp-content/example.css
```

Searching with the absolute source path could instead reproduce the entire `/var/www/html/ecommerce` prefix beneath the destination.

## Verification

Compare the number of source and destination files:

```bash
find . -type f -name "*.css" | wc -l
find /ecommerce -type f -name "*.css" | wc -l
```

Inspect a sample of the copied files:

```bash
find /ecommerce -type f -name "*.css" | head
```

For an exact relative-path comparison:

```bash
find . -type f -name "*.css" -printf '%P\n' | sort > /tmp/source-css.txt
find /ecommerce -type f -name "*.css" -printf '%P\n' | sort > /tmp/destination-css.txt
diff /tmp/source-css.txt /tmp/destination-css.txt
```

No output from `diff` means the file lists match.

## Troubleshooting Lessons

- **`find: missing argument to '-exec'`** — the command is missing its `\;` or `+` terminator.
- **`cp: failed to access '--parents'`** — place the option immediately after `cp`: `cp --parents ...`.
- **`cp: will not overwrite just-created ...`** — a flat copy encountered duplicate basenames. Preserve the parent directories instead.
- Quote `"*.css"` so the shell does not expand the pattern before `find` receives it.

## Reusable Pattern

```bash
cd SOURCE_DIRECTORY
sudo find . -type f -name "PATTERN" -exec cp --parents {} DESTINATION_DIRECTORY/ \;
```
