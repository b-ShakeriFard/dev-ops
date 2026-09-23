# Install a Specific Ansible Version with `pip3`

## Objective

Install **Ansible 4.7.0** using `pip3` and make the Ansible commands available to every user on the machine.

## Why Use a System-Wide Installation?

Running `pip3 install --user` places the package inside the current user's home directory. Other users may not be able to execute that installation.

Using `sudo pip3 install` installs the package system-wide, normally placing its commands under `/usr/local/bin`, which is generally included in every user's `PATH`.

## 1. Confirm Python and pip3

```bash
python3 --version
pip3 --version
```

If `pip3` is unavailable on CentOS Stream, Rocky Linux, or RHEL, install it:

```bash
sudo dnf install -y python3-pip
```

## 2. Install the Required Ansible Version

Use `==` to request an exact package version:

```bash
sudo pip3 install ansible==4.7.0
```

Here:

- `sudo` performs a system-wide installation.
- `pip3` installs a Python 3 package.
- `ansible` is the package name.
- `==4.7.0` pins the installation to exactly version 4.7.0.

Do not use the following command for this challenge:

```bash
pip3 install --user ansible==4.7.0
```

The `--user` option limits the installation to the current account.

## 3. Verify the Installation

```bash
ansible --version
```

The output may show both the Ansible package version and its underlying `ansible-core` version. This is normal because the full `ansible` package bundles a compatible release of `ansible-core` plus additional collections.

Locate the executable:

```bash
which ansible
```

The expected location is commonly:

```text
/usr/local/bin/ansible
```

You can also verify the installed Python package:

```bash
pip3 show ansible
```

## 4. Confirm Other Users Can Run It

If another account exists, test Ansible through that account:

```bash
sudo -u another_user ansible --version
```

If the command is not found, inspect that user's path:

```bash
sudo -u another_user sh -c 'echo $PATH'
```

Ensure `/usr/local/bin` is present.

## Useful Maintenance Commands

```bash
# Display available versions
pip3 index versions ansible

# Upgrade or change to another exact version
sudo pip3 install --upgrade ansible==VERSION

# Remove the pip-installed package
sudo pip3 uninstall ansible
```

## Final Verification

```bash
ansible --version
command -v ansible
```

If both commands succeed and the required version is reported, the task is complete.
