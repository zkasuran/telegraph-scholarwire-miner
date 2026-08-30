# ScholarWire: keyless scholarly research for Telegraph

Three Telegraph canonical intents, served by one Cloudflare Worker with no API key and no
database. Every answer is read live at request time from public research sources, so nothing can
silently go stale.

- **ACADEMIC_SEARCH**: scholarly papers on a topic, from the OpenAlex works API with Crossref as
  a fallback. Names the top match in one sentence then lists the top three with authors, year,
  venue, citation count and DOI.
- **RESEARCH_QUERY**: a factual answer to a research question, from the Wikipedia REST summary
  API. The question is mapped to a page then answered in one or two plain sentences, with the
  page title, the canonical URL and the source behind it. OpenAlex is a scholarly backup.
- **RESEARCH_SYNTHESIS**: a short synthesis of a topic from more than one source. The Wikipedia
  definition leads, the most-cited OpenAlex works ground it, then every source is cited.

Live: <https://telegraph-scholar.margyn.workers.dev>

```bash
curl -s "https://telegraph-scholar.margyn.workers.dev/papers/quantum%20computing"
curl -s "https://telegraph-scholar.margyn.workers.dev/research?question=who%20discovered%20penicillin"
curl -s "https://telegraph-scholar.margyn.workers.dev/synthesis?topic=mRNA%20vaccines"
curl -s "https://telegraph-scholar.margyn.workers.dev/papers?question=find%20recent%20papers%20on%20large%20language%20models"
```

## Quality first

A paper list, a factual answer and a synthesis are things you can check, so the bar is to get
them right and state them clearly. Each summary is one plain answer to the question, then a
`Readings:` block that lists every figure behind it at the source's full precision. Citation
counts and years are stated exactly, never rounded. Both the raw integer and the grouped
form of a large count are given so either can be checked against a source that formats it the
other way. Every figure is a live read. Nothing is fabricated and nothing is cached beyond ten
seconds.

Example answers, produced by the worker from the live sources:

```
ACADEMIC_SEARCH  /papers/quantum%20computing
The top OpenAlex match for "quantum computing" is "Quantum Computing in the NISQ era and beyond"
by John Preskill (2018) in Quantum, cited 8,637 times. Readings: OpenAlex reports 871,800 works
matching "quantum computing"; top 3 by relevance: (1) ...

RESEARCH_QUERY  /research?question=who%20discovered%20penicillin
Penicillins are a group of β-lactam antibiotics originally obtained from Penicillium moulds,
principally P. chrysogenum and P. rubens. Readings: Wikipedia page "Penicillin" ...
```

## How it answers

Built on the lessons the sibling SkyWire and ChainWire miners learned against the live node:

- **Providers are raced, not tried in turn.** A validator spot check has a deadline, so a slow
  endpoint must not spend it. OpenAlex is tried first with Crossref behind it. The Wikipedia
  title and fulltext searches back each other up, all on short timeouts.
- **A whole question works as well as a structured field.** Every endpoint reads `?question=`,
  `?query=` or `?q=` and the path form. It parses the subject out of a natural question, so
  "who discovered penicillin" resolves the same as a bare topic.
- **An unfilled path template answers rather than errors.** The node probes declared paths with
  the template left in. A 400 on that probe reads as "miner did not respond" and freezes the
  miner out of routing for an epoch, so a template slot resolves to a sensible default subject
  and answers 200.
- **A ten second per-isolate memo.** A hot answer costs milliseconds, staleness bounded at the
  ten seconds the response advertises.
- **`/__last`** is a per-isolate ring buffer of recent requests, which is how the node's real
  call shape gets observed rather than guessed.
- **House style holds in the answer text.** Source prose is normalised so a long dash becomes a
  clause break and a serial comma is dropped. A sentence is never cut mid-word.

## Keyless sources

- **OpenAlex** works API, `https://api.openalex.org/works`. Title, year, citation count,
  authors, venue and DOI. No key.
- **Crossref** works API, `https://api.crossref.org/works`, the fallback for paper search.
- **Wikipedia REST** summary and search, `https://en.wikipedia.org/api/rest_v1/` and
  `https://en.wikipedia.org/w/rest.php/v1/search/`. The page summary, plus title and fulltext
  search to map a question to a page.

None of these need an API key. There are no secrets in the worker or in `wrangler.toml`.

## Endpoints

| Path | Intent | Example |
| --- | --- | --- |
| `/papers/{topic}` | ACADEMIC_SEARCH | `/papers/CRISPR%20gene%20editing` |
| `/papers?topic=&question=` | ACADEMIC_SEARCH | `?topic=quantum%20computing` |
| `/research/{question}` | RESEARCH_QUERY | `/research/Photosynthesis` |
| `/research?question=&topic=` | RESEARCH_QUERY | `?question=what%20is%20a%20black%20hole` |
| `/synthesis/{topic}` | RESEARCH_SYNTHESIS | `/synthesis/mRNA%20vaccines` |
| `/synthesis?topic=&question=` | RESEARCH_SYNTHESIS | `?topic=climate%20change` |
| `/health`, `/`, `/__last` | diagnostics | |

## Descriptors

One descriptor per intent, ready to register on the Telegraph registry:

- ACADEMIC_SEARCH, id 7325, `scholarwire-academic-search.yaml`
- RESEARCH_QUERY, id 7326, `scholarwire-research-query.yaml`
- RESEARCH_SYNTHESIS, id 7327, `scholarwire-research-synthesis.yaml`

## Deploy

```bash
wrangler deploy
```

No secrets, no bindings. Deploy is a bare `wrangler deploy`.

## Layout

- `worker.js`: the whole miner, one Cloudflare Worker module.
- `scholarwire-academic-search.yaml`, `scholarwire-research-query.yaml`,
  `scholarwire-research-synthesis.yaml`: the three descriptors.
- `wrangler.toml`: deploy config.

Written for the Telegraph network by [zkasuran](https://github.com/zkasuran) with AI assistance
(Claude, Anthropic).

## Licence

MIT.
