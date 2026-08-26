# UGTPHON — Moderation Sidecars

Per-sentence safety scores from OpenAI's content moderation API, kept as a
standalone annotation layer so the dataset files (`{lang}/{split}.json`) stay
free of non-essential fields.

## Model

| | |
|---|---|
| Provider / API | OpenAI `client.moderations.create(...)` |
| Snapshot | **`omni-moderation-2024-09-26`** (pinned, not the `*-latest` alias) |

`omni-moderation-latest` resolves to this same snapshot as of 2026-05, but
the alias may be silently updated by OpenAI in the future. We pin the dated
snapshot to keep scores reproducible.

## File layout

```
moderation/
├── en/{train,dev,test}.json
├── vi/{train,dev,test}.json
└── ko/{train,dev,test}.json
```

Each file is a JSON array of records, ordered to match the corresponding
dataset file row-for-row.

## Schema

```jsonc
{
  "id": "lexnorm2015_1212",           // == {source}_{index} of the matching dataset row
  "moderation": {
    "raw":  { "flagged": false, "top_category": "harassment", "top_score": 0.0123 },
    "norm": { "flagged": false, "top_category": "harassment", "top_score": 0.0145 }
  }
}
```

- `raw` — moderation result for `sent_raw` (original user-generated text).
- `norm` — moderation result for `sent_norm` (canonicalized text).
- `flagged` — boolean from the API (any category over its threshold).
- `top_category` — argmax over the 13 canonical moderation categories
  (`harassment`, `harassment/threatening`, `hate`, `hate/threatening`,
  `illicit`, `illicit/violent`, `self-harm`, `self-harm/intent`,
  `self-harm/instructions`, `sexual`, `sexual/minors`, `violence`,
  `violence/graphic`). Snake_case aliases returned by the SDK are skipped
  so the same category is not double-counted.
- `top_score` — score for `top_category`, rounded to 4 decimals.

## How these were produced

For each sentence, `text` (raw) and `canonical` (normalized) were submitted
to the OpenAI moderation API as a 2-element batch. The full per-sentence
API responses were cached as JSONL; the compact sidecars in this directory
are the argmax-over-canonical-categories projection of those responses,
aligned to the dataset row-by-row by sentence ID.

## Notes on coverage

The API input is the sentence text only (`text`, `canonical`); no
token-level annotations are sent. Mentions, URLs, and punctuation appear
in `text` / `canonical` as written.
