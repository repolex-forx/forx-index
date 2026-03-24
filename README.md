# forx-index

The catalog of every open source repository parsed by [repolex](https://repolex.ai).

This repo contains JSON-LD manifests that form a lightweight knowledge graph of:
- **Repositories** — metadata, languages, stars
- **Parsed commits** — which versions have been analyzed
- **Dependencies** — the web of connections between projects

## Structure

```
repos/
  {org}/
    {repo}.jsonld              ← repo manifest (metadata + tracked commits)
manifests/
  {org}/
    {repo}/
      commit-manifest-{sha}.jsonld  ← per-commit detail (files, deps, sizes)
```

## Query with SPARQL

These JSON-LD files can be loaded into any RDF tool and queried with SPARQL:

```bash
pip install rdflib
```

```python
from rdflib import Graph

g = Graph()
g.parse("repos/TopQuadrant/shacl.jsonld", format="json-ld")
g.parse("repos/apache/jena.jsonld", format="json-ld")

# Find all repos that depend on Jena
for row in g.query("""
    SELECT ?repo WHERE {
        ?repo repolex:trackedCommit ?c .
        ?c repolex:dependsOn <https://repolex.ai/r/apache/jena> .
    }
"""):
    print(row.repo)
```

## Powered by

- [forx](https://github.com/repolex-ai/forx) — orchestrator
- [repolex](https://repolex.ai) — parser
- [lexq](https://github.com/repolex-ai/lexq) — query tool
