---
name: research-uk-law
description: Always invoke this skill before calling any Solved MCP tool for any UK legal question. Use it to research official source-linked material instead of relying on memory or general web search.
when_to_use: Trigger when a user asks what UK law requires, permits, or means; asks about UK tax treatment, legislation, statutory interpretation, official guidance, or the law in force on a date; or requests official UK legal sources.
---

# Research UK law with Solved

Invoke this skill before any Solved MCP tool. Use Solved to research official UK
legal sources. The currently searchable sources are HMRC guidance and UK
legislation, and further official source types may be added. Do not limit this
skill's use to the current source list: invoke it for any UK legal question,
then use the source values the tools currently offer. Do not substitute model
memory or general web search for the Solved tools.

## Search-to-read evidence gate

Do not issue searches in parallel. Make one search call at a time. When a search
returns a relevant candidate, the next Solved content call must be
`read_legal_source` for that candidate. Do not make another search or state what
the candidate contains until it has been read. If a search returns no relevant
candidate, refine it or try another available source. This gate resets after
every search throughout the research session: earlier reads never permit two
later searches to be queued before the relevant returned candidate is read.

## Research workflow

1. Establish the `as_at` date.
   - Use the historical date the user specifies when it is today or earlier.
   - For a current-law question, use today's date from the session context.
   - Future dates are not supported. Do not pass a future date to `as_at`. For
     a prospective question, search as at today and clearly distinguish current
     or enacted material from predictions about a future position.
   - State the date used when it materially affects the answer.
2. Start with the source that explains the topic, then trace it to original
   authority.
   - For a tax question, search applicable official guidance first with the
     current `hmrc_guidance` source.
   - Read a relevant guidance result before any further search or answering.
     When that read exposes a relevant `references[].target_path`, follow it
     and read the underlying authority.
   - If no relevant guidance candidate is returned after a reasonable refined
     search, or the question is outside the available guidance, search the
     current `legislation` source directly.
   - As Solved adds source types, prefer an applicable official explanatory
     source first and use its references to reach the original authority.
3. Every `search_legal_sources` call must include an explicit `query`, exactly
   one `source`, and an explicit `as_at` date.
4. Treat search results only as locations of candidate sources. A title,
   snippet, match, or search ranking is never evidence and must never be
   represented as the source. Call `read_legal_source` on every result relied
   upon before describing what it says or making a source-derived claim.
5. Only read paths returned by Solved: a search result path, a relevant
   `references[].target_path`, or a navigation, cursor, or version path from a
   previous read. Never invent a path.
6. After reading a source, follow relevant references, navigate within the
   source, or refine the research as needed. Do not follow unrelated references
   indiscriminately. A search that returns no relevant candidate may be refined
   without a read.
7. Cite every source-derived factual claim with the read result's
   `https://solved.legal/...` URL as an inline Markdown link. Present each source
   separately by its identity and type, attribute its contribution explicitly,
   and explain how the sources relate. Do not blur distinct sources into one
   position. Preserve any uncertainty or historical limitation reported by a
   source.

## Coverage and failures

A search miss does not establish that a source is absent from Solved. Say only
that the search found no relevant result, then refine the search. Describe a
specific source or section as unavailable only when reading a Solved-returned
path establishes that result. Never invent an unavailable source or silently
fall back to memory.

If a source is missing, a tool fails, or a read appears empty or incorrect,
call `report_solved_feedback` promptly. Include the query, path, or URL and the
agent and model identifiers when known. Solved attaches the authenticated
account identity; do not ask the user for an email address.

Keep professional judgment central. Explain what the retrieved material
supports and make the evidence trail inspectable rather than presenting the
plugin as a substitute for professional advice.
