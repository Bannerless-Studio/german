# German A1-B1 vocab pack

Static data pack for a language-agnostic vocab trainer (`key: "de"`). It has
2000 words spanning A1-B1. Each word has a short English gloss, and nouns show
their article and plural. Example sentences come with translations and, where
the licence permits, native audio. The Read tab adds 60 short reading
passages with comprehension questions (see "Reading passages" below).

**Live:** https://bannerless-studio.github.io/german/

Open the link, pick a level (or take the placement test), and start a Today
session: short rounds of flashcard-style review mixed with new words, plus a
Read tab with short passages and comprehension questions, and typing practice
for spelling. Progress (what you've seen, what's due for review) is saved in
your browser only, and can be exported/imported as a file to move between
devices. The site works offline once loaded (it registers a service worker).

**Scope note:** this app gives the vocabulary base for B1. The
Goethe-Zertifikat B1 also needs grammar, writing and speaking practice,
which this app does not teach.

**Data quality:** three QA rounds hand-checked stratified samples; the final
round had 60/60 correct primary senses and 195/196 correct word links in the
freshest sample. Every noun shows a plural line (plurals nobody uses, like
Milchen, show "rarely pl."). All but 23 words have an example showing the
word's own form. Levels are frequency bands, not CEFR: a probe of 41
hand-picked words puts every expected-A1 word in A1, but a few frequent B1
words (obwohl, Meinung) land in A2 because subtitle frequency ranks some
everyday written-register words past the cut; 39 such words (Supermarkt,
Miete, Fahrkarte, regnen, Schnee, and others) are kept by hand at B1. Rules,
counts and seeds are in `tools/REPORT.md`, and residuals are in `TODO.md`.

**Content policy:** sentences on sexual content, suicide, threats, violence,
dying or death wishes, weapons, blood, poison, corpses, drugs or abuse are
kept out of A1/A2. A word that is itself on the list, such as sterben or die
Waffe, takes B1-level examples. Sentences about rape or sexual/child abuse
are removed at every level. töten, sterben and Tod stay at their frequency
level as neutral core vocabulary; their violent sentences reach learners
only at B1 (same call as Spanish matar/morir and Russian убить). See
`TODO.md` for the exact rule history and counts.

## Reading passages (Read tab)

60 short reading texts, 20 each at A1, A2 and B1, with comprehension
questions each. A level's 20 passages unlock once you've learned 70% of that
level's words. Tapping any word in a passage shows its gloss, including
inflected forms. Comprehension questions feed missed words back into the
review queue as weak words. The passages and questions are machine-written,
checked by an automated QA pass rather than a native speaker.

Nouns are shown with their article (`das Haus`) and plural (`pl. Häuser`,
`no pl.`, `pl. only`); typing either the bare noun or the article-form
scores correctly. Typing is case-insensitive, and umlauts are lenient at
A1/A2 (`a` for `ä`, ae/oe/ue/ss spellings accepted), strict from B1 on. A
fold-only match is rejected when it would spell another pack word:
`schon`/`schön` and `zahlen`/`zählen` are each distinguished, in both
directions.

## What's in this repo

This repo holds the German data pack (`pack/`) and the data files its build
reads (`tools/`), plus [`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine)
as a git submodule at `engine/`, which holds the shared UI, drill logic and
pack builder used by every language in this trainer. See `tools/README.md`
for a file-by-file breakdown of `tools/` (including the German-specific
builder rules for separable verbs, Sie/sie, tenses and capitalisation), and
`CLAUDE.md` for the full architecture and build commands.

## Rebuild and publish (maintainers)

```
git clone --recurse-submodules <this repo>   # or: git submodule update --init
cd german && python3 -m venv .venv && source .venv/bin/activate
pip install -r tools/requirements.txt
python3 tools/build_pack.py && python3 engine/tools/jsonify_pack.py pack
./build.sh && ./check.sh
```

See `tools/README.md` for what each rebuild step reads/writes and `CLAUDE.md`
for the pinned commands, submodule-update flow and forbidden patterns.

## Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`de_full.txt`, 2018 OpenSubtitles) | CC-BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package | CC-BY-SA 4.0 | word ranking |
| Glosses, part of speech, gender, plural | [kaikki.org](https://kaikki.org) German Wiktionary extract | CC-BY-SA 3.0 / GFDL (Wiktionary) | English glosses, POS, noun gender and plural, inflection map |
| POS tagging / lemmatisation (build time only) | [spaCy](https://spacy.io) (MIT) with the `de_core_news_sm` 3.8.0 model | MIT (model) | corpus POS, lemma and sense choice; sentence word links. The pack ships no model files. |
| Example sentences | [Tatoeba](https://tatoeba.org) `deu_sentences_detailed.tsv` | CC-BY 2.0 FR | sentence text (contributor usernames in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `deu-eng_links.tsv` | CC-BY 2.0 FR | English translations |
| Sentence audio | Tatoeba `sentences_with_audio.tar.bz2` | CC BY / CC BY-SA / CC0, per clip; only permissive clips are linked | `sentences.json[].audio`; recorders per licence in `pack/attribution.json` |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

No licence is non-commercial. No graded German word list is used or shipped.
The Goethe-Institut lists are copyrighted, and Kelly has no German list.

## Level bands

Candidate (lemma, POS) pairs are ranked by a blended frequency score, the
mean of log subtitle-rank and log `wordfreq`-rank.

- **A1** (600 words): every forced item, then the highest-ranked remaining
  words. Forced items are days, months, seasons, numbers 0-20 plus the tens,
  hundert and tausend, colours, pronouns, articles, question words, core
  prepositions and conjunctions, greetings, and the A1 core list in
  `tools/forced_a1.txt`.
- **A2**: the next 700 by rank.
- **B1**: the next 700 by rank.

This is a simple, reproducible proxy for CEFR level. It is not an official
CEFR classification. See the data quality note above for a probe-word check.
