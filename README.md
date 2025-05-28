# Write policies in natural language

An example repo that demonstrates how to use rules with Infrabase. You'd want to fork this repo, install infrabase GitHub app into your private fork, and gradually develop your own set of rules.

## Overview

The rules repo contains rules for Infrabase to use in make decisions when it reviews your infrastructure PRs. A bit like OPA policies, except that Infrabase rules are free form text and can capture fairly complex logic concisely. You can think of rules as prompts - which they are! Infrabase provides the full set of rules as context to the model alongside code for review.

## Motivation

We wanted to provide a way to customise Infrabase for each scenario. A common way to customise an AI application is to expose promt(s) one way or another. Loading rules from a dedicated repo means that they are captured "as code" and can be audited, versioned and access-controlled.

## Structure and conventions

Rules need to be stored in the `policies` folder as markdown files. No other folders or file types are considered.

Each rule file can be structured arbitrarily, but the more explicitly you separate and phrase them the better. General prompt engineering best practices apply. Suggested simple structure that can be a good start: for each rule, write a heading that summarises the key point - e.g. "no public S3 buckets allowed" - followed by a paragraph that adds more detail. For best results use short, definitive sentences. Examples in this repo follow this pattern.

You can have as many files as you want - so it might make sense to group them by topic. For example, `security.md` for security rules and `style.md` for code style rules.

## Troubleshooting

- Rules are not refreshed after commit: try removing the rules repo from the Infrabase github app installation and then adding again.
