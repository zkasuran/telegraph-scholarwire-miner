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
| api.openalex.org | Scholarly works, citation counts and venues | CC0 1.0 for the data. | Permitted by CC0. | Not required by CC0. Credited anyway. | Keyless tier returns 429 from a Cloudflare edge IP under load, so Crossref leads. |
| api.crossref.org | Scholarly works metadata | No single SPDX id. The public pool is open. | Permitted in those words. | Not required. Credited anyway. | Public pool measured at 1 request per second with concurrency 1. |
| en.wikipedia.org | Encyclopedic answer text for a research question | CC BY-SA 4.0 | Permitted by CC BY-SA 4.0. | Required, with the licence named and modification stated. | Standard Wikimedia API etiquette. Two to four calls per uncached question. |

## Per source

### api.openalex.org

Scholarly works, citation counts and venues.

Commercial use: Permitted by CC0.

Attribution: Not required by CC0. Credited anyway.

Credit line published in every answer:

    Scholarly metadata from OpenAlex, CC0 1.0.

Rate limit: Keyless tier returns 429 from a Cloudflare edge IP under load, so Crossref leads.

OPEN ITEM: the terms of service could not be read (403 to every client), and the archived version contains a clause barring unauthorised republication that sits oddly with the CC0 grant on the data itself. Both facts are recorded rather than resolved.

### api.crossref.org

Scholarly works metadata.

What the terms say: "No sign-up is required to use the REST API, and almost none of the metadata is subject to copyright" and "you may use it for any purpose."

Commercial use: Permitted in those words.

Attribution: Not required. Credited anyway.

Credit line published in every answer:

    Scholarly metadata from Crossref.

Rate limit: Public pool measured at 1 request per second with concurrency 1.

Some abstracts "may be subject to copyright by publishers or authors", so abstract text is not republished.

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

- api.openalex.org: the required credit line travels in every answer and in NOTICE.
- api.crossref.org: the required credit line travels in every answer and in NOTICE.
- en.wikipedia.org: the required credit line travels in every answer and in NOTICE.

Open, stated rather than hidden:

- api.openalex.org: OPEN ITEM: the terms of service could not be read (403 to every client), and the archived version contains a clause barring unauthorised republication that sits oddly with the CC0 grant on the data itself. Both facts are recorded rather than resolved.
- en.wikipedia.org: OPEN ITEM: CC BY-SA is share-alike, so an answer that reuses Wikipedia prose carries a share-alike obligation onto whatever embeds it. The credit line travels in the answer, and the on-chain projection carries the same field.
