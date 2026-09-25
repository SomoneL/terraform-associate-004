# 04 — File Structure and Organization

Covers: Terraform File Structure and Organization

## How Terraform reads files

Terraform processes **all `.tf` files in the working directory together**, as one
configuration. It does not go into subdirectories unless you reference them (a
module source, for example).

Consequences:
- Splitting across files is purely for humans — one big `main.tf` behaves the same
- File order and resource order don't matter; the dependency graph handles it
- A resource in `network.tf` can reference a variable in `variables.tf` with no
  import or include

## Conventional file names

None of these are required. They're convention.

| File | Contents |
|---|---|
| `main.tf` | primary infrastructure resources |
| `variables.tf` | variable *definitions* |
| `outputs.tf` | output blocks |
| `providers.tf` | provider configuration and requirements |
| `terraform.tfvars` | variable *values* — usually gitignored |

You can also split by component: `network.tf`, `firewall.tf`, `dns.tf`,
`kubernetes.tf`.

Note the split: `variables.tf` declares what a variable is; `terraform.tfvars`
supplies its value. That distinction shows up on the exam.

## Files Terraform creates

| File | What it is | Version control? |
|---|---|---|
| `terraform.tfstate` | the state file | No |
| `terraform.tfstate.backup` | previous state, written before state is updated | No |
| `.terraform.lock.hcl` | provider version selections | **Yes** |
| `.terraform/` | cached providers and modules | No |

## One working directory = one state

```
terraform/
├── prod/      main.tf, variables.tf, outputs.tf + its own state
├── test/      "
├── dev/       "
└── projectX/  "
```

Each subdirectory is its own working directory with its own state file. This
separates environments and limits blast radius — an apply in `dev/` cannot touch
what `prod/` manages.

## Gotchas / exam bait

- Terraform reads every `.tf` in the working directory, but **not**
  subdirectories
- Splitting into multiple files changes nothing functionally
- `variables.tf` (definitions) vs `terraform.tfvars` (values)
- `terraform.tfstate.backup` is the *previous* state, written before each update
- Separate directories, not just separate files, is what separates state

## Open questions

- (nothing yet)
