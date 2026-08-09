# Security Playbook

A personal, structured knowledge base for web application security and bug bounty hunting — built as an Obsidian vault.

Every lab solved, writeup read, and lesson learned gets distilled into an atomic, linked note, so knowledge compounds over time instead of evaporating. The vault is organized by **domain first** (Web / API / LLM), with OWASP references kept as an in-note detail rather than the folder structure itself. It's designed to eventually be exposed to an AI coding agent (Claude Code) as a reference **Skill**.

## Table of Contents

- [Scope](#scope)
- [Repository Structure](#repository-structure)
- [Core Conventions](#core-conventions)
- [Disclaimer](#disclaimer)

## Scope

|Domain|Status|
|---|---|
|Web|Active|
|API|Active|
|LLM|Planned — folder exists, not yet populated|

## Repository Structure

```
Sec-Playbook/
├── 00-Inbox/                      # Unsorted quick captures
├── 01-Methodology/                # Recon, enumeration, exploitation, reporting workflows
├── 02-Vulnerability-Classes/      # Core technical knowledge, organized by domain first
│   ├── Web/
│   │   ├── Access-Control/        # IDOR, SSRF, Open-Redirect, Auth-Bypass
│   │   ├── Injection/             # XSS, SQLi, SSTI, Command-Injection, XXE, Prototype-Pollution
│   │   ├── Auth-Session/          # JWT-Attacks, OAuth-Misconfiguration
│   │   ├── Client-Side/           # CSRF, Clickjacking, CORS-Misconfiguration, DOM-Clobbering
│   │   ├── Server-Side/           # Insecure-Deserialization, File-Upload, Race-Conditions,
│   │   │                          # HTTP-Request-Smuggling, Web-Cache-Poisoning
│   │   ├── Business-Logic/        # Business-Logic-Flaws, Rate-Limit-Bypass
│   │   └── Infra/                 # Subdomain-Takeover, DNS-Misconfiguration
│   ├── API/                       # BOLA, Broken-Authentication, Broken-Object-Property-Level-Auth,
│   │                               # Unrestricted-Resource-Consumption, Broken-Function-Level-Auth,
│   │                               # Sensitive-Business-Flows, Improper-Inventory-Management,
│   │                               # Unsafe-API-Consumption, GraphQL-Attacks
│   └── LLM/                       # Prompt-Injection, Sensitive-Information-Disclosure, Supply-Chain,
│                                   # Data-Model-Poisoning, Improper-Output-Handling, Excessive-Agency,
│                                   # System-Prompt-Leakage, Vector-Embedding-Weaknesses, Misinformation,
│                                   # Unbounded-Consumption
├── 03-Writeups/                   # Own-words analysis of writeups read (dated, tagged)
├── 04-Labs/                       # Solved labs (PortSwigger / HTB / TryHackMe)
├── 05-Cheatsheets/                # Self-tested payloads and WAF bypasses
├── 06-Lessons-Learned/            # Cross-cutting patterns and mistakes to avoid
├── 07-Resources/                  # Curated blogs, people, books, courses
```

## Core Conventions

1. **Domain first, OWASP second.** Every vulnerability note lives under `Web/`, `API/`, or `LLM/` by what it fundamentally is, not by OWASP category. The relevant OWASP reference (e.g. `OWASP Web Top 10:2025 → A05 Injection`) is a section _inside_ the note, not the folder it lives in.
2. **No duplicate notes across domains.** If a technique spans two domains (e.g. SSRF is relevant to both `Web/Access-Control/` and the API world), it gets **one canonical note** in the domain it most fundamentally belongs to, and a short cross-link/pointer note (or just a link) from the other domain — never a full copy.
3. **Naming is `Title-Case-With-Hyphens.md`,** matching the technique's common name (e.g. `SSRF.md`, `Broken-Object-Level-Authorization.md`) — not tied to any external numbering scheme, since OWASP numbering can change between editions but the technique's name rarely does.
4. **No writeup content is ever copied verbatim.** Every writeup note is a summary and analysis in my own words.

## Disclaimer

This vault documents techniques for **authorized security testing only** — bug bounty programs, licensed labs, and CTFs. Nothing in this repository should be used against a system without explicit permission from its owner.