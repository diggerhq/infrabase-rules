# Write policies in natural language

This is an example repository of custom rules for [Infrabase](https://infrabase.co/). It's a GitHub app that detects security issues and other misconfigurations in your IaC pull requests. You can write Infrabase policies in natural language instead of OPA / Rego / Sentinel; this repo contains some examples.

## Quick start
1. Create an account at [dashboard.infrabase.co](https://dashboard.infrabase.co/)
2. Fork this repo (optional; only if you want to use custom rules)
3. Install the Infrabase GitHub app into the repos you'd like to review and take policies from (including your fork of this repo)
4. Create a pull request with some infrastructure changes. Shortly Infrabase will report on findings in the comment

<img width="916" alt="infrabase-analysis" src="https://github.com/user-attachments/assets/daea5c6d-5e27-4d91-9faa-c0b0e3e595be" />

## Motivation
Policy-as-code is one of those things that everyone knows should be done, but in practice is rarely implemented.

How come? We believe this is caused by the combination of the following 2 factors:
- OPA is cumbersome, so writing even a single policy takes a lot of effort
- Each policy project needs to start from scratch because policies aren't re-usable

Infrabase checks your infra with an LLM instead of policies directly (`gemini-2.5-pro-preview-05-06`). You can write your own policies as natural language prompts to customize behaviour - just fork this repo and connect it to Infrabase GitHub app.

## See the difference

✅ Infrabase policy - just one line!

> No two aws_security_group_rule resources may define an identical ingress rule (same security-group ID, protocol, from/to port, and CIDR block).

❌ Alternative: 61 lines of Rego:
<details>
  
```
package sg_unique

import future.keywords

# Helper: recursively yield every resource block (root + child modules)

all_resources[r] {
  # walk over the input object and pick leaves whose keys are "resources"
  some path, idx
  walk(input, path, reslist)
  last(path) == "resources"
  r := reslist[idx]
}

# Flatten each aws_security_group_rule resource into atomic "rule units"

unit_rules[u] {
  r := all_resources[_]
  r.type == "aws_security_group_rule"
  r.mode == "managed"                      # ignore data / config-driven
  is_ingress := r.values.type == "ingress"

  # explode cidr_blocks list; if empty treat as ["0.0.0.0/0"]
  cidr := r.values.cidr_blocks[_]
  when := is_ingress                       # only ingress rules in scope

  u := {
    "addr": r.address,                     # resource address for messaging
    "sg":   r.values.security_group_id,
    "proto": r.values.protocol,
    "from":  r.values.from_port,
    "to":    r.values.to_port,
    "cidr":  cidr
  }
}

# Detect duplicates: identical rule parameters but different resource addresses

dupes[[u1.addr, u2.addr, u1]] {
  u1 := unit_rules[_]
  u2 := unit_rules[_]
  u1.addr < u2.addr                       # deterministic ordering
  u1.sg   == u2.sg
  u1.proto == u2.proto
  u1.from  == u2.from
  u1.to    == u2.to
  u1.cidr  == u2.cidr
}


# Policy decision

deny[msg] {
  pair := dupes[_]
  ru   := pair[2]                         # representative rule params
  msg := sprintf(
    "Duplicate ingress rule (%s %d-%d %s on %s) in %s and %s",
    [ru.proto, ru.from, ru.to, ru.cidr, ru.sg, pair[0], pair[1]]
  )
}

default allow = count(deny) == 0

```

</details>

## Features
- Supports Terraform, OpenTofu, Terragrunt, CDK, CDKTF, CloudFormation, Pulumi
- Summary table of findings, ordered by severity. Works well in large monorepos
- Integrated with GitHub Advanced Security (GAS) alerts - findings are uploaded as SARIF files
- Write policies in natural language (fork this repo)

## Structure and conventions

Rules need to be stored in the `policies` folder as markdown files. No other folders or file types are considered.

Each rule file can be structured arbitrarily, but the more explicitly you separate and phrase them the better. General prompt engineering best practices apply. Suggested simple structure that can be a good start: for each rule, write a heading that summarises the key point - e.g. "no public S3 buckets allowed" - followed by a paragraph that adds more detail. For best results use short, definitive sentences. Examples in this repo follow this pattern.

You can have as many files as you want - so it might make sense to group them by topic. For example, `security.md` for security rules and `style.md` for code style rules.

## Troubleshooting

- Rules are not refreshed after commit: try removing the rules repo from the Infrabase github app installation and then adding again.
