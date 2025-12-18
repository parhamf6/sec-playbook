# sec-playbook

A personal security playbook: concise, practical notes, labs, and journals for web vulnerabilities, CTFs, boxes, and hands-on learning.

## About
`sec-playbook` is a learning-first repository for documenting attack classes (XSS, SQLi, SSRF, etc.), writeups, labs, and personal journals. Intended for authorized, educational use only.

## Repo structure (example)
- `attack-playbook/` — concept-focused folders (xss, sqli, ssrf, ...) with theory, payloads, labs, journal, defenses  
- `ctf/` — challenge writeups and artifacts  
- `boxes/` — HTB / similar box writeups  
- `tools/` — scripts, PoCs, helpers  
- `assets/` — screenshots, pcaps, binaries (use Git LFS for large files)  
- `templates/` — writeup & journal templates

## How to use
- Browse by folder (concept → practice → journal).  
- Use templates in `templates/` for consistent writeups.  
- Keep sensitive data out of the repo (no real credentials or private data).

## Spoilers & content policy
- Mark spoilers with `<details>` blocks or a `spoiler: true` frontmatter tag.  
- This repo documents **authorized** labs/CTFs only. Do **not** publish exploits for unpatched production systems.

## Contributing
PRs welcome (formatting, new writeups, templates). See `CONTRIBUTING.md` for guidelines.

## License
Choose a license (e.g., `MIT`) and add `LICENSE` to the repo.

