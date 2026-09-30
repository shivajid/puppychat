# Puppy Chat documentation

Documentation site for [Puppy Chat](https://puppychat.readthedocs.io/) — a warm macOS assistant workspace connected to self-hosted SGLang models.

Built with MkDocs + Material for MkDocs, hosted on Read the Docs.

## Edit the docs

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve    # preview at http://127.0.0.1:8000
```

Pushes to `main` rebuild the site automatically on Read the Docs.
