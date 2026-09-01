# SSH Key Setup — Passwordless Server Access

Generate an SSH key pair and copy the public key to a remote server so you can log in without typing a password every time.

## Pre-checklist
- [ ] you have password-based SSH access to the target server (needed once, to copy the key over)
- [ ] `<USER>` and `<SERVER_IP>` below are replaced with the real username and host

---

## Linux / macOS (Bash)

```bash
# 1. Generate a new ed25519 key pair (press Enter to accept defaults, optionally set a passphrase)
ssh-keygen -t ed25519

# 2. Copy the public key to the server's authorized_keys
cat ~/.ssh/id_ed25519.pub | ssh <USER>@<SERVER_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"

# 3. Log in — should no longer prompt for a password
ssh <USER>@<SERVER_IP>
```

---

## Windows — PowerShell

```powershell
# 1. Generate a new ed25519 key pair
ssh-keygen -t ed25519

# 2. Copy the public key to the server's authorized_keys
Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub" | ssh <USER>@<SERVER_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"

# 3. Log in
ssh <USER>@<SERVER_IP>
```

---

## Windows — Command Prompt (CMD)

```cmd
:: 1. Generate a new ed25519 key pair
ssh-keygen -t ed25519

:: 2. Copy the public key to the server's authorized_keys
type "%USERPROFILE%\.ssh\id_ed25519.pub" | ssh <USER>@<SERVER_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"

:: 3. Log in
ssh <USER>@<SERVER_IP>
```

---

## What each step does
- `ssh-keygen -t ed25519` creates a private key (`id_ed25519`, keep secret) and a public key (`id_ed25519.pub`, safe to share). ed25519 is preferred over RSA — smaller keys, faster, equally secure.
- The middle command pipes the public key over SSH and appends it to `~/.ssh/authorized_keys` on the server, creating the `.ssh` directory if it doesn't exist yet.
- `chmod 700 ~/.ssh` and `chmod 600 ~/.ssh/authorized_keys` are required — SSH refuses to use `authorized_keys` if the permissions are too open.

## Gotchas
- If `~/.ssh/id_ed25519` already exists, `ssh-keygen` will prompt before overwriting it — don't overwrite a key you're still using elsewhere.
- The copy step still needs password auth to work once; if password auth is disabled on the server, add the public key via another route (cloud provider console, existing key, etc.).
- On Windows, `ssh-keygen` and `ssh` require the OpenSSH client feature to be installed (bundled by default on Windows 10/11).
- After confirming key-based login works, you can optionally disable password authentication in `/etc/ssh/sshd_config` (`PasswordAuthentication no`) for better security.
