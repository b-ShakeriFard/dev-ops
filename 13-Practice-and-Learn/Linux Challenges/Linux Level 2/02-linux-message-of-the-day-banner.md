# Linux Message of the Day (MOTD) Challenge

## Objective

Configure the approved Nautilus **message of the day** on all application servers. The message must appear after a user successfully logs in.

The approved template is stored on the jump host:

```bash
/home/thor/nautilus_banner
```

The correct destination on each application server is:

```bash
/etc/motd
```

## Why `/etc/motd`?

Linux provides several types of login messages:

| File | Display time |
|---|---|
| `/etc/issue` | Before a local console login |
| `/etc/issue.net` | Before SSH authentication when configured in `sshd_config` |
| `/etc/motd` | After successful login |

Because the challenge says the banner must be displayed **upon a successful login**, `/etc/motd` is required.

## Server 1: `stapp01`

```bash
scp /home/thor/nautilus_banner tony@stapp01:/tmp/nautilus_banner
ssh tony@stapp01
sudo cp /tmp/nautilus_banner /etc/motd
sudo chmod 644 /etc/motd
exit
```

## Server 2: `stapp02`

```bash
scp /home/thor/nautilus_banner steve@stapp02:/tmp/nautilus_banner
ssh steve@stapp02
sudo cp /tmp/nautilus_banner /etc/motd
sudo chmod 644 /etc/motd
exit
```

## Server 3: `stapp03`

```bash
scp /home/thor/nautilus_banner banner@stapp03:/tmp/nautilus_banner
ssh banner@stapp03
sudo cp /tmp/nautilus_banner /etc/motd
sudo chmod 644 /etc/motd
exit
```

The file is first copied to `/tmp` because regular users normally cannot write directly to `/etc`. The privileged `sudo cp` command then installs it as `/etc/motd`.

## Verification

Open a fresh SSH session to each server:

```bash
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```

The Nautilus banner should appear immediately after authentication. You can also compare the installed file with the source:

```bash
cat /etc/motd
```

No SSH configuration change or service restart is required for `/etc/motd`.

