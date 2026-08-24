# infra

## Secrets

Secrets are stored as SOPS-encrypted `secrets.env` files. Plaintext `.env` files are never committed.

### Install

#### Windows

```powershell
winget install --id SecretsOPerationS.SOPS -e
winget install --id FiloSottile.age -e
```

#### macOS / Linux

Using Homebrew:

```bash
brew install sops age
```

Verify:

```bash
sops --version
age --version
```

### Single stack

Encrypt:

```bash
sops --encrypt --output stacks/example/secrets.env stacks/example/.env
```

Decrypt:

```bash
sops --decrypt --output stacks/example/.env stacks/example/secrets.env
```

### All stacks

#### Windows PowerShell

Encrypt all:

```powershell
Get-ChildItem stacks -Directory | ForEach-Object { $src = Join-Path $_.FullName ".env"; if (Test-Path $src) { sops --encrypt --output (Join-Path $_.FullName "secrets.env") $src } }
```

Decrypt all:

```powershell
Get-ChildItem stacks -Directory | ForEach-Object { $src = Join-Path $_.FullName "secrets.env"; if (Test-Path $src) { sops --decrypt --output (Join-Path $_.FullName ".env") $src } }
```

#### macOS / Linux

Encrypt all:

```bash
for dir in stacks/*; do [ -f "$dir/.env" ] && sops --encrypt --output "$dir/secrets.env" "$dir/.env"; done
```

Decrypt all:

```bash
for dir in stacks/*; do [ -f "$dir/secrets.env" ] && sops --decrypt --output "$dir/.env" "$dir/secrets.env"; done
```

## GitHub Actions deployment

Required repository variables:

- `VPS_HOST`
- `VPS_USER`

Required repository secrets:

- `VPS_SSH_KEY`
- `REPO_DEPLOY_KEY`
- `SOPS_SECRET_KEY`

See:

- `.github/workflows/deploy/variables.example`
- `.github/workflows/deploy/secrets.example`