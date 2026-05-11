# Language Data Registry

Reference-grammar registry for the cross-lang skill. The purpose is to **prevent hallucinated cross-linguistic examples**: any generated example must be grounded in a published reference grammar of the target language.

The registry is organized by language family, since the cross-lang skill's coverage rule is **typological-family-based**, not language-count-based. Five examples in five Indo-European languages do not satisfy the "≥3 distinct families" rule.

## Coverage rule

A document's cross-linguistic universality claim is considered **adequately covered** if its worked examples represent **≥3 typologically distinct families** from the registry below. Adequate examples must include the full surface form, gloss, and (optionally) IPA transcription.

## Family-language registry

### Indo-European

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Germanic | English | Quirk, Greenbaum, Leech & Svartvik (1985) *A Comprehensive Grammar of the English Language*; Huddleston & Pullum (2002) *The Cambridge Grammar of the English Language* |
| Germanic | German | Eisenberg (2013) *Grundriß der deutschen Grammatik*; Zifonun et al. (1997) *Grammatik der deutschen Sprache* |
| Romance | Spanish | RAE (2009) *Nueva gramática de la lengua española*; Butt & Benjamin (2011) *A New Reference Grammar of Modern Spanish* |
| Romance | French | Grevisse & Goosse (2016) *Le bon usage* (16th ed.); Riegel, Pellat & Rioul (2009) *Grammaire méthodique du français* |
| Romance | Italian | Salvi & Renzi (2010) *Grammatica dell'italiano antico*; Maiden & Robustelli (2013) *A Reference Grammar of Modern Italian* |
| Slavic | Russian | Wade (2010) *A Comprehensive Russian Grammar*; Timberlake (2004) *A Reference Grammar of Russian* |
| Indo-Iranian | Hindi | Kachru (2006) *Hindi*; Shapiro (2003) *A Primer of Modern Standard Hindi* |
| Hellenic | Greek (Modern) | Holton, Mackridge & Philippaki-Warburton (2012) *Greek: A Comprehensive Grammar* |

### Sino-Tibetan

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Sinitic | Mandarin Chinese | Li & Thompson (1981) *Mandarin Chinese: A Functional Reference Grammar*; Huang, Li & Li (2009) *The Syntax of Chinese* |
| Sinitic | Cantonese | Matthews & Yip (2011) *Cantonese: A Comprehensive Grammar* |
| Tibeto-Burman | Tibetan (Standard) | DeLancey (2003) *Lhasa Tibetan* in Thurgood & LaPolla (eds) *The Sino-Tibetan Languages* |

### Japonic

| Language | Reference grammar(s) |
|---|---|
| Japanese | Kuno (1973) *The Structure of the Japanese Language*; Shibatani (1990) *The Languages of Japan*; Tsujimura (2014) *An Introduction to Japanese Linguistics* (3rd ed.) |

### Koreanic

| Language | Reference grammar(s) |
|---|---|
| Korean | Sohn (1999) *The Korean Language*; Yeon & Brown (2011) *Korean: A Comprehensive Grammar* |

### Afro-Asiatic

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Semitic | Arabic (MSA) | Ryding (2005) *A Reference Grammar of Modern Standard Arabic*; Badawi, Carter & Gully (2004) *Modern Written Arabic: A Comprehensive Grammar*; Mohammad (2000) *Word Order, Agreement and Pronominalization in Standard and Palestinian Arabic*; Benmamoun (2000) *The Feature Structure of Functional Categories* |
| Semitic | Hebrew (Modern) | Glinert (1989) *The Grammar of Modern Hebrew*; Coffin & Bolozky (2005) *A Reference Grammar of Modern Hebrew* |

### Niger-Congo

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Bantu | Swahili | Ashton (1944/1947) *Swahili Grammar*; Mohammed (2001) *Modern Swahili Grammar* |
| Atlantic | Wolof | Diouf (2003) *Grammaire du wolof contemporain* |
| Volta-Niger | Yoruba | Bamgbose (1990) *Fonoloji ati Girama Yoruba* |

### Austronesian

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Malayo-Polynesian | Indonesian | Sneddon, Adelaar, Djenar & Ewing (2010) *Indonesian Reference Grammar* (2nd ed.) |
| Malayo-Polynesian | Tagalog | Schachter & Otanes (1972) *Tagalog Reference Grammar* |

### Dravidian

| Language | Reference grammar(s) |
|---|---|
| Tamil | Lehmann (1989) *A Grammar of Modern Tamil*; Asher (1985) *Tamil* |
| Telugu | Krishnamurti & Gwynn (1985) *A Grammar of Modern Telugu* |

### Uralic

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Finnic | Finnish | Karlsson (1999) *Finnish: An Essential Grammar*; Hakulinen et al. (2004) *Iso suomen kielioppi* |
| Ugric | Hungarian | Kenesei, Vago & Fenyvesi (1998) *Hungarian* |

### Turkic

| Language | Reference grammar(s) |
|---|---|
| Turkish | Kornfilt (1997) *Turkish*; Göksel & Kerslake (2005) *Turkish: A Comprehensive Grammar* |

### Mongolic

| Language | Reference grammar(s) |
|---|---|
| Mongolian (Khalkha) | Janhunen (2012) *Mongolian* |

### Austroasiatic

| Branch | Language | Reference grammar(s) |
|---|---|---|
| Mon-Khmer | Vietnamese | Thompson (1965) *A Vietnamese Grammar*; Nguyễn (1997) *Vietnamese* |
| Mon-Khmer | Khmer | Haiman (2011) *Cambodian: Khmer* |

### Isolate

| Language | Reference grammar(s) |
|---|---|
| Basque | Hualde & Ortiz de Urbina (2003) *A Grammar of Basque* |

## Use by cross-lang skill

When the `cross-lang` skill generates an example (`--generate` mode), it must:

1. Pick a language from a target family not already covered in the document
2. Cite the reference grammar from this registry as authority
3. Mark generated examples as `[draft — verify with native speaker]` until reviewed
4. Never generate examples in a language without a registered reference grammar — fail loudly instead

When `cross-lang` validates existing examples, it does **not** verify the example sentence is grammatical — that requires native-speaker review. It only verifies that the example covers a new typological family relative to existing examples.

## Adding new languages

To add a language to the registry, edit this file directly. The skill loads the registry at runtime. Each entry needs:

1. Family classification (use Glottolog or Ethnologue if unsure)
2. At least one peer-reviewed reference grammar
3. Optional: known constructional features relevant to cross-linguistic claims (negation morphology, basic word order, etc.)

## Romanization standards

For generated examples in languages with non-Latin scripts:

| Language | Standard |
|---|---|
| Mandarin | Hanyu Pinyin (with tone marks for didactic use) |
| Japanese | Hepburn romanization (long vowels with macron) |
| Korean | Revised Romanization of Korean (2000) for general; McCune-Reischauer for linguistic literature |
| Arabic (MSA) | DIN 31635 transliteration |
| Russian | ISO 9 (1995) transliteration |
| Hindi | IAST for Sanskrit-derived; ISO 15919 for modern |
| Tibetan | Wylie transliteration |
| Thai | Royal Thai General System of Transcription |
| Vietnamese | already Latin (no romanization needed) |

Examples in the document should use the native script first (where applicable), followed by the romanization in italics, followed by a literal gloss in single quotes.

Format: 出かけなかった *dekakenakatta* 'go-out-NEG-PST'
