# ✏️ nano

> Editing text files with a simple terminal-based editor.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Opening Files](#opening-files)
- [Essential Shortcuts](#essential-shortcuts)
- [Searching](#searching)
- [Saving and Exiting](#saving-and-exiting)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Shortcut | Purpose |
|---|---|
| `Ctrl+O` | Save file |
| `Ctrl+X` | Exit nano |
| `Ctrl+W` | Search text |
| `Ctrl+\` | Search and replace |
| `Ctrl+K` | Cut current line |
| `Ctrl+U` | Paste cut text |
| `Alt+U` | Undo |
| `Alt+E` | Redo |
| `Ctrl+G` | Help |

---

## Overview

`nano` is a simple text editor that runs inside the terminal.

Open a file:

```bash
nano file.txt
```

If the file does not exist, nano can create it when you save.

It is useful for editing:

```text
Configuration files
Scripts
Notes
Service files
Application settings
```

---

## Opening Files

Open an existing file:

```bash
nano /etc/hosts
```

Edit a privileged file:

```bash
sudo nano /etc/ssh/sshd_config
```

Open at a specific line:

```bash
nano +25 file.txt
```

This starts at line 25.

---

## Essential Shortcuts

Nano displays shortcuts at the bottom of the screen.

The symbol:

```text
^
```

means:

```text
Ctrl
```

For example:

```text
^X
```

means:

```text
Ctrl+X
```

Cut current line:

```text
Ctrl+K
```

Paste:

```text
Ctrl+U
```

Undo:

```text
Alt+U
```

---

## Searching

Search:

```text
Ctrl+W
```

Enter the search term and press:

```text
Enter
```

Search and replace:

```text
Ctrl+\
```

Nano will ask for:

```text
Search text
Replacement text
```

---

## Saving and Exiting

Save:

```text
Ctrl+O
```

Nano asks for the filename.

Confirm with:

```text
Enter
```

Exit:

```text
Ctrl+X
```

If there are unsaved changes, nano asks whether to save them.

---

## Practical Examples

Edit Nginx configuration:

```bash
sudo nano /etc/nginx/nginx.conf
```

Create a Bash script:

```bash
nano backup.sh
```

Add:

```bash
#!/bin/bash

echo "Backup started"
```

Save and exit.

Then:

```bash
chmod +x backup.sh
```

---

## Common Pitfalls

### Permission Denied

You may need:

```bash
sudo nano FILE
```

### Accidentally Cutting a Line

`Ctrl+K` cuts the whole current line.

Restore it with:

```text
Ctrl+U
```

### Expecting Mouse-Driven Editing

Nano is primarily keyboard-driven, although mouse support may be available depending on configuration.

---

## Related Topics

- `cat.md`
- `less.md`
- `vi.md`
- `../../../08-Shell-Scripting/`

---

## Conclusion

For quick terminal editing, nano is straightforward:

```text
Ctrl+O → Save
Ctrl+W → Search
Ctrl+X → Exit
```

It is an excellent editor for simple Linux administration tasks.