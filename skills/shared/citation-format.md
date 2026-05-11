# Citation Format Templates

Canonical templates for the three most common academic citation styles. Used by `cite-audit` when proposing fixes to malformed bibliography entries and by `propagate` when emitting new citation lines.

theory-forge does not enforce a single style on the user — it detects the project's prevailing style from the existing bibliography and proposes fixes in that style. If the project is inconsistent, the audit reports the inconsistency at Minor severity.

## APA (7th edition)

Format: **Author, A. A., & Author, B. B.** (YYYY). *Title of work in sentence case.* Publisher.

```
- **Lambrecht, K.** (1994). *Information structure and sentence form: Topic, focus, and the mental representations of discourse referents.* Cambridge University Press.
- **Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., & Liang, P.** (2023). Lost in the middle: How language models use long contexts. *Transactions of the Association for Computational Linguistics, 11,* 157–173. https://doi.org/10.1162/tacl_a_00638
```

## Chicago (author-date)

Format: **Author Surname, First Name, and Co-author First Name Surname.** YYYY. "Article Title." *Journal Name* Volume(Issue): Pages.

```
- **Lambrecht, Knud.** 1994. *Information Structure and Sentence Form: Topic, Focus, and the Mental Representations of Discourse Referents.* Cambridge: Cambridge University Press.
- **Liu, Nelson F., Kevin Lin, John Hewitt, Ashwin Paranjape, Michele Bevilacqua, Fabio Petroni, and Percy Liang.** 2023. "Lost in the Middle: How Language Models Use Long Contexts." *Transactions of the Association for Computational Linguistics* 11: 157–173.
```

## MLA (9th edition)

Format: **Author Surname, First Name, and Co-author First Name Surname.** *Title.* Publisher, YYYY.

```
- **Lambrecht, Knud.** *Information Structure and Sentence Form: Topic, Focus, and the Mental Representations of Discourse Referents.* Cambridge UP, 1994.
- **Liu, Nelson F., et al.** "Lost in the Middle: How Language Models Use Long Contexts." *Transactions of the Association for Computational Linguistics,* vol. 11, 2023, pp. 157–173.
```

## CFLT/Markdown house style (the format used by the CFLT project itself)

The format the CFLT bibliography uses — a pragmatic Markdown adaptation of APA:

```
- **Author, A. & Co-author, B.** (Year). *Title.* Publisher / Journal Volume(Issue), pp. DOI: [10.xxxx/yyyy](https://doi.org/10.xxxx/yyyy)
```

theory-forge defaults to this format for new entries because:
- Bolding the authors makes scanning bibliographies easier
- Italicized title is unambiguous
- DOI as live link supports verification
- Compatible with MkDocs rendering

## Detection heuristic

To detect the prevailing project style:

1. Sample 20 random bibliography entries
2. Score each against the four templates above (string-match the dominant punctuation pattern: `.` after author year vs. comma; `&` vs `and`; quoted vs. italicized title)
3. Use the highest-scoring template as the project style
4. If two templates score within 10% of each other, mark as **mixed** and report at Minor severity

## Field-level field maps

For any entry in any style, normalize to the canonical record schema in `citation-parser.md` §3 by extracting:

| Style | authors field | year field | title field | venue field |
|---|---|---|---|---|
| APA | Before `(YYYY).` | Inside `(YYYY)` | After year, sentence-case | After title period |
| Chicago | Before `YYYY.` | Standalone year | In quotes (article) or italics (book) | After title |
| MLA | Before italic-title | At end | Italicized | Before year |
| CFLT | Inside `**...**` | Inside `(Year).` | Italicized after year | After title period |

This normalization is what allows `cite-audit` to query CrossRef regardless of source style.
