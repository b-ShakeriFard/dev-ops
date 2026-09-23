# Configure a Local YUM Repository

## Challenge

On the **backup server**, create a YUM repository named `yum_local` and point it to the local directory specified by the challenge. The repository must be enabled and usable by YUM/DNF.

> Replace `<repository-directory>` below with the exact absolute path supplied in the challenge.

## Repository Configuration

YUM and DNF read repository definitions from files ending in `.repo` under:

```text
/etc/yum.repos.d/
```

The required repository file does not need to exist beforehand—we create it ourselves.

## Step-by-Step Instructions

### 1. Connect to the backup server

From the jump host, connect using the correct backup-server account:

```bash
ssh <user>@<backup-server>
```

Confirm the current system:

```bash
hostname
```

### 2. Confirm the package directory

```bash
ls -ld <repository-directory>
ls <repository-directory> | head
```

If this is already a prepared repository, it should normally contain a `repodata` directory:

```bash
ls <repository-directory>/repodata
```

### 3. Create the repository file

```bash
sudo vi /etc/yum.repos.d/yum_local.repo
```

Press `i` and add:

```ini
[yum_local]
name=yum_local
baseurl=file:///<repository-directory-without-leading-slash>
enabled=1
gpgcheck=0
```

For example, if the directory is `/packages/downloaded_rpms`, use:

```ini
baseurl=file:///packages/downloaded_rpms
```

Press `Esc`, type `:wq`, and press Enter.

### 4. Inspect the saved configuration

```bash
cat /etc/yum.repos.d/yum_local.repo
```

### 5. Refresh repository information

```bash
sudo dnf clean all
sudo dnf makecache
```

### 6. Verify the repository

```bash
sudo dnf repolist
sudo dnf repoinfo yum_local
```

The output should list `yum_local` as an enabled repository.

## Lessons Learned

- `/etc/yum.repos.d/yum_local.repo` is a new configuration file; it is normal for it not to exist initially.
- The text inside `[yum_local]` is the repository ID used by commands such as `dnf repoinfo yum_local`.
- `name=` is the human-readable repository name.
- `baseurl=file:///...` points to a local absolute path. Three slashes are required: two belong to the `file://` scheme and the third begins the filesystem path.
- `enabled=1` activates the repository; `enabled=0` keeps it configured but inactive.
- `gpgcheck=0` disables RPM signature checking. This may be acceptable in a controlled lab, but production repositories should normally use signed packages and `gpgcheck=1`.
- A directory containing RPM files is not automatically a repository. It normally needs metadata under `repodata/`. When building a repository yourself, generate it with `createrepo_c <directory>`.
- YUM and DNF use compatible `.repo` definitions on modern RHEL-family systems.

## Interview Questions

### 1. Where are YUM repository configurations stored?

Usually under `/etc/yum.repos.d/` in files ending with `.repo`.

### 2. What is the difference between `baseurl` and `mirrorlist`?

`baseurl` identifies a specific repository location, while `mirrorlist` supplies a list of alternative mirrors.

### 3. Why does a local repository use `file:///`?

`file://` is the URI scheme, and the additional slash starts the absolute Linux path.

### 4. What does `gpgcheck=1` do?

It requires package signatures to be verified using a trusted GPG key.

### 5. How do you temporarily use a disabled repository?

```bash
sudo dnf --enablerepo=yum_local install <package>
```

### 6. How can you troubleshoot a repository?

```bash
sudo dnf clean all
sudo dnf repolist -v
sudo dnf repoinfo yum_local
```

Also verify the `baseurl`, directory permissions, repository metadata, network access when applicable, and GPG configuration.
