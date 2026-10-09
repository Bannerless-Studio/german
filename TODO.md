# TODO (v2 candidates)

Residuals from the v1 QA rounds. The rules already in place are described in
`engine/tools/packbuilder/langs/de.py` and summarised in the README.

## Levels
- Levels are frequency bands. Subtitle frequency pulls some B1 exam words
  early: obwohl, Erfahrung, Meinung, Zukunft and Entscheidung sit in A2, and
  trotzdem sits in A1. A written-register frequency list would fix this.
- Resolved (2026-09-25): the everyday words the subtitle list lacked
  (Supermarkt, Gemüse, regnen, Schnee, Jacke, Miete, Fahrkarte, Ausland,
  parken, Bewerbung, Umwelt, Führerschein and 27 more from a weather,
  clothing, shopping, transport, housing, admin and food audit) are kept via
  `keep_keys` in langs/de.py, at B1. They displace 39 film-register words
  in the B1 band (`drop_keys`: weapons, crime, military, slang, insults such
  as Schwert, Täter, erschießen, Bulle, sexy), so everyday tail words
  (Toilette, Einladung, lächeln, mitkommen, umsonst, wegwerfen, drinnen,
  vorhin) stay. Words kids' books and news use (Prinzessin, Riese, Wolf,
  Engel, Kaiser, Minister, Nation, Grab, herrschen, besiegen) are kept. The
  full list and rationale are in langs/de.py and `tools/REPORT.md`.
- Everyday words still missing, each blocked by a tagger or pool limit:
  schneien (ranked past the candidate pool, so `keep_keys` cannot reach it),
  Internet and Taxi (tagged as proper nouns), Butter (read as the verb
  buttern), Straßenbahn and E-Mail (no corpus key). Das Gehalt (salary) loses
  to der Gehalt (content), so Lohn stands in. A gender-homograph audit of the
  pack's nouns has not been done.
- Not added (cap of about 40): Mantel, Socke, Mütze, Handschuh, Kasse, Tüte, Ampel,
  Aufzug, Balkon, Kaution, Lebenslauf, Zeugnis, Praktikum, Tomate, Zwiebel,
  Salat, Saft, Nudel, Wurst, Bäckerei, Apotheke, Tablette, Fieber,
  Erkältung, Zahnarzt, Briefmarke, Paket, Rezept, Nebel, Gewitter, tanken,
  bewerben, Kreuzung, Quittung, Visum.

## Reading passages
- A native-speaker pass over the 60 texts has not been done yet; only an
  automated QA pass plus one round of manual/external QA fixes (see
  `tools/REPORT_passages.md` for the full manual notes and per-passage
  coverage/link numbers).
- Idiom parts still link separately rather than as a phrase: vor allem
  (links alles), unter der Woche (links Woche), erst einmal (links erst +
  einmal), Danke schön (links schön).
- The sentence-initial article in "Die meisten ..." (p0043 s5) has no span.
- The only out-of-pack word across all 60 passages is gleis, a station-
  announcement term (p0033), judged unavoidable there.
- The passage linker rules for German (finite-verb-next-to-subject
  disambiguation, salutation lieb vs. lieben, meisten/Meister guard, gehört
  after haben, am liebsten/lieber, als/wie clause-role rules, alle-family
  routing, zu/um preposition-vs-infinitive-marker rules, kennenlernen
  joining, capitalised-noun-as-name overrides) live in vocab-engine's
  `packbuilder/langs/de.py` passage hooks, not in this repo. The same rules
  would change 6 word-id links if turned on for the full sentence corpus
  (currently passage-only; see `tools/REPORT_passages.md`).

## Sentence links
- Genitive and dative pronoun forms used as possessive-looking words
  ("statt meiner") link the possessive mein.
- Sentence-initial words that the truecaser lowered and the small model
  tagged as nouns stay nouns. Most are right (Essen, Leben), but a few
  imperatives slip through when nothing after them marks the imperative.
- A particle whose verb Wiktionary lacks as a separable verb stays unjoined.
  Examples are "da sein" and "los sein", which are not verbs. REPORT.md
  counts them.
- A possessive the small model tags as a verb after a formal imperative
  ("beschuldigen Sie meinen Sohn": meinen linked as the verb meinen) stays
  wrong. It was 2 of 25 in the second QA round. Routing mein- forms before a
  noun to the possessive would fix it.
- inner has only "im Inneren" as an example, which shows the nominalised
  form rather than the adjective (die innere Stadt).
- Swiss spellings with ss (weisst, heiss) resolve through Wiktionary's
  alternative-spelling entries. Rare Swiss-only words are left unlinked.

## Words and glosses
- Glosses beyond the reviewed ones come straight from the Wiktionary sense
  ranking. About 180 hand overrides fix the high-frequency ones in
  `tools/gloss_overrides.json`. Lower B1 glosses can still lead with a
  secondary or overlong sense.
- Technical plurals are hidden by frequency ("rarely pl."). Plurals that
  are both rare and homographs of a common form are judged from the corpus,
  so a few borderline nouns (Häute, Sichten) show "rarely pl.".
- 23 words, mostly attributive-only adjectives (heutig, ehemalig), have no
  example sentence showing the bare form, because none exists in the corpus.
- Pronoun case forms fold into the nominative lemma (mich, mir to ich). The
  pack has no separate entries for dir, dich, ihm and so on.
- The -in feminine forms (Lehrerin) fold into the masculine noun, as in
  Wiktionary's form-of entries. They are not separate words.

## Engine (vocab-engine repo)
- Resolved on engine dfd2f6e: lenient typing now accepts ae/oe/ue/ss for
  ä/ö/ü/ß, both directions, target-side, with the plain accent-strip fold
  kept. The pack's `alt` spellings on A1/A2 words are no longer needed for
  this but are left in place.
- The id map (`tools/id_map_v1.json`) is frozen. It is keyed by the
  builder's internal lowercase lemma (`haus|noun`, `beamter|noun`), not the
  displayed words.json lemma. `.cache/derived/de_idkeys.json` holds the
  current build's keys.
- Republish 09e90bc: sentence spans (20007/20007 linked words placed); inflected forms now cloze targets.
- Republish ef44c6e: no word moves (de opts out of the gloss rule); deleted override keys bulle|noun, verhaften|verb, truppe|noun, klappe|noun, toter|noun (words dropped in 7acabec); set-counter and no-voice planner fixes

- Migration proof: rollback hash 1b2f7046261d0e8ee91adbf542076ddbcf083610; previous live md5 index 07dea3a283e0633ef18f6daeb756297e, sw 9f895038d0c7f7be949d463ecaae35e1. Storage: new fields day/sn/t/u/f/p/pm/pv/pause/read.done s,ls/today.tw on first use; boot writes nothing; previous build ef44c6e carries them (migration [port] 9/9). New md5 index 5477185f18c313f058d05bad5905c124, sw e1c5d4213287b7cf4c96022370a5bc59 (engine 7b8c173; ETA gain [0.005307, 0.003376, 0.002705], known 7.582, gates 3/3 in and out of sample).
- Live proof a5b9d64 (2026-10-08) KEEP: 12-session seed from 1b2f704 byte-equal after boot/reload/Progress open (leaving Progress adds only prog.pv); one Today session writes w/s/sets/sessions/sn/day/pm; that record boots on 1b2f704 byte-equal (boot + reload), one key vocab_de, a session runs there; back on live byte-equal; 0 console errors, 0 failed requests.
- Migration proof on engine 8745de1 (placement early stop, placed level drives reading/patterns/estimates, gender gaps, session estimates recalibrated): rollback hash 4c6353cf74bf52726353cbd0f3c3fa3894e63ff9 (engine 7b8c173). Live md5 before: index.html 5477185f18c313f058d05bad5905c124, sw.js e1c5d4213287b7cf4c96022370a5bc59. New build: index.html a22e2052ca1e0af83b2df88a1f5b650a, sw.js 445dcffda93d33666a5e325e31fc644a. Pack diff: pack.json gains gapGender, placedKnown, placedRead, placementEarlyStop, placementWhole (+ eta). Storage: boot writes nothing; 12-session seed from the previous build boots byte-equal on the new build (boot, reload, Progress; only pv added on leaving Progress), after one session boots on the previous build byte-equal with no backup keys and runs a session, and back on the new build byte-equal (scratch 13/13, KEEP).
