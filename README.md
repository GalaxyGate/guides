# GalaxyGate Guides [![Build Status](https://travis-ci.com/GalaxyGate/guides.svg?branch=master)](https://travis-ci.com/GalaxyGate/guides)

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
