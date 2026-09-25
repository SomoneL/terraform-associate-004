# 01 — Terraform Foundations

Covers: Introduction to Terraform, HCL Basics

## What it is

IaC tool from HashiCorp. Provisions and manages infrastructure from config
files instead of console clicks. Platform agnostic — any cloud, plus non-cloud
providers (GitHub, Vault, Kubernetes, DNS).

Declarative: you describe the end state, Terraform figures out the steps and
the order. Imperative would be "step 1 build VM, step 2 attach network."

Desired state = what's in your config. Terraform compares it against real-world
resources and makes whatever changes close the gap.

## Core components

| Component | What it is |
|---|---|
| Terraform Core | The CLI binary. Reads config, builds the graph, calls providers. |
| Providers | Plugins that talk to a specific platform's API (aws, azurerm, github). |
| Resources | The actual infrastructure objects Terraform manages. |
| State | The map between config and real-world resources. |
| Modules | Reusable, callable blocks of config. |

## Editions

- **Community** — free, CLI only, local state by default
- **HCP Terraform** — HashiCorp-hosted SaaS, remote state and runs
- **Terraform Enterprise** — self-hosted version of the same platform

## Terraform vs other tools

Similar (IaC, provisioning): CloudFormation, Azure Bicep, Pulumi.
Different (config management — installs packages, manages files on existing
machines): Ansible, Chef, Puppet. These are complementary, not competitors —
Terraform builds the box, Ansible configures what's on it.

## HCL syntax

```hcl
block_type "label" "label" {
  argument = expression_or_value
}
```

Real example:

```hcl
resource "aws_vpc" "vpc" {
  cidr_block = var.vpc_cidr
}
```

- `resource` = block type
- `"aws_vpc"` = resource type (which provider + what kind of thing)
- `"vpc"` = name I chose
- Referenced elsewhere as `aws_vpc.vpc.<attribute>`

Resource referencing is what builds the dependency graph. Referencing
`aws_vpc.vpc.id` in another resource creates an implicit dependency, and
Terraform orders creation accordingly. No `depends_on` needed for that case.

## Files and formatting

- `.tf` for config, `.tfvars` for variable values
- `#` for comments
- Two-space indent, align the `=` signs
- `terraform fmt` does the formatting for you
- Convention: split into `main.tf`, `variables.tf`, `outputs.tf`

## Gotchas / exam bait

- Declarative ≠ imperative — Terraform decides the how and the order
- Terraform provisions; it does not configure software inside a machine
- Implicit dependency (a reference) vs explicit (`depends_on`)
- HCP Terraform and Terraform Enterprise are the same platform, hosted
  differently — SaaS vs self-hosted


