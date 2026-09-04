# Changelog

All notable changes to aws-toolkit will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Fixed

- aws-ops agent frontmatter is valid strict YAML (`description` as a `>-` block
  scalar) — Claude Code now inventories the agent
- Install commands point at this repo's self-hosted marketplace instead of the
  nonexistent `epiccounty/plugins` hub
- Codex discovery: added `.agents/plugins/marketplace.json` plus the
  `plugins/aws-toolkit` dirlink bundle

### Changed

- License: MIT → Apache-2.0 (workspace convention); copyright holder Epic County
- Host-discovery trees (`.claude/skills`, `.codex/skills`, `.hermes/skills`,
  `.claude/agents`) are directory symlinks instead of per-file mirrors
- Removed the empty `commands/` directory and its manifest entry
- Manifest metadata: license/repository/homepage/keywords on all four manifests

## [0.1.0] - 2026-08

### Added

- Six skills: `aws-access`, `aws-ssm`, `aws-cli`, `aws-onboarding`, `aws-security`,
  `aws-services`
- `aws-ops` subagent for bulk AWS queries
- Multi-host manifests (Claude Code, Codex, agy, hermes)
