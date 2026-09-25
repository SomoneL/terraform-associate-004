# 03 — Terraform CLI

Covers: Introduction to the Terraform CLI, Making the Most of the CLI

## Command structure

```
terraform <subcommand> [options or flags]
```

Example: `terraform plan -out=planfile`

- `terraform` — the base command
- `plan` — the subcommand
- `-out=planfile` — optional flag

OpenTofu uses the same shape: `tofu <subcommand> [options]`.

## Environment variables

Pass settings and credentials without hardcoding them in `.tf` files. This is
how you keep API keys out of version control.

```bash
export TF_LOG=DEBUG
export TF_VAR_svr_name="prod-db-01"
export AWS_ACCESS_KEY_ID="..."
```

PowerShell: `$Env:TF_LOG = "DEBUG"`
CMD: `setx TF_LOG "DEBUG"`

`TF_VAR_<name>` sets the Terraform input variable `<name>`. That naming
convention is exam material.

## terraform fmt

Rewrites config files to the canonical format and style.

```bash
terraform fmt              # current directory only
terraform fmt -recursive   # subdirectories too
```

## Help

```bash
terraform --help           # list all subcommands
terraform plan --help      # options for one subcommand
```

Worth knowing for the exam: `terraform plan -destroy` creates a plan to destroy
everything currently managed — the planning mode `terraform destroy` uses.

## Autocomplete

```bash
touch ~/.bashrc
terraform -install-autocomplete
```

Tab then completes subcommands, flags, and file paths. Bash and Zsh.

## Gotchas / exam bait

- `TF_VAR_<name>` is the env var prefix for input variables — not `TFVAR_` or
  `TF_<name>`
- `TF_LOG` controls log verbosity (see the troubleshooting section later)
- `fmt` is not recursive by default
- `fmt` changes formatting only, never behavior

## Open questions

- (nothing yet)
