# No big comment headers
Do not allow big multiline comment headings that take a lot of visual space, eg surround line of text with lots of "#####" symbols

# No raw resources when possible
Resources should be defined as modules whenever possible. For example VPC shouldn't be created as a raw resource - there's an internal module for that in "git::https://github.com/diggerhq/common-modules//vpc". Same for other resources that have internal modules.
