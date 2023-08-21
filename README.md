# GalaxyGate Guides ![Docker CI](https://github.com/GalaxyGate/guides/workflows/Docker%20CI/badge.svg)

This repo is where users can submit guides for server administration, bot 
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

## Notes
* All documents are inside the `docs/` folder.
* Menu links are located on the `mkdocs.yml` file.
* Keep images locally refer to existing guides to see how it is done.
* Links are to be from related sources e.g. no short urls, tracking links or from obscure sites. 
* Run `mkdocs build --strict --verbose` and check for any errors before making a PR.