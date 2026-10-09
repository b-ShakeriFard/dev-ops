# Linux GPG Encryption and Decryption Challenge

## Challenge Overview

This exercise introduced GNU Privacy Guard (`gpg`) and asymmetric encryption for the first time. The objective was to:

1. Use a supplied public key to encrypt a specified plaintext file.
2. Save the encrypted result using an exact required filename and extension.
3. Import the corresponding private key.
4. Decrypt an encrypted file to the required plaintext output.
5. Verify that both operations completed successfully.

The recipient identity used during encryption was:

```text
kodekloud@kodekloud.com
```

The encryption example used these paths:

```text
Plaintext input:  /home/encrypt_me.txt
Encrypted output: /home/encrypted_me.asc
```

The supplied public- and private-key paths depend on the exact challenge environment, so the commands below use placeholders where necessary.

---

## What Is GPG?

GPG, or GNU Privacy Guard, is a free implementation of the OpenPGP standard. It supports:

- Public-key encryption
- Symmetric encryption using a passphrase
- Digital signatures
- Signature verification
- Key creation, import, export and revocation

In this challenge, GPG used an asymmetric key pair:

| Key | Purpose | May it be shared? |
|---|---|---|
| Public key | Encrypt data for the key owner; verify their signatures | Yes |
| Private key | Decrypt data encrypted for the owner; create signatures | No |

```mermaid
flowchart LR
    A[Plaintext file] --> B[Encrypt with public key]
    B --> C[Encrypted file]
    C --> D[Decrypt with private key]
    D --> E[Recovered plaintext]
```

Possession of the public key does not allow someone to decrypt the resulting message. Decryption requires the matching private key.

---

## Important GPG Concepts

### Key pair

A mathematically related public key and private key. Data encrypted for the public key can be decrypted by the corresponding private key.

### User ID

A human-readable identity associated with a key, commonly containing a name and email address. In this exercise, GPG searched for:

```text
kodekloud@kodekloud.com
```

### Fingerprint

A long, unique identifier derived from a key. A full fingerprint is safer and less ambiguous than selecting a key by a short key ID or email address.

### Keyring

The collection of keys stored in a user's GPG home directory. Each Linux account normally has a separate keyring.

Typical locations include:

```text
/home/natasha/.gnupg
/root/.gnupg
```

### ASCII armor

A text representation of binary OpenPGP data. Armored files usually begin with a line such as:

```text
-----BEGIN PGP MESSAGE-----
```

The `--armor` option creates this format. The `.asc` extension conventionally indicates ASCII-armored data, although an extension alone does not change the file format.

---

## Step-by-Step Solution

## Part 1: Inspect the Provided Files

List the relevant directory before running GPG:

```bash
ls -l /path/to/keys/
file /path/to/keys/*
```

Check the source file:

```bash
ls -l /home/encrypt_me.txt
file /home/encrypt_me.txt
```

Inspect a public key without importing it:

```bash
gpg --show-keys /path/to/public-key.asc
```

For a key that is only readable by root:

```bash
sudo gpg --show-keys /path/to/public-key.asc
```

This reveals the key's user ID, key ID and fingerprint.

---

## Part 2: Understand Which GPG Keyring Is Being Used

The original attempt used `sudo gpg`. GPG therefore ran as `root` and created:

```text
/root/.gnupg
/root/.gnupg/pubring.kbx
```

This is an important distinction:

```bash
gpg --list-keys
```

reads the current user's keyring, while:

```bash
sudo gpg --list-keys
```

reads root's keyring.

Importing a key as one user and then encrypting as another causes GPG to search the wrong keyring.

The commands must therefore be consistent:

```text
gpg import       → gpg encrypt/decrypt
```

or:

```text
sudo gpg import  → sudo gpg encrypt/decrypt
```

For this lab, the workflow continued with `sudo gpg`, so the supplied keys were imported into root's GPG keyring.

In ordinary administration, prefer running GPG as the intended non-root account unless elevated privileges are actually required.

---

## Part 3: Import the Public Key

Import the provided public key:

```bash
sudo gpg --import /path/to/public-key.asc
```

List the imported public keys:

```bash
sudo gpg --list-keys
```

Display the fingerprint for the recipient:

```bash
sudo gpg --fingerprint kodekloud@kodekloud.com
```

The listing for a public key begins with `pub`:

```text
pub   ...
uid   ... kodekloud@kodekloud.com
```

Only the public key is needed for encryption.

---

## Part 4: Encrypt the Plaintext File

Use the public key to encrypt `/home/encrypt_me.txt` and write ASCII-armored output to `/home/encrypted_me.asc`:

```bash
sudo gpg --armor \
    --output /home/encrypted_me.asc \
    --encrypt \
    --recipient 'kodekloud@kodekloud.com' \
    /home/encrypt_me.txt
```

The command structure is:

```text
gpg --output ENCRYPTED-OUTPUT --encrypt --recipient RECIPIENT PLAINTEXT-INPUT
```

Or, more compactly:

```bash
sudo gpg -a -o /home/encrypted_me.asc \
    -e -r 'kodekloud@kodekloud.com' \
    /home/encrypt_me.txt
```

Long options are generally clearer in documentation and scripts.

### Why `--armor`?

The requested encrypted filename used `.asc`, so ASCII-armored output was appropriate. Without `--armor`, GPG normally produces binary OpenPGP data even if the filename ends in `.asc`.

---

## Part 5: Verify the Encrypted File

Confirm that the output exists:

```bash
sudo ls -lh /home/encrypted_me.asc
```

Identify its type:

```bash
sudo file /home/encrypted_me.asc
```

Inspect only the beginning of the armored file:

```bash
sudo head /home/encrypted_me.asc
```

Expected opening marker:

```text
-----BEGIN PGP MESSAGE-----
```

Inspect the OpenPGP packet structure without decrypting the message:

```bash
sudo gpg --list-packets /home/encrypted_me.asc
```

The encrypted output should not expose the original plaintext.

---

## Part 6: Import the Private Key

The corresponding private key is required for decryption:

```bash
sudo gpg --import /path/to/private-key.asc
```

Confirm that GPG now has a secret key:

```bash
sudo gpg --list-secret-keys --keyid-format LONG
```

A private-key entry begins with `sec`:

```text
sec   ...
uid   ... kodekloud@kodekloud.com
```

Compare this with `pub`, which represents a public key.

Importing a private key commonly imports or associates its public-key information as well.

---

## Part 7: Decrypt the Encrypted File

General syntax:

```bash
sudo gpg --output /path/to/plaintext-output \
    --decrypt /path/to/encrypted-input.asc
```

For example:

```bash
sudo gpg --output /home/decrypted_me.txt \
    --decrypt /home/encrypted_me.asc
```

The structure is:

```text
gpg --output PLAINTEXT-OUTPUT --decrypt ENCRYPTED-INPUT
```

No `--recipient` option is required during decryption. GPG reads the encrypted message, identifies which key was used, and searches the secret-key ring for the corresponding private key.

If the private key is passphrase-protected, GPG requests the passphrase through its pinentry mechanism.

---

## Part 8: Verify the Decrypted Output

```bash
sudo ls -l /home/decrypted_me.txt
sudo file /home/decrypted_me.txt
sudo cat /home/decrypted_me.txt
```

If you control both the original and decrypted files, compare them directly:

```bash
sudo cmp /home/encrypt_me.txt /home/decrypted_me.txt
```

No output from `cmp` means the files are identical. You can also compare their hashes:

```bash
sudo sha256sum /home/encrypt_me.txt /home/decrypted_me.txt
```

Matching SHA-256 values confirm that decryption recovered the original data exactly.

---

## The Failed Attempt and Why It Failed

The unsuccessful command resembled:

```bash
sudo gpg --output /home/encrypt_me.txt \
    --encrypt \
    --recipient 'kodekloud@kodekloud.com' \
    /home/encrypted_me.asc
```

GPG returned errors similar to:

```text
gpg: error retrieving 'kodekloud@kodekloud.com' via WKD: No data
gpg: kodekloud@kodekloud.com: skipped: No data
gpg: encryption failed: No data
```

There were two issues.

### Issue 1: The public key was not in the active keyring

Because the command used `sudo`, GPG searched root's keyring. It could not find a local key matching the recipient and attempted Web Key Directory discovery. That lookup returned no key.

The fix was to import the supplied public key into the same keyring used by the encryption command:

```bash
sudo gpg --import /path/to/public-key.asc
```

### Issue 2: Input and output were reversed

With GPG, the value following `--output` is the new destination. The final positional pathname is the existing input.

Incorrect conceptual order:

```text
--output plaintext-file ... encrypted-file
```

Correct encryption order:

```text
--output encrypted-file ... plaintext-file
```

Correct command:

```bash
sudo gpg --armor \
    --output /home/encrypted_me.asc \
    --encrypt \
    --recipient 'kodekloud@kodekloud.com' \
    /home/encrypt_me.txt
```

---

## Troubleshooting Guide

### `encryption failed: No data`

Likely cause: GPG cannot find a public key matching the recipient.

Check:

```bash
sudo gpg --list-keys
sudo gpg --fingerprint kodekloud@kodekloud.com
```

Then import the correct public key into the active keyring.

### `decryption failed: No secret key`

Likely causes:

- The matching private key was not imported.
- The private key was imported by another Linux user.
- The encrypted file targets a different key.

Check:

```bash
sudo gpg --list-secret-keys --keyid-format LONG
sudo gpg --list-packets /path/to/encrypted-file.asc
```

### `gpg: WARNING: unsafe permissions on homedir`

GPG expects its home directory to be private. Typical permissions are:

```bash
chmod 700 ~/.gnupg
```

Files inside the directory should also have appropriately restrictive permissions.

### `Permission denied` while creating output

Check the destination directory and existing output file:

```bash
ls -ld /path/to/output-directory
ls -l /path/to/output-file
```

Run GPG as the account that should own the result. Avoid broad permissions such as `chmod 777`.

### `File exists`

GPG may ask whether it should overwrite an existing output. First verify that the pathname is correct and that deleting or replacing the existing file is safe.

### Recipient selection is ambiguous

Use the full verified fingerprint rather than an email address:

```bash
sudo gpg --fingerprint kodekloud@kodekloud.com
sudo gpg --armor --output OUTPUT.asc \
    --encrypt --recipient 'FULL_FINGERPRINT' INPUT.txt
```

---

## Security Best Practices

1. Never publish or commit a private key to Git.
2. Verify a public key's fingerprint through a trusted channel before using it.
3. Run GPG as the intended user; use root only when the task genuinely requires it.
4. Protect `~/.gnupg` with restrictive permissions.
5. Use a strong passphrase for long-lived private keys.
6. Back up private keys securely and separately from the protected data.
7. Create and protect a revocation certificate for long-lived keys.
8. Avoid selecting important recipients by short key ID because collisions are possible.
9. Do not assume that a filename extension proves the actual format; inspect it with `file` or GPG.
10. Encryption provides confidentiality, but sender authentication normally requires a digital signature as well.

After a temporary lab, keys can be removed if the exercise permits it. Always identify the exact fingerprint first:

```bash
sudo gpg --list-secret-keys --keyid-format LONG
sudo gpg --list-keys --keyid-format LONG
```

Private keys must be removed before their corresponding public keys:

```bash
sudo gpg --delete-secret-keys FULL_FINGERPRINT
sudo gpg --delete-keys FULL_FINGERPRINT
```

Do not remove keys from a real system unless their ownership, backup status and continued need are fully understood.

---

## Useful Command Reference

| Purpose | Command |
|---|---|
| Inspect a key file | `gpg --show-keys KEYFILE` |
| Import a key | `gpg --import KEYFILE` |
| List public keys | `gpg --list-keys` |
| List private keys | `gpg --list-secret-keys` |
| Display fingerprints | `gpg --fingerprint` |
| Encrypt for a recipient | `gpg --output OUTPUT --encrypt --recipient RECIPIENT INPUT` |
| Create armored output | Add `--armor` or `-a` |
| Decrypt a file | `gpg --output OUTPUT --decrypt INPUT` |
| Inspect OpenPGP packets | `gpg --list-packets FILE` |
| Compare recovered file | `cmp ORIGINAL DECRYPTED` |
| Hash files | `sha256sum FILE1 FILE2` |

Remember to add `sudo` consistently when the selected keyring is root's keyring.

---

## Lessons Learned

1. Public keys encrypt; private keys decrypt.
2. Private keys must remain secret, while public keys are designed for distribution.
3. GPG keyrings belong to individual Linux users.
4. `gpg` and `sudo gpg` normally use different keyrings.
5. The key must be imported into the same account context that performs the operation.
6. `--output` specifies the new destination file; the final pathname is the input file.
7. A `.asc` extension does not automatically create ASCII armor; use `--armor` explicitly.
8. GPG does not require a `--recipient` during decryption because the encrypted message identifies the target key.
9. `pub` indicates a public key and `sec` indicates an available secret/private key.
10. GPG errors often describe a key-selection or keyring problem rather than a damaged input file.
11. Verification is part of the task: inspect the encrypted format and compare the decrypted output with the original when possible.
12. Exact filenames and paths matter in automation and graded lab environments.

---

## Interview Questions and Answers

### 1. What is GPG?

GPG is GNU Privacy Guard, an implementation of the OpenPGP standard used for encryption, decryption, digital signatures and key management.

### 2. What is the difference between a public key and a private key?

The public key may be distributed and is used to encrypt data for its owner or verify their signatures. The private key must remain secret and is used to decrypt data or create signatures.

### 3. Which key is used to encrypt a message for another person?

The recipient's public key.

### 4. Which key is used to decrypt the message?

The recipient's matching private key.

### 5. What is a GPG fingerprint?

It is a long identifier derived from the key. It is used to verify and select a key more reliably than a name, email address or short key ID.

### 6. Why did `sudo gpg` fail even though the key was imported with `gpg --import`?

The commands ran as different Linux users and therefore used different GPG home directories and keyrings.

### 7. Where does GPG store a user's keyring?

Normally under that user's `~/.gnupg` directory. Root's keyring is usually `/root/.gnupg`.

### 8. What does `--armor` do?

It encodes OpenPGP output as printable ASCII text with recognizable `BEGIN` and `END` markers rather than leaving it in binary form.

### 9. Does renaming a binary encrypted file to `.asc` make it ASCII-armored?

No. The extension is only a name. The `--armor` option controls the output representation.

### 10. Why is no recipient specified during decryption?

The encrypted data contains information that allows GPG to identify which private key is needed. GPG searches the active secret-key ring automatically.

### 11. What does `No secret key` mean?

GPG cannot find the private key required to decrypt the message in the active keyring.

### 12. What is the difference between encryption and signing?

Encryption provides confidentiality by preventing unauthorized reading. Signing provides authenticity and integrity by proving who signed the data and whether it was modified.

### 13. Can GPG both encrypt and sign a file?

Yes. A file can be encrypted for confidentiality and signed for authenticity and integrity.

### 14. What is symmetric GPG encryption?

Symmetric encryption protects data with a shared passphrase rather than a public/private key pair. Anyone with the passphrase can decrypt it.

Example:

```bash
gpg --symmetric --cipher-algo AES256 file.txt
```

### 15. How can you verify that decryption recovered the original file?

Use `cmp` for a byte-for-byte comparison or compare cryptographic hashes such as SHA-256.

---

## Final Checklist

- [ ] Locate the exact public and private key files.
- [ ] Inspect the public key and verify the recipient identity.
- [ ] Choose the correct Linux user and keyring.
- [ ] Import the public key.
- [ ] Confirm the recipient appears in `--list-keys`.
- [ ] Encrypt the correct plaintext input to the exact required output path.
- [ ] Use `--armor` when ASCII-armored output is required.
- [ ] Verify the encrypted output exists and contains an OpenPGP message.
- [ ] Import the corresponding private key.
- [ ] Confirm it appears under `--list-secret-keys`.
- [ ] Decrypt the correct encrypted input to the exact required plaintext path.
- [ ] Verify the recovered content.
- [ ] Keep private-key material out of Git and other untrusted locations.

## Result

The supplied public key was imported and used to encrypt the required file. The matching private key was then imported and used to decrypt the protected data successfully. This completed a first practical introduction to GPG keyrings, asymmetric encryption, decryption and verification.
