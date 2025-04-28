# 2025-04-28: Understanding SSH Keys

## What Are SSH Keys

SSH keys are a **pair of files** used to **prove your identity securely** when connecting to servers (like GitHub, remote machines, etc.).

They work like a **lock and key system**:

| Part | Meaning | File Type |
|:---|:---|:---|
| **Private Key** | Your secret identity. You must protect it. | e.g., `github_rsa`, `id_ed25519` |
| **Public Key** | Your "badge" you can hand out. Servers (GitHub etc.) use it to recognize you. | e.g., `github_rsa.pub`, `id_ed25519.pub` |

## Why Two Keys?

Because of **asymmetric encryption**.

- The **private key** stays on your computer. (🔒 **Never share it!**)
- The **public key** is uploaded to the server (e.g., GitHub). (📢 **Safe to share**.)

When you try to connect:

1. GitHub says: *"Hey, prove you are you!"*
2. Your computer uses your **private key** to generate a response.
3. GitHub checks it against your **public key** stored in your account.
4. If they match → Access granted ✅

👉 **You don't even need to type passwords anymore.**

### Simple Explanation

| | Private Key | Public Key |
|:-|:--|:--|
| Secret? | Must stay private (only you) | Share freely (to servers) |
| Where? | On your computer | On GitHub (or server) |
| What for? | Proving who you are | Letting servers recognize you |
| Example filename | `github_rsa`, `id_ed25519` | `github_rsa.pub`, `id_ed25519.pub` |

## What Happens When You Generate SSH Keys?

When you run:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

You get:
- A private key file → (e.g., `~/.ssh/id_ed25519`) — **keep safe**.
- A public key file → (e.g., `~/.ssh/id_ed25519.pub`) — **upload to GitHub**.


## Common Commands with SSH

| Command | Purpose |
|:---|:---|
| `ssh-keygen` | Create a new SSH key pair. |
| `ssh-add ~/.ssh/your_key` | Add a private key to SSH agent (so it's remembered). |
| `ssh -T git@github.com` | Test connection to GitHub using SSH. |

## Summary on SSH Keys
### Important Rules

- **Private key must never be shared** — treat it like a password.
- **Public key is meant to be shared** — you upload it to GitHub, servers, etc.
- You can have **many pairs** — personal, work, school, etc.
- You can **choose which key to use** when you connect (using your `~/.ssh/config`).

### Visual Summary

```
[Your Computer]
   |
  [Private Key (secret)]
   |
(Secure communication handshake)
   |
  [Public Key (server)]
[GitHub Server]
```

- Private key stays local
- Public key is saved on server
- They "talk" to verify you without needing a password!




## More About GitHub SSH Keys

### When Your Computer Tries to Connect

When you do something like:

```bash
git push origin main
```

Git (through SSH) tries to **connect to** `git@github.com`.

At that moment, your computer:
- **Looks inside `~/.ssh/config`** (if it exists),
- **Loads the default SSH keys** into an agent (or tries all available keys),
- **Sends your public key information to GitHub** as a *proof of identity*.

### GitHub Server Looks for a Match

GitHub checks:
- *"Does this public key match any key stored in the user's GitHub account?"*
- If yes → GitHub accepts your connection ✅
- If not → GitHub refuses connection ❌ ("Permission denied (publickey)" error).

### Where Is Key Matching Configured?

| File | Role |
|:---|:---|
| **`~/.ssh/config`** | (Optional) Tells your computer **which private key** to use when connecting to a specific host (e.g., GitHub). |
| **GitHub Settings (SSH Keys)** | GitHub stores **your public keys** linked to your account. |

### Quick Scenarios

| Situation | What Happens |
|:---|:---|
| You have one key (default) | Git uses it automatically. |
| You have many keys but no config | Git tries them one by one (may cause delays or errors). |
| You have many keys and a good `~/.ssh/config` | Git uses the correct key instantly (best setup). |



### Practical Tips

- **If you have only one GitHub account** → No need to set up `~/.ssh/config` carefully.
- **If you have multiple GitHub accounts** → You should **configure `~/.ssh/config`** to tell which key to use for which GitHub server or project.
- **If you see a "Permission denied (publickey)" error** → Usually means wrong key being sent or no key sent at all.

### Example of a Good `~/.ssh/config` Setup

```bash
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_rsa
  UseKeychain yes
  AddKeysToAgent yes

Host github-sfu
  HostName github.sfu.ca
  User git
  IdentityFile ~/.ssh/sfu_github_rsa
  UseKeychain yes
  AddKeysToAgent yes
```

## Personal Reflection

Today, I learned that SSH keys provide a much safer and smoother authentication method compared to passwords. Understanding how public/private keys work together made it much easier to manage multiple accounts securely. Also, learning how key matching happens gave me deeper control and confidence over managing my GitHub and SSH setups!