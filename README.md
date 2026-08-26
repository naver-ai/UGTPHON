# UGTPHON

Official dataset for the EMNLP 2026 paper **"Phonemizing User-Generated Text: A Benchmark, Taxonomy, and Compositional Approach"**.

MinJu Jeon, Younghan Park, Han Sung Park, Jong-Hwan Kim, Dong-Jin Kim<sup>†</sup>, Hoyeon Lee<sup>†</sup> (<sup>†</sup> corresponding authors)

A multilingual G2P benchmark providing IPA phoneme transcriptions for user-generated text (UGT) across English, Vietnamese, and Korean. Each sample is a sentence with a raw surface form, its canonical normalization, the gold IPA transcription of the canonical form, and per-token non-canonical (NC) category labels.

## Splits

|         | train | dev   | test  | Total  |
|---------|------:|------:|------:|-------:|
| **en**  | 3,329 |   600 | 1,100 |  5,029 |
| **vi**  | 8,597 | 1,129 | 1,121 | 10,847 |
| **ko**  | 1,976 |   246 |   275 |  2,497 |

## File layout

```
{lang}/                              (lang ∈ {en, vi, ko})
├── train.json
├── dev.json
├── test.json
└── LICENSE
moderation/
├── {lang}/{split}.json              per-sentence safety scores from OpenAI moderation
└── README.md
```

Each `{lang}/{split}.json` is a JSON array of sample objects, ordered to match the corresponding `moderation/{lang}/{split}.json` row-for-row by `id`.

## Schema

The schema is identical across all three languages.

| Field        | Level   | Type            | Description |
|--------------|---------|-----------------|-------------|
| `id`         | sample  | string          | `{source}_{external_id_or_index}` |
| `lang`       | sample  | string          | `en` / `vi` / `ko` |
| `source`     | sample  | string          | Source dataset (`lexnorm2015`, `vilexnorm`, `multilexnorm2026`) |
| `split`      | sample  | string          | `train` / `dev` / `test` |
| `text`       | sample  | string          | Raw sentence (possibly noisy / user-generated) |
| `canonical`  | sample  | string          | Gold normalized sentence |
| `phonemes`   | sample  | string          | IPA phonemes of `canonical`, whitespace-separated. Mentions, URLs, and punctuation are omitted |
| `tokens`     | sample  | list[dict]      | Per-token annotations |
| `text`       | token   | string          | Token's raw surface form |
| `canonical`  | token   | string          | Token's gold normalized form. Multiple acceptable normalizations are joined by `,` |
| `phoneme`    | token   | string          | IPA phoneme for the canonical form. Empty for mentions / punctuation / unphonemizable tokens. Multiple pronunciations joined by `,` |
| `is_nc`      | token   | bool            | Whether this token is non-canonical (UGT — requires normalization) |
| `nc_type`    | token   | string \| null  | UGT category when `is_nc=true`; `null` otherwise |

### `nc_type` values

| Group | ID | Category |
|-------|----|----------|
| **A — Reconstruct** | A1 | Shortening (vowel-drop) |
|                      | A2 | Shortening (extreme) |
|                      | A3 | Phrasal (unpronounceable) |
| **B — Emphasis**    | B1 | Lengthening |
|                      | B2 | Iteration |
| **C — Pass-through** | C1 | Phonetic variant |
|                      | C2 | Regular variant |
|                      | C3 | Slang / teencode |
|                      | C4 | Phrasal (pronounceable) |

## Example

```json
{
  "id": "lexnorm2015_471851969032634369",
  "lang": "en",
  "source": "lexnorm2015",
  "split": "test",
  "text": "Lebron shoulda just took that smh",
  "canonical": "lebron should have just took that shaking my head",
  "phonemes": "ˈɫɛbɹən ˈʃʊd ˈhæv dʒɪst ˈtʊk ˈðæt ˈʃeɪkɪŋ ˈmaɪ ˈhɛd",
  "tokens": [
    {"text": "Lebron",  "canonical": "lebron",            "phoneme": "ˈɫɛbɹən",            "is_nc": false, "nc_type": null},
    {"text": "shoulda", "canonical": "should have",       "phoneme": "ˈʃʊd ˈhæv",          "is_nc": true,  "nc_type": "C2"},
    {"text": "just",    "canonical": "just",              "phoneme": "dʒɪst,ˈdʒəst",       "is_nc": false, "nc_type": null},
    {"text": "took",    "canonical": "took",              "phoneme": "ˈtʊk",               "is_nc": false, "nc_type": null},
    {"text": "that",    "canonical": "that",              "phoneme": "ˈðæt,ðət",           "is_nc": false, "nc_type": null},
    {"text": "smh",     "canonical": "shaking my head",   "phoneme": "ˈʃeɪkɪŋ ˈmaɪ ˈhɛd",  "is_nc": true,  "nc_type": "A3"}
  ]
}
```

## License

Per-language licenses. See `{lang}/LICENSE` for full terms.

- **en** — no explicit license; attribution via citation
- **vi** — CC BY-NC-SA 4.0
- **ko** — Apache 2.0

For an integrated distribution, the most restrictive (vi's CC BY-NC-SA 4.0) applies.

## Citation

```bibtex
@inproceedings{jeon2026ugtphon,
  title     = {Phonemizing User-Generated Text: A Benchmark, Taxonomy, and Compositional Approach},
  author    = {Jeon, MinJu and Park, Younghan and Park, Han Sung and Kim, Jong-Hwan and Kim, Dong-Jin and Lee, Hoyeon},
  booktitle = {Proceedings of the 2026 Conference on Empirical Methods in Natural Language Processing (EMNLP)},
  year      = {2026}
}
```
