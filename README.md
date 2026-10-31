# Windows VPS: read-only GitHub repository access

Public setup instructions only. This repository contains no game source, game assets, credentials, server addresses or deployment payload. Run the block below **on the Windows VPS**, in PowerShell, under the account that will read the private repository.

## 1. Create a dedicated SSH key

Copy and paste this entire block:

```powershell
$ErrorActionPreference = 'Stop'
$keyPath = Join-Path $env:USERPROFILE '.ssh\dynablaster_readonly'
New-Item -ItemType Directory -Force (Split-Path -Parent $keyPath) | Out-Null
if ((Test-Path -LiteralPath $keyPath) -or (Test-Path -LiteralPath ($keyPath + '.pub'))) {
    throw 'A key or public-key file already exists at this path. Do not overwrite it.'
}
if (-not (Get-Command ssh-keygen -ErrorAction SilentlyContinue)) {
    throw 'ssh-keygen is unavailable. Ask the administrator to provide OpenSSH tools; this block installs nothing.'
}
ssh-keygen -t ed25519 -f $keyPath -C 'dynablaster-vps-readonly'
if ($LASTEXITCODE -ne 0) { throw 'Key generation failed.' }
Write-Host 'Share ONLY this public key (.pub):'
Get-Content -LiteralPath ($keyPath + '.pub')
```

The key generator asks for a passphrase and confirmation. Choose a passphrase if desired; retain it locally. Never paste a private key or passphrase into GitHub issues or chat. If generation is cancelled, stop and inspect the files before trying again.

The line to share starts with `ssh-ed25519`. It is the **public** key. The similarly named file **without** `.pub` is private and stays on the VPS.

## 2. Register read-only access

The private repository's owner opens **Settings → Deploy keys → Add deploy key**, pastes the public key, and leaves **Allow write access unchecked**.

A deploy key grants access to one repository. Do not add a personal full-account token or reuse a production service's SSH key for this setup.

## 3. What this does not do

This creates a key only. It does not install Git/OpenSSH, clone the game, run scripts from the internet, deploy a browser client, open a port, change firewall rules, or alter any existing STALKER service. A tested browser demo and its exact pull/build/run instructions are a separate deliverable.

The intended demo access policy is a dedicated endpoint allowed from the owner's and two friends' public IPs; actual addresses and server configuration must not be posted in this public repository. This key does not enforce that network policy.

Reference: [GitHub deploy keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys).

## Observed

These are prepared PowerShell instructions for generating a dedicated key on a Windows VPS. The private game repository is separate.

## Inferred (not verified)

A read-only deploy key is suitable for owner-operated retrieval of one private repository.

## Unknown

OpenSSH availability, existing key state, passphrase choice and successful registration on the target VPS.

## Not checked

- This command block has not been executed on the VPS; no key, clone, service, firewall rule or game deployment is created by publishing these instructions.
