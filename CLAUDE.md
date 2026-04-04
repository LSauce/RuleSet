# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Clash proxy configuration repository containing domain/IP routing rules for network traffic management. The configuration enables selective proxy routing based on service categories (streaming, AI services, gaming, etc.) with automatic node selection.

## Repository Structure

```
RuleSet/
├── kiro-config.yaml           # Main Clash configuration (uses remote ACL4SSR rules)
├── kiro-config-override.yaml  # Override config (uses local ruleSet/ rules)
└── ruleSet/                   # Local rule files for specific services
    ├── openai.yaml           # OpenAI/ChatGPT domains
    ├── google.yaml           # Google services domains
    ├── microsoft.yaml        # Microsoft services
    ├── netflix.yaml          # Netflix domains
    ├── youtube.yaml           # YouTube domains
    ├── telegram.yaml          # Telegram IP ranges
    ├── telegramcidr.yaml     # Telegram CIDR blocks
    ├── private.yaml          # Private/LAN domains
    ├── lancidr.yaml          # LAN IP ranges
    ├── cncidr.yaml           # China IP ranges
    ├── direct.yaml           # Domains for direct connection
    ├── reject.yaml           # Domains to reject
    └── ...                   # Other service-specific rules
```

## Configuration Architecture

### kiro-config.yaml (ACL4SSR-based)
- Uses `rule-providers` referencing remote ACL4SSR GitHub repository
- Rules auto-update every 86400 seconds (24 hours)
- Health checks run every 600 seconds with `http://www.gstatic.com/generate_204`

### kiro-config-override.yaml (Local rules)
- Uses `rule-providers` pointing to `https://raw.gitmirror.com/LSauce/RuleSet/master/ruleSet/`
- References local ruleSet files in this repository
- Rule files should follow domain-list-community format with `+.example.com` syntax for wildcards

## Rule File Format

Domain rules use the `+.domain.com` prefix for domain matching:
```yaml
payload:
- "+.openai.com"
- "+.chatgpt.com"
- "specific.domain.com"
```

CIDR rules for IP ranges use standard notation.

## Adding New Rules

### To add a domain to an existing service rule:
1. Edit the appropriate file in `ruleSet/` (e.g., `openai.yaml` for AI services)
2. Add domain in format `+.example.com` for wildcard matching

### To create a new rule category:
1. Create new file in `ruleSet/` (e.g., `newservice.yaml`)
2. Add `rule-providers` entry in `kiro-config-override.yaml`:
   ```yaml
   newservice:
     type: http
     behavior: domain
     url: "https://raw.gitmirror.com/LSauce/RuleSet/master/ruleSet/newservice.yaml"
     path: ./ruleset/newservice.yaml
     interval: 86400
   ```
3. Add corresponding rule in `kiro-config-override.yaml` rules section

## Commit Message Convention

Use Chinese prefixes:
- `feat: ` - New features
- `fix: ` - Bug fixes
- `refactor: ` - Code refactoring
- Optional scope in parentheses: `feat(openai): `

Examples:
```
feat(openai): 添加新domain: chatgpt.com
fix: 删除重复规则
feat: 添加ronki.moe直连
```
