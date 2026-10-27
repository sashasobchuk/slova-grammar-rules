You build the COMPLETE default grammar catalog of a language-learning app, in ONE answer.

My NEXT message names the language ("Japanese"). Nothing else is given to you,
and you ask nothing back: everything below you decide yourself.

TARGET LANGUAGE — the language that is being learned. The whole catalog is
about its grammar, it is written in it, and all example sentences are in it.

LANGUAGE CODE — derive it yourself: the ISO 639-1 code of the TARGET
LANGUAGE (Japanese -> ja, Ukrainian -> uk, Spanish -> es). Two-letter code
if the language has one, otherwise ISO 639-3.

Return ONE JSON object and nothing else: no prose before or after it, no
markdown code fence, no comments, no trailing commas.

The answer is the WHOLE catalog at once: all groups, all tags, all rules of
all CEFR levels a1 through c2, in a single JSON object. Do NOT split it by
level, do NOT stop early, do NOT summarise, shorten descriptions, drop
examples or reduce the number of rules to save space. Every rule gets its
full lesson and all 10 examples. Never end on broken JSON.

====================== 0. LANGUAGE OF EACH FIELD ======================

The catalog describes the TARGET LANGUAGE, and it is written IN the TARGET
LANGUAGE. The learner's native language is unknown and is never mentioned.

In the TARGET LANGUAGE:
rule.title, rule.description, rule.llmDescription,
example.wrong, example.correct, example.note,
tag.description, group.description

In ASCII only, NEVER translated:
every "id", every tag "name", every group "name"

Never use English (unless the TARGET LANGUAGE is English) and never use any
third language. Never write "unlike English", "in your language" or any
comparison with another language. Typical mistakes are the ones learners of
the TARGET LANGUAGE make in general — overgeneralising a pattern, confusing
a neighbouring rule of the TARGET LANGUAGE, dropping part of the form,
wrong agreement, wrong register — never interference from one specific
native language.

====================== 1. OUTPUT SHAPE ======================

{
"lang": "<LANGUAGE CODE>",
"groups": [ { "id": "...", "name": "...", "description": "..." } ],
"tags":   [ { "id": "...", "name": "...", "description": "...",
"groupIds": ["..."] } ],
"rules":  [ { "id": "...", "title": "...", "description": "...",
"llmDescription": "...",
"examples": [ { "wrong": "...", "correct": "...",
"note": "..." } ],
"tagIds": ["..."] } ]
}

No other keys anywhere. "examples" has EXACTLY 10 entries per rule.

Because this is JSON, line breaks inside "description" are written as \n and
a blank line between blocks is \n\n. A real line break inside a string is
invalid. Escape any double quote inside a string as \".

====================== 2. IDs AND NAMES ======================

id format: <code>.rule.<slug> / <code>.tag.<slug> / <code>.group.<slug>,
where <code> is the LANGUAGE CODE you derived.

- characters allowed: [a-z0-9_.] only, slug in snake_case;
- the slug is the ENGLISH grammar term for the thing, even when the TARGET
  LANGUAGE uses another script: ja.rule.te_form_request, never a
  transliteration and never Cyrillic / kana / hanzi;
- ids are permanent: user statistics is keyed by them;
- no id repeats, no rule "title" repeats, no tag/group "name" repeats.

====================== 3. GROUPS (fixed, exactly these three) ======================

The "name" values are read by code and must stay exactly as written. Only
"description" is in the TARGET LANGUAGE.

<code>.group.level     name: "level"
description: CEFR level at which the rule is normally taught.
<code>.group.severity  name: "severity"
description: how badly breaking the rule damages comprehension.
<code>.group.area      name: "area"
description: the grammar area the rule belongs to.

====================== 4. TAGS ======================

Level tags — exactly these six, names verbatim, description in the TARGET
LANGUAGE saying what a learner at that level can do in it:
a1, a2, b1, b2, c1, c2      groupIds: [<code>.group.level]

Severity tags — exactly these three, names verbatim:
minor     the mistake is noticeable but the meaning stays clear
major     the mistake breaks the meaning, not only the form
critical  the sentence becomes hard or impossible to understand
groupIds: [<code>.group.severity]

Area tags — you invent them, 12-20 of them, for the areas the TARGET
LANGUAGE actually has. Derive them from its grammar: emit a tag for a
category only if the language has it (no "article" tag for a language
without articles), and do emit the categories it does have that other
languages lack — case, aspect, gender, animacy, classifiers, particles,
politeness level, vowel harmony, mutation, evidentiality, and so on.
name: snake_case ASCII, never the same word as its group;
description: one sentence in the TARGET LANGUAGE explaining what the tag
covers, meaningful enough for another model to pick the right tag from
it;
groupIds: [<code>.group.area]

Every area tag must be used by at least one rule.

====================== 5. RULES ======================

Coverage: 8-15 rules per CEFR level, a1 through c2 (50-90 rules in total),
ordered from the most basic to the most advanced: all a1 rules first, then
a2, b1, b2, c1, c2. Cover the grammar of the TARGET LANGUAGE systematically
and start from the rules a learner meets first. Together the rules must
cover every area tag you created.

One rule = ONE construction. "All the tenses" is not a rule; "the present
perfect with since and for" is. Never widen a title into a whole topic.
Each rule must be narrow enough to be practised with a single sentence.
Never emit two rules that name the same grammar point in different words.

--- title ---
2-6 words in the TARGET LANGUAGE, the way a grammar reference of that
language would head it. Unique across the whole catalog.

--- description (the human lesson) ---
Markdown in the TARGET LANGUAGE, 15-30 lines. This is the WHOLE lesson, not
a summary: a learner who has never met this rule must be able to use it
correctly after reading this and nothing else — no other textbook needed.
Be generous: explain, do not just list.

Write it as these blocks, each opened by a **bold** lead-in instead of a
heading, separated by \n\n:

1. **The idea** — what the rule means, in two or three plain sentences:
   the thinking behind the form (what the speaker is expressing and why
   the language needs a special form for it), not a definition copied
   from a grammar book.
2. **The form** — the formula as a "- " list, EVERY part named
   (stem, ending, auxiliary, particle, case, word order, agreement),
   followed by: the negative form, the question form, short/contracted
   forms if the rule has any, and the endings / agreement /
   conjugation pattern spelled out in words (person, number, gender,
   case, as the language requires). If the form varies by sound or
   spelling, give each variant as its own "- " line with the condition
   that selects it.
3. **When it applies** — the situations that call for the rule, one per
   line, each with the signal words, particles, adverbs or context that
   give it away.
4. **When it does NOT apply** — name explicitly the neighbouring rule of
   the TARGET LANGUAGE that learners reach for instead, and the ONE
   difference that decides between the two. Give a quick test the
   learner can run in their head ("if X, use this; if Y, use the other").
5. **Typical mistakes** — what learners get wrong here and WHY: which
   pattern they overgeneralise, which form they copy from the neighbouring
   rule, which part of the form they drop or put in the wrong place. One
   mistake per "- " line, each with its cause, and, where possible, how
   to avoid it.
6. **Register and usage** — spoken vs written, formal vs informal,
   frequency; add only when it matters for the rule.
7. **Exceptions and anything worth knowing** — irregular cases, spelling
   or sound changes, fixed expressions, common traps. Omit this block
   when the rule truly has none.

Allowed markup: **bold**, *italic*, "- " lists, `inline code`, blank lines
between blocks. NO headings (#), NO tables — the name is already in title.

Explain with words and short fragments (an ending, a form, a signal word, a
word pair), but put NO full example sentences here: whole sentences belong
to examples.

Grade the language of the description to the level of the rule: for a1-a2
use short sentences and basic vocabulary, lean on formulas and fragments;
for b1 plain explanatory prose; from b2 up normal grammatical metalanguage
is fine.

--- llmDescription ---
ONE line in the TARGET LANGUAGE, up to 30 words, no Markdown, no example
sentences. Another model reads it to decide whether a learner's sentence
falls under this rule, so it must carry the MARKERS the rule is recognised
by: key words, forms, constructions (these stay in the TARGET LANGUAGE).
No teaching talk, no "this rule teaches". Make it impossible to confuse with
a neighbouring rule: name what tells them apart.

--- examples: exactly 10 ---
wrong:   a sentence in the TARGET LANGUAGE with the typical learner
mistake for THIS rule. It must be genuinely wrong, not merely
clumsy or a matter of style.
correct: the same sentence fixed. It differs ONLY in what this rule
governs — every other word stays identical — and it is the one
natural way to fix it.
note:    one short sentence in the TARGET LANGUAGE, up to 12 words,
saying what exactly is wrong in "wrong".

All 10 must be really different:
- different persons and numbers (I, you, he/she, we, they; singular and
  plural), where the language marks them;
- different tenses, moods or aspects, where they are compatible with the
  rule;
- different vocabulary and topics (home, work, travel, food, feelings,
  study, health, technology, nature, shopping, relationships);
- different sentence types: statement, negation, question, request,
  subordinate clause;
- different registers (neutral, formal, informal), where the language has
  them;
- different typical mistakes within the rule (wrong form, missing part,
  wrong order, wrong choice between two forms), not ten copies of one
  error.
  Never 10 variants of one sentence. Sentences 4-12 words, or the equivalent
  length for a language not written with spaces. Keep the vocabulary of
  a1-a2 examples simple and the content natural from b1 up.

--- tagIds ---
At least one level tag, at least one severity tag, and one or two area
tags. Every tagId must exist in "tags". The level tag must match the level
at which the rule sits in the ordering.

====================== 6. SELF-CHECK BEFORE ANSWERING ======================

Go through this list silently before you output anything:

- the object contains "lang", all 3 groups, all 6 level tags, all 3
  severity tags, 12-20 area tags, and 50-90 rules covering a1-c2 with
  8-15 rules per level;
- every rule has exactly 10 examples and a description of 15-30 lines with
  the blocks from section 5;
- description holds no full example sentences; llmDescription holds no
  Markdown and is one line of up to 30 words;
- every tagId in rules exists in tags; every groupId in tags exists in
  groups; every area tag is used by some rule;
- no duplicate id, no duplicate rule title, no duplicate tag/group name,
  no two rules about the same grammar point;
- all ids and all tag/group names are ASCII; group names are exactly
  "level", "severity", "area"; level and severity tag names are exactly as
  specified;
- every text field is in the TARGET LANGUAGE, no English and no third
  language anywhere, no mention of any native language;
- the output is one valid JSON object, parseable as is, with \n for line
  breaks inside strings.