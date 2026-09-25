# 05 — Terraform Configuration: Fundamentals

Covers: block types, provider, resource, data, variable, output, terraform blocks

## Block types

| Block | Purpose |
|---|---|
| `provider` | connect Terraform to a platform (AWS, Azure, GitHub) |
| `resource` | infrastructure Terraform creates, updates, or deletes |
| `data` | read information about existing resources |
| `variable` | inputs that make config reusable |
| `output` | expose values after deployment |
| `terraform` | global settings — versions, backend |
| `module` | reusable grouped configuration |
| `import` | bring existing resources under management |

Not exhaustive, and you don't need all of them in a config.

## provider block

```hcl
provider "aws" {
  region  = "us-east-2"
  profile = "prd-workload"
}

provider "azurerm" {
  features {}
}
```

- Providers are plugins, developed separately from Terraform core, so features
  change per provider version
- Published by HashiCorp, partners, and the community
- Code lives on GitHub; **docs live at registry.terraform.io**
- Declared in config → downloaded to the machine running Terraform during `init`
- One provider block covers every resource for that platform
- Auth arguments belong in environment variables, not in the block

### Multiple providers with alias

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "prod"
  region = "us-west-1"
}

resource "aws_s3_bucket" "dev_bucket" {
  bucket = "my-dev-bucket"          # uses the default provider
}

resource "aws_s3_bucket" "prod_bucket" {
  provider = aws.prod               # explicitly uses the aliased one
  bucket   = "my-prod-bucket"
}
```

`provider` is a **meta-argument**. Syntax is `provider = <name>.<alias>`, with no
quotes. Used for multi-region, multi-account, multi-cloud.

## resource block

```hcl
resource "aws_instance" "web" {
  ami           = "ami-012345"
  instance_type = "t2.micro"

  tags = {
    Name = "prd-web-svr_01"
  }
}
```

- `"aws_instance"` — resource **type**, defined by the provider
- `"web"` — resource **name**, chosen by you, must be unique per config
- Referenced elsewhere as `aws_instance.web` (type + name), attributes as
  `aws_instance.web.id`

Naming rule: must start with a letter or underscore; may contain only letters,
digits, underscores, and dashes.

### Resource referencing

```hcl
resource "github_repository" "prod_repo" {
  name       = "prod-app-xyz-repo"
  visibility = "private"
}

resource "github_branch" "default" {
  repository = github_repository.prod_repo.name
  branch     = "main"
}
```

A reference creates an implicit dependency and puts it in the graph.

## data block

Reads existing infrastructure instead of creating it.

```hcl
data "aws_vpc" "prd" {
  filter {
    name   = "tag:Name"
    values = ["prd-vpc"]
  }
}

resource "aws_subnet" "pub" {
  vpc_id     = data.aws_vpc.prd.id
  cidr_block = "10.0.6.0/24"
}
```

Reference format: `data.<type>.<name>.<attribute>` — note the leading `data.`,
which resource references don't have.

Use it to avoid hardcoding IDs, and to connect to resources managed outside this
configuration.

## variable block

```hcl
variable "vsphere_datacenter" {
  description = "Name of datacenter"
  type        = string
  default     = "prd-workload-dc"
}
```

Arguments: `description`, `type`, `default`.

Reference with `var.<name>`, e.g. `var.vsphere_datacenter`.

### Types

**Primitive:** `string`, `number`, `bool`

**Complex:**

| Type | Syntax | Notes |
|---|---|---|
| `list` | `["t3.small", "t4g.micro"]` | ordered; index starts at 0 |
| `map` | `{ instructor = "bryan" }` | key/value; access `var.name.key` |
| `set` | `["subnet-1", "subnet-2"]` | unique values, **unordered** |

Lists are indexed: `var.vsphere_networks[0]`. Sets are not — you cannot index
into a set because it's unordered; convert to a list first. That distinction is
exam bait.

### Assigning values

1. `default` in the variable block
2. Environment variable: `export TF_VAR_vsphere_network="10.0.5.0/24"`
3. `.tfvars` file (auto-loaded if named `terraform.tfvars`):
   ```hcl
   vsphere_network = "10.0.5.0/24"
   enable_logging  = true
   ```
4. Command line: `terraform plan -var="enable_logging=true"`

### Order of precedence

Lowest to highest:

```
variable block default
  → environment variables (TF_VAR_*)
    → *.tfvars files
      → *.auto.tfvars files
        → command line flags (-var)
```

Command-line `-var` beats everything. **Memorize this order.**

## output block

```hcl
output "instance_public_ip" {
  description = "Public IP of Web server"
  value       = aws_instance.web.public_ip
}

output "website_url" {
  value = "https://${aws_alb.web.dns_name}"
}
```

Displays values after deployment and passes data between modules. The second
example shows string interpolation: `${...}` inside a quoted string.

## terraform block

Global settings for the project.

```hcl
terraform {
  required_version = "~> 1.10.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket = "prd-terraform-east-2"
    key    = "state/terraform.tfstate"
    region = "us-east-2"
  }
}
```

Three jobs: pin the Terraform version, declare required providers and their
versions, configure the backend.

Why it matters: without version constraints, a colleague who pulls your repo
downloads whatever provider version is latest and gets different behavior.

### Version constraints

| Constraint | Allows |
|---|---|
| `"1.9.8"` | exactly 1.9.8 |
| `">= 1.9.8"` | 1.9.8 or any later version |
| `"~> 5.87.0"` | 5.87.0 up to but not including 5.88.0 |
| `"~> 5.87"` | 5.87 up to but not including 6.0 |

`~>` (pessimistic constraint) lets only the **rightmost specified component**
increment. How many components you write changes what it permits — that's the
part people get wrong.

## Gotchas / exam bait

- Variable precedence order, lowest to highest: default → env vars → `.tfvars`
  → `.auto.tfvars` → `-var` flag
- `TF_VAR_<name>` is the env var prefix
- Data block references start with `data.`; resource references don't
- Sets can't be indexed; lists can. Lists start at 0.
- `provider = aws.prod` — meta-argument, unquoted
- `~> 5.87.0` allows 5.87.x only; `~> 5.87` allows 5.x from 5.87 up
- Provider docs are on the Terraform Registry, not GitHub
- Resource names must be unique per configuration, and can't start with a digit
- One provider block serves every resource of that platform

## Open questions

- (nothing yet)

## Correction to the course slides

The version-constraints slide describes `~> 1.11.0` as "v1.11.x or greater" and
`~> 5.87.0` as "greater or equal to 5.87.x". That's misleading. HashiCorp's docs
are explicit: `~> 1.0.4` allows 1.0.5 and 1.0.10 but **not** 1.1.0, and `~> 1.1`
allows 1.2 and 1.10 but not 2.0. So `~> 5.87.0` caps at 5.87.x — it does not
allow 5.88. The slide's own footnote ("~ allows only the right-most version
component to increment") is the correct rule. Trust the footnote, not the bullet.
