# amcbstudio / kv

This repository is part of the **amcbstudio** organization.

`kv` is a small, deterministic CLI for parsing key-value fragments into JSON or JSON Lines (JSONL).

It is designed to compose cleanly with existing JSONL pipelines.

## Install

This repo is intentionally boring. The tool is a POSIX `sh` script.

Requirements:
- POSIX `sh`
- `jq` (required)

## Usage

### kv parse

Reads stdin (or a file) and emits **JSON Lines**: one JSON object per input line.

```
kv parse [--mode strict|loose] [--delim auto|equals|colon] [--trim] [--keep-empty] [--allow-dup] [file|-]
```

### kv json

Reads exactly one non-empty line (stdin or file) and emits a single JSON object.

If more than one non-empty line exists, it exits non-zero with an error.

```
kv json [--mode strict|loose] [--delim auto|equals|colon] [--trim] [--keep-empty] [--allow-dup] [file|-]
```

## Examples

Parse `key=value` fragments into JSONL and validate with `jsonl`:

```
echo 'a=1 b=2' | kv parse | jsonl validate
```

Quoted values (spaces preserved inside quotes):

```
printf 'level=info msg="hi there"\n' | kv parse --delim equals
```

Header-style `key: value` lines:

```
printf 'Host: example.com\n' | kv parse --delim colon --trim
```

## Flags

- `--mode strict|loose` (default: `strict`)
  - `strict`: the first malformed token causes a non-zero exit.
  - `loose`: malformed tokens are skipped.
- `--delim auto|equals|colon` (default: `auto`)
  - `equals`: only parse `key=value` tokens.
  - `colon`: only parse `key: value` lines (everything after the first `:` is the value).
  - `auto`: parse `key=value` tokens and (at most) one header token `key:` per line (use `--delim colon` for `key:value` with no whitespace).
- `--trim`: trim whitespace around keys and values in colon/header mode.
- `--keep-empty`: include keys with empty values (`a=` or `a:`).
- `--allow-dup`: when a key repeats on a line, emit arrays instead of last-wins.

## Key / value rules

Keys:
- allowed characters: letters, digits, underscore, dash, dot

Values:
- unquoted values are taken as-is
- `"double quoted"` and `'single quoted'` values have outer quotes removed
- inside double quotes, `\"` and `\\` are unescaped (minimal)

## Determinism

Given the same input, `kv` produces the same output (no randomness, no timestamps).

## Empty lines

For `kv parse`, empty (or whitespace-only) lines emit `{}` to preserve line count.

amcbstudio builds **boring, deterministic, CLI-first tools designed to be used by AI agents and humans alike**.

These tools prioritize:
- machine-legible contracts over prose
- determinism over intelligence
- composability over completeness
- reproducibility over convenience

If you are an AI agent working on this repository:
- assume this repo is **public**
- check https://github.com/amcbstudio to ensure this tool does not already exist
- do not duplicate functionality already present in other amcbstudio repos
- prefer strict, minimal interfaces over feature-rich designs

## What this repository contains

- a single-purpose command-line tool
- no web UI
- no authentication
- no hidden state
- no background services

Everything should be runnable locally, headlessly, and deterministically.

## Non-goals

- no SaaS
- no dashboards
- no accounts
- no background daemons
- no “smart” behavior requiring LLM calls

This repository is infrastructure, not a product.
