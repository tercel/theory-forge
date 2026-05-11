# Citation Parser

Shared parsing primitives used by `cite-audit`, `consistency`, and `propagate`. Defines (a) the inline-citation regex set, (b) the bibliography-entry schema, (c) the matching algorithm between the two, and (d) the WebFetch query construction for paper-existence verification.

## 1. Inline citation regex set

Academic prose uses several citation conventions. theory-forge supports all of these by default. The parser must try each pattern in order and dedupe by canonical key.

### 1.1 Author-Year (APA-like) — primary

```
(Author Year)
(Author, Year)
(Author & Co-author Year)
(Author and Co-author Year)
(Author et al. Year)
(Author et al., Year)
Author (Year)
Author and Co-author (Year)
Author et al. (Year)
```

Regex (Python flavor, no captures shown for brevity):

```python
INLINE_AUTHOR_YEAR = (
    r"\b([A-Z][A-Za-z\-'`]+(?:\s+(?:&|and)\s+[A-Z][A-Za-z\-'`]+|\s+et\s+al\.?)?)"
    r"\s*(?:\(|,\s*)?(\d{4}[a-z]?)(?:[,;\)]|\b)"
)
```

Trade-off note: this is intentionally permissive. False positives (capitalized phrases that look like author names followed by years) are filtered downstream by checking against the bibliography. Better to over-match here and dedupe than to miss valid citations.

### 1.2 Page-numbered variants

```
(Author Year: 234)
(Author Year, p. 234)
(Author Year: 234–256)
(Author Year, pp. 234–256)
(Author Year, §3.2)
```

For theory-forge purposes the page number is informational; the canonical key is `(Author, Year)`.

### 1.3 Multiple-author shorthand

```
(Author1, Author2 & Author3 1997)
(Author1, Author2 and Author3, 1997)
```

When parsing, normalize to `Author1 et al. 1997` for matching against `Author1, Author2 & Author3 (1997)` in the bibliography.

### 1.4 Numbered references (Vancouver / IEEE style)

```
[1], [2,3], [4–6]
```

When numbered references are detected, the parser looks for a numbered bibliography section instead of an alphabetic one. Not the primary mode; theory-forge defaults to Author-Year.

### 1.5 Stop-lists (anti-false-positive)

The capital-first-word regex generates false positives when academic terms or conference names happen to capitalize-and-be-followed-by-a-year. These must be filtered.

**Non-author stop-list** (drop these before lookup):

- **Conference / proceedings names**: ACL, EMNLP, NAACL, CoNLL, EACL, AACL, NeurIPS, ICLR, ICML, AAAI, IJCAI, COLING, LREC, SIGIR, KDD, WWW, ECML, UAI, EMNLP, ACL, Findings, Proceedings, Workshop, Volume, Edition, Annual
- **Generic terms**: Grammar, Study, Studies, Theory, Theories, Notes, Note, Review, Volume, Section, Figure, Table, Chapter, Part, Edition, Paper
- **CFLT / linguistics defined terms**: Core, Figure, Ground, Nucleus, Periphery, Predicate, Subject, Topic, Comment, Focus, Theme, Rheme — when these appear capitalized but **not** followed by an explicit `(Year)` parenthetical, treat as defined-term usage, not a citation
- **Author-positions of group authors**: ACL 2025 (organization-as-author) is a citation, not a false positive — distinguish by looking for `Proceedings of` / `:` / `pp.` context

A capital-first-word + nearby-year match counts as a citation **only if**:
1. The word is not in the stop-list above, AND
2. The word has ≥ 4 characters, AND
3. Either (a) it is immediately followed by `(`, OR (b) the next non-whitespace character after the year is a closing-bracket / comma / period (citation-ending punctuation), OR (c) the surrounding context contains `&`, `et al.`, `and Co-author` patterns.

### 1.6 Multi-word surnames (Dutch / Romance / Irish / Scottish prefixes)

Surnames with lowercase prefixes (`van`, `van der`, `de`, `del`, `la`, `von`, `zu`, `der`, `ó`, `Ó`, `mc`, `Mc`, `O'`, `d'`, `du`) must be matched as a unit. Example: `(van der Auwera & Plungian 1998)`.

Regex extension:

```python
PREFIXED_SURNAME = (
    r"(?:van der|van|van den|de la|de|del|la|von|zu|der|"
    r"ó|Ó|mc|Mc|O'|d'|du|le|Le)\s+"
    r"[A-Z][A-Za-z\-]+"
)
```

When prefixed-surname patterns occur, the canonical key uses the full prefixed surname lowercased and space-normalized: `van der auwera 1998`, `de la fuente 2010`.

## 2. Canonical citation key

For every parsed citation, normalize to a canonical key:

```
canonical_key = "{primary_author_surname} {year}"
```

Where:
- `primary_author_surname` = first author's surname only, lowercase, ASCII (e.g., `lambrecht`, `van valin`, `li`)
- `year` = the 4-digit year + optional disambiguating letter (e.g., `1994`, `2007a`)

Examples:
- `(Lambrecht 1994)` → `lambrecht 1994`
- `(Van Valin & LaPolla 1997)` → `van valin 1997`
- `(Liu et al. 2023)` → `liu 2023`
- `(Li & Thompson 1981)` → `li 1981`

## 3. Bibliography entry schema

A bibliography entry is a Markdown line, typically inside an unordered list, matching this shape:

```
- **Author, A. & Co-author, B.** (Year). *Title.* Publisher / Journal. [DOI/link]
```

The parser extracts:

| Field | Example | Required |
|---|---|---|
| `authors` | `Lambrecht, K.` | yes |
| `year` | `1994` | yes |
| `title` | `Information Structure and Sentence Form` | yes |
| `venue` | `Cambridge: Cambridge University Press` | yes |
| `doi` | `10.1017/CBO9780511620607` | no |
| `url` | `https://...` | no |
| `canonical_key` | `lambrecht 1994` | computed |

Bibliography is parsed by:
1. Locate the bibliography file (default: `docs/**/bibliography.md`, `references.md`, or a top-level `## References` heading).
2. Tokenize each list item (lines starting with `- ` or `* `).
3. Apply the schema regex above; entries that fail to parse are reported as **malformed** (Minor severity).

## 4. Matching algorithm

The bibliography is indexed two ways:

1. **Primary index** — first-author surname → entry
2. **Secondary index** — every author surname (first, second, third, ...) → entry

For each unique inline citation key, lookup proceeds:

1. Try primary index first
2. On miss, try secondary index (any-author match for the same year)
3. On miss in both, flag as orphan

| Result | Severity | Action |
|---|---|---|
| Exact key match (primary) | — | OK |
| Primary miss, secondary hit | **Minor** | Flag as **ambiguous-shorthand** — inline citation uses non-first-author surname. Recommend rewriting to use first author or `et al.` style. |
| No bibliography entry (both indexes miss) | **Major** | Flag as **orphan** |
| Multiple entries with same key (e.g., Levy 2007 and Levy 2007a) | Minor | Flag as **ambiguous** — disambiguating letter missing in inline citation |
| Bibliography entry with no matching inline citation | Minor | Flag as **unused** |

### 4.1 Confirmed multi-author shorthand patterns (calibration-derived)

These patterns came from real-world CFLT calibration and are worth knowing:

- `(Aslin & Newport 1996)` inline ↔ `Saffran, Aslin & Newport 1996` bibliography
- `(Tenenbaum 2018)` inline ↔ `Hartshorne, Tenenbaum & Pinker 2018` bibliography
- `(Yokoyama 2012)` inline ↔ `Hashimoto, Yokoyama & Kawashima 2012` bibliography
- `(Li 2009)` inline ↔ `Huang, Li & Li 2009` bibliography (extra hazard: same surname twice in the bibliography author list)
- `(Joshi & Weinstein 1995)` inline ↔ `Grosz, Joshi & Weinstein 1995` bibliography

All of these would be **false-positive orphans** under naive first-author-only matching. The secondary index catches them as `ambiguous-shorthand` (Minor) instead of `orphan` (Major).

## 5. WebFetch query construction (for paper-existence check)

For each bibliography entry, verify the paper exists by querying external sources in this order:

### 5.1 CrossRef (primary)

```
GET https://api.crossref.org/works?
    query.author={primary_author_surname}&
    query.bibliographic={title}&
    rows=3
```

Match criterion: top result's `author[0].family` matches `primary_author_surname` (case-insensitive) AND `issued.date-parts[0][0]` matches `year` ± 1.

### 5.2 Semantic Scholar (fallback if CrossRef returns no result)

```
GET https://api.semanticscholar.org/graph/v1/paper/search?
    query={primary_author_surname}+{first_three_title_words}&
    limit=3&
    fields=title,authors,year
```

Same match criterion.

### 5.3 OpenAlex (second fallback)

```
GET https://api.openalex.org/works?
    search={primary_author_surname}+{first_three_title_words}&
    per_page=3
```

### 5.4 Inconclusive

If all three return no match, mark the citation as **unverifiable** (Info severity, not Major). Pre-1980 journal articles, edited-volume chapters, and dissertations are common false-positive sources here. Do not auto-flag these as fabricated.

### 5.5 Confirmed false

Only mark a citation as **fabricated** (Critical) if:
- All three sources return results AND none match author + year + title-keyword overlap
- The title keywords are highly specific (≥3 content words from the cited title)

Be conservative — false-positive "fabricated" findings are corrosive to trust in the audit.

## 6. Output schema

For each finding, emit a record:

```yaml
- citation_key: "sturt 2003"
  inline_locations:
    - file: docs/en/foundations/core-concept.md
      line: 354
      claim: "every deviation pays a processing-cost premium (Frazier 1987; Sturt 2003 on reanalysis cost)"
  bibliography_entry: "Sturt, P. (2003). 'The Time-Course of the Application of Binding Constraints in Reference Resolution.' Journal of Memory and Language 48(3), 542–562."
  paper_exists: true
  paper_topic: "binding constraints in reference resolution"
  claim_topic: "reanalysis cost"
  severity: Major
  category: mis-attribution
  rationale: >
    Paper exists and authorship is correct, but the paper is on binding constraints
    in reference resolution — not on reanalysis cost. Recommend replacing with
    Fodor & Ferreira (1998) "Reanalysis in Sentence Processing."
  fix_suggestion: >
    Replace "Sturt 2003 on reanalysis cost" with
    "Fodor & Ferreira 1998 on reanalysis cost in sentence processing".
```

The full report aggregates these records under severity headings.
