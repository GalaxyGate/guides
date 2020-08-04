# GalaxyGate Guides ![Docker CI](https://github.com/GalaxyGate/guides/workflows/Docker%20CI/badge.svg)

This Website is a place where users can submit guides for server administration, bot 
management and everything related.

We welcome all pull requests.

## Building

This guide uses `MkDocs` and is written using Markdown.

Pre-Requisites:
- python3

Install dependencies:
```bash
pip install mkdocs mkdocs-material pymdown-extensions pygments 
```

Start development server:
````bash
mkdocs serve
````

All documents are inside the `docs/` folder and menu links are located on the `mkdocs.yml` file.
