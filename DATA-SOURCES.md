# Data sources

Every figure this miner serves is a live read at request time. This file records, per source,
what it provides, what its own terms say about commercial use and redistribution, what credit it
requires and what its real rate limit is.

Two rules were followed in writing it. A licence is only recorded when the provider's own terms
page was read; where a page could not be read, that is stated as unverified rather than guessed.
And every source was called from a Cloudflare Worker before it went in, because several hosts
answer differently from a worker than from a laptop.

| Host | Provides | Licence | Commercial use | Attribution | Rate limit |
| --- | --- | --- | --- | --- | --- |
| www.ebi.ac.uk/europepmc | The finding that answers a research question, quoted from an open-access article's abstract | Per article, and the query asks only for CC BY 4.0 records. | Permitted by CC BY 4.0 on the articles selected. | Required by CC BY 4.0, with the licence named. | No key and no published quota. One to three searches per uncached research question. |
| api.openalex.org | Scholarly works, citation counts and venues | CC0 1.0 for the data. | Permitted by CC0. | Not required by CC0. Credited anyway. | Keyless tier gets $0.10 of usage per day and a full-text search costs $1 per 1,000 calls, so a shared Cloudflare egress IP answers 429 most of the time. Crossref carries the traffic. |
| api.crossref.org | Scholarly works metadata | No single SPDX id. The public pool is open. | Permitted in those words. | Not required. Credited anyway. | Public pool publishes its own limit in the response headers: x-rate-limit-limit 3, interval 1s, pool polite-array. |
| en.wikipedia.org | Encyclopedic answer text for a research question | CC BY-SA 4.0 | Permitted by CC BY-SA 4.0. | Required, with the licence named and modification stated. | Standard Wikimedia API etiquette. Two to four calls per uncached question. |

## Per source

### www.ebi.ac.uk/europepmc

The finding that answers a research question, quoted from an open-access article's abstract.

What the terms say: EMBL-EBI "places no additional restrictions on the use or redistribution of the data available via its Data Resources and Tools other than those provided by the original data owners"; Europe PMC records that "The respective copyright holders retain rights for reproduction, redistribution and reuse" and exposes each article's own licence, which is what the LICENSE:"cc by" filter selects on.

Commercial use: Permitted by CC BY 4.0 on the articles selected.

Attribution: Required by CC BY 4.0, with the licence named.

Credit line published in every answer:

    Finding quoted from an open-access article via Europe PMC, CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/), quoted from the abstract with the article named.

Rate limit: No key and no published quota. One to three searches per uncached research question.

Only the labelled conclusion of a CC BY article is quoted, and the article's own licence travels in the payload as `licence`. This exists because Crossref's metadata grant carves abstracts out: "Some abstracts contained in the metadata may be subject to copyright by publishers or authors." So an abstract is quoted only where its article's licence permits it, rather than on the strength of the metadata grant.

### api.openalex.org

Scholarly works, citation counts and venues.

Commercial use: Permitted by CC0.

Attribution: Not required by CC0. Credited anyway.

Credit line published in every answer:

    Scholarly metadata from OpenAlex, CC0 1.0.

Rate limit: Keyless tier gets $0.10 of usage per day and a full-text search costs $1 per 1,000 calls, so a shared Cloudflare egress IP answers 429 most of the time. Crossref carries the traffic.

OPEN ITEM: the terms of service could not be read (403 to every client), and the archived version contains a clause barring unauthorised republication that sits oddly with the CC0 grant on the data itself. Both facts are recorded rather than resolved. Only titles, years and citation counts are taken from it, never abstract text.

### api.crossref.org

Scholarly works metadata.

What the terms say: "No sign-up is required to use the REST API, and almost none of the metadata is subject to copyright" and "you may use it for any purpose."

Commercial use: Permitted in those words.

Attribution: Not required. Credited anyway.

Credit line published in every answer:

    Scholarly metadata from Crossref.

Rate limit: Public pool publishes its own limit in the response headers: x-rate-limit-limit 3, interval 1s, pool polite-array.

Titles, years, venues and citation counts only. Some abstracts "may be subject to copyright by publishers or authors", so abstract text from Crossref is never republished: a finding is quoted only from Europe PMC, where the article's own licence can be read and filtered on.

### en.wikipedia.org

Encyclopedic answer text for a research question.

Commercial use: Permitted by CC BY-SA 4.0.

Attribution: Required, with the licence named and modification stated.

Credit line published in every answer:

    Text from English Wikipedia, CC BY-SA 4.0, adapted (trimmed to the sentences that answer the question).

Rate limit: Standard Wikimedia API etiquette. Two to four calls per uncached question.

OPEN ITEM: CC BY-SA is share-alike, so an answer that reuses Wikipedia prose carries a share-alike obligation onto whatever embeds it. The credit line travels in the answer, and the on-chain projection carries the same field.

## Compliance

Met:

- www.ebi.ac.uk/europepmc: the required credit line travels in every answer and in NOTICE.
- api.openalex.org: the required credit line travels in every answer and in NOTICE.
- api.crossref.org: the required credit line travels in every answer and in NOTICE.
- en.wikipedia.org: the required credit line travels in every answer and in NOTICE.

Open, stated rather than hidden:

- api.openalex.org: OPEN ITEM: the terms of service could not be read (403 to every client), and the archived version contains a clause barring unauthorised republication that sits oddly with the CC0 grant on the data itself. Both facts are recorded rather than resolved. Only titles, years and citation counts are taken from it, never abstract text.
- en.wikipedia.org: OPEN ITEM: CC BY-SA is share-alike, so an answer that reuses Wikipedia prose carries a share-alike obligation onto whatever embeds it. The credit line travels in the answer, and the on-chain projection carries the same field.
