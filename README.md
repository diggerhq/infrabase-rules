# Write policies in natural language

This is an example repository of custom rules for [Infrabase](https://infrabase.co/). It's a GitHub app that detects security issues and other misconfigurations in your IaC pull requests. You can write Infrabase policies in natural language instead of OPA / Rego / Sentinel; this repo contains some examples.

## Quick start
1. Create an account at [infrabase.co](https://infrabase.co/)
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

## How it works


## Structure and conventions

Rules need to be stored in the `policies` folder as markdown files. No other folders or file types are considered.

Each rule file can be structured arbitrarily, but the more explicitly you separate and phrase them the better. General prompt engineering best practices apply. Suggested simple structure that can be a good start: for each rule, write a heading that summarises the key point - e.g. "no public S3 buckets allowed" - followed by a paragraph that adds more detail. For best results use short, definitive sentences. Examples in this repo follow this pattern.

You can have as many files as you want - so it might make sense to group them by topic. For example, `security.md` for security rules and `style.md` for code style rules.

## Troubleshooting

- Rules are not refreshed after commit: try removing the rules repo from the Infrabase github app installation and then adding again.
