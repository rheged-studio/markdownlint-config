# markdownlint config

The estate's shared markdownlint configuration, published as `@rheged-studio/markdownlint-config`. It gives every Rheged Studio repo one consistent Markdown style without each repo restating the rules.

## Language

### The package

**Shared config**:
The single JSONC file, `.markdownlint.jsonc`, that the package publishes as its `main` entry and in `files`. It is the whole product; there is no code.
_Avoid_: Preset, ruleset, base config

**Consumer**:
A repo that depends on the package and pulls the shared config in through `extends`.
_Avoid_: Downstream, adopter, client

**Direct extension**:
The recommended way to consume the config: a consumer's `.markdownlint-cli2.jsonc` sets `config.extends` to the package name.
_Avoid_: Inheritance, import

**cli2 requirement**:
The constraint that `extends` resolution must run through `markdownlint-cli2`, because the shared config carries comments and only its parser stack reads JSONC. Bare `markdownlint` and `markdownlint-cli` reject the file.
_Avoid_: JSONC caveat

### The stance

**Strict on structure, lenient on prose**:
The package's philosophy. Rules that govern the document skeleton (headings, lists, links, tables, fences) stay on; rules that govern how prose is written (line length, inline HTML) are off.
_Avoid_: Opinionated mode

**Structural rule**:
An enabled rule that protects the document skeleton, such as heading shape, list numbering, link references, table shape or fence language tags.
_Avoid_: Strict rule

**Prose rule**:
A rule about how text reads rather than how a document is built. The shared config disables the two that cause the most friction: line length (`MD013`) and inline HTML (`MD033`).
_Avoid_: Style rule

**Baseline**:
`"default": true`, which turns on every markdownlint rule at its stock options before any decision is applied.
_Avoid_: Default ruleset

### Decisions

**Non-default decision**:
A rule the shared config sets differently from the baseline, either by changing its options or by switching it off. Only these are listed in the file.
_Avoid_: Customisation, tweak

**Inline rationale**:
The comment beside each non-default decision explaining why it exists. It is the record of intent and must be kept in step with the rule.
_Avoid_: Docs, annotation

**Re-enabled rule**:
A rule the predecessor package (from `protomolecule`) switched off that this package switched back on, such as `MD040` (fence language) and `MD041` (first-line H1).
_Avoid_: Restored rule, un-disabled rule

**Audit stamp**:
The dated header line recording which `markdownlint-cli2` and `markdownlint` versions the config was last checked against.
_Avoid_: Compatibility note

### Consumer adjustments

**Override**:
A rule setting a consumer adds beside `extends` to change a shared decision for itself, such as restoring `MD013` with a line length.
_Avoid_: Patch, local rule

**Opt-out**:
An override that turns a shared rule off entirely, for example `MD041: false` in a repo full of embeddable fragments.
_Avoid_: Exemption, exclusion

**Fragment**:
A Markdown file with neither a first-line H1 nor a frontmatter `title:`. It fails `MD041` unless the consumer opts out or ignores it.
_Avoid_: Partial, snippet file

**Language-less sample**:
A fenced code block with no real language, tagged `text`, `console` or `plaintext` so it satisfies `MD040`.
_Avoid_: Untagged block
