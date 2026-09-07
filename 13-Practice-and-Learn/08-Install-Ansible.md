# Installing Ansible 4.7.0 with pip3

## Scenario

The task was to install **Ansible version 4.7.0** on the **Jump Host** with one important restriction:

> Use `pip3` only.

The Ansible binary also had to be available **globally**, so every user on the system could run Ansible commands.

This made the task slightly more interesting than a normal package-manager installation.

---

## Goal

Install:

```text
ansible 4.7.0
```

using:

```text
pip3
```

and ensure that:

```bash
ansible --version
```

works system-wide.

---

## 1. Install Ansible with pip3

The correct installation command is:

```bash
sudo pip3 install ansible==4.7.0
```

### Why `sudo`?

Without `sudo`, pip may install the package only for the current user.

For example:

```bash
pip3 install --user ansible==4.7.0
```

would normally place executables under:

```text
~/.local/bin
```

That would not satisfy the requirement that Ansible be globally available.

Using:

```bash
sudo pip3 install ...
```

typically installs the executable under:

```text
/usr/local/bin
```

which is usually available in every user's `PATH`.

---

## 2. Verify the Installed Package

Check the Python package directly:

```bash
pip3 show ansible
```

Expected:

```text
Name: ansible
Version: 4.7.0
```

This is the most direct way to confirm that the required Ansible package version was installed.

---

## 3. Verify the Binary Location

Run:

```bash
which ansible
```

Expected output:

```text
/usr/local/bin/ansible
```

Check its permissions:

```bash
ls -l /usr/local/bin/ansible
```

You should normally see executable permissions similar to:

```text
-rwxr-xr-x
```

This means all users can execute the binary.

---

## 4. The Unexpected Problem

After installation, running:

```bash
ansible --version
```

produced an error referencing:

```text
/usr/bin/ansible
```

and an older Ansible Core installation.

However:

```bash
which ansible
```

showed:

```text
/usr/local/bin/ansible
```

This looked contradictory.

The reason was that the shell had cached the old command location.

Bash can remember where previously executed commands were found.

So even though the new binary existed in:

```text
/usr/local/bin/ansible
```

the current shell was still trying to execute:

```text
/usr/bin/ansible
```

---

## 5. Clear Bash's Command Cache

The fix was:

```bash
hash -r
```

This tells Bash to forget cached command paths and search the `PATH` again.

After that:

```bash
ansible --version
```

worked correctly.

---

## 6. Understanding the Version Output

One potentially confusing detail is that:

```bash
ansible --version
```

did **not** display:

```text
Ansible 4.7.0
```

Instead, it showed something similar to:

```text
ansible [core 2.11.12]
```

This is normal.

Ansible uses two related version numbers:

```text
Ansible community package → 4.7.0
ansible-core              → 2.11.x
```

The `ansible` command reports the **ansible-core** version.

The `pip3 show ansible` command reports the installed community package version.

Therefore:

```bash
pip3 show ansible
```

is the correct check for the challenge requirement.

---

## 7. Useful Verification Commands

```bash
pip3 show ansible
```

Verify the Ansible package version.

```bash
ansible --version
```

Verify that the CLI works.

```bash
which ansible
```

Check which binary will be executed.

```bash
type -a ansible
```

Show every `ansible` executable found in the current `PATH`.

Example:

```text
ansible is /usr/local/bin/ansible
ansible is /usr/bin/ansible
```

This is particularly useful when multiple installations exist.

---

## Troubleshooting Flow

```text
Install ansible==4.7.0 with pip3
        ↓
Check pip3 show ansible
        ↓
Check which ansible
        ↓
ansible --version fails
        ↓
Error references old /usr/bin/ansible
        ↓
Shell command cache suspected
        ↓
Run hash -r
        ↓
Run ansible --version again
        ↓
Success
```

---

## Key Commands

```bash
sudo pip3 install ansible==4.7.0

pip3 show ansible

which ansible

type -a ansible

hash -r

ansible --version
```

---

## Key Takeaways

1. A specific Python package version can be installed with:

```bash
pip3 install package==version
```

2. Use a global installation when all users need access to the executable.

3. `/usr/local/bin` commonly contains executables installed globally through `pip`.

4. Bash may cache the path of previously executed commands.

5. Use:

```bash
hash -r
```

when a newly installed executable is not being picked up correctly.

6. `ansible --version` reports the **ansible-core** version, while:

```bash
pip3 show ansible
```

reports the Ansible community package version.

---

## Final Result

The successful configuration was:

```text
Ansible package:     4.7.0
ansible-core:        2.11.12
Binary location:     /usr/local/bin/ansible
Global availability: Yes
Installation method: pip3
```

This task was a useful example of the difference between **package installation**, **binary resolution**, and **shell command caching**.
