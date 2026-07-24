# Multiple GitHub Accounts on One PC (SSH)

## Problem

Git was pushing to the wrong GitHub account.

Error:

Permission to dev-playbook21/dev-playbook.git denied to NikStack20.

## Cause

The repository was using HTTPS and Git authenticated with the credentials of another GitHub account.

## Solution

- Created a dedicated SSH key for the second account.
- Added the public key to GitHub.
- Configured ~/.ssh/config with separate host aliases.
- Changed the repository remote from HTTPS to SSH.

## SSH Config

Host github-nik
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519

Host github-playbook
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_playbook

## Remote

git@github-playbook:dev-playbook21/dev-playbook.git

## Verification

ssh -T git@github-playbook

## Status

✅ Working