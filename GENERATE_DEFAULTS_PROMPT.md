You build the COMPLETE default grammar catalog of a language-learning app, in ONE answer.

My NEXT message names the language ("Japanese"). Nothing else is given to you,
and you ask nothing back: everything below you decide yourself.

TARGET LANGUAGE — the language that is being learned. The whole catalog is
about its grammar, it is written in it, and all example sentences are in it.

LANGUAGE CODE — derive it yourself: the ISO 639-1 code of the TARGET
LANGUAGE (Japanese -> ja, Ukrainian -> uk, Spanish -> es). Two-letter code
if the language has one, otherwise ISO 639-3. Base code only, with no
script or region suffix: Chinese (Simplified) -> zh, not zh-Hans. The file
is saved as "<LANGUAGE CODE>.json" and the app requests exactly that name.

VERSION — the catalog always carries a "version": an integer the app
compares with what a device has already seeded. A brand new catalog is
"version": 1. Regenerating or extending an existing catalog: take the
"version" of the current file and add 1, otherwise devices that already
have the older catalog skip the new rules entirely.

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
"version": <VERSION>,
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
Markdown in the TARGET LANGUAGE, 20-38 lines. This is the WHOLE lesson, not
a summary: a learner who has never met this rule must be able to use it
correctly after reading this and nothing else — no other textbook needed.
Be generous: explain, do not just list.

Write it as these blocks, each opened by a **bold** lead-in instead of a
heading, separated by \n\n. Blocks A and B always come FIRST, then blocks
1-7 exactly as they are below:

A. **Why this rule matters** — 2-4 sentences, no grammar terms. Which
   problem the rule solves and what goes wrong without it. Give a real
   everyday situation in which a person needs this construction. Never
   compare with another language (the learner's native language is unknown);
   if the TARGET LANGUAGE itself has a stricter or looser neighbouring
   pattern, you may say where this rule is stricter.
B. **In simple words** — 2-3 sentences: the rule as if explained to a
   friend. Then, in this order: the formula or scheme in `inline code`;
   exactly ONE short example sentence in the TARGET LANGUAGE (no
   translation — the only example sentence allowed in this field); one
   quick self-test as a question and answer ("Is it ...? Then ...
   Otherwise ..."), written in the TARGET LANGUAGE.

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
   to avoid it. Each line names a CONCRETE mistake with the wrong fragment
   (a form, a word pair), not a topic ("verbs that do not allow it" is
   not a mistake; name the verbs or drop the line). No two lines describe
   the same mistake. A mistake that belongs to a neighbouring rule is
   not listed here. Each mistake listed here MUST be shown by at least
   one of the 10 examples (see "examples" below).
6. **Register and usage** — spoken vs written, formal vs informal,
   frequency; add only when it matters for the rule.
7. **Exceptions and anything worth knowing** — irregular cases, spelling
   or sound changes, fixed expressions, common traps. Omit this block
   when the rule truly has none.

Formatting of the blocks is the same in every rule: a prose block is
"**Lead-in** — text" on ONE line; a block made of a list is "**Lead-in**"
alone on its line, then the "- " items from the next line. Never mix the two
styles for the same block between rules.

Allowed markup: **bold**, *italic*, "- " lists, `inline code`, blank lines
between blocks. NO headings (#), NO tables — the name is already in title.

Explain with words and short fragments (an ending, a form, a signal word, a
word pair), but put NO full example sentences here, except the single one
in block B: whole sentences belong to examples.

Quality of the text, for every rule:
- No jargon the learner has not been given. A grammar term (participle,
  clause, agent, reflexive, register, agreement ...) is allowed only from
  b2 up; below that, say it in plain words ("the third form of the verb",
  "the one who does the action"). If a term is unavoidable, explain it in
  a few words at its first use.
- Every claim must be true and uncontested. Do not state debatable things
  as fact (what "can be dropped in speech", what "sounds unnatural"). If
  usage varies, say so ("often", "in formal writing") and give the
  condition.
- Blocks never contradict each other and never repeat each other: a fact
  stated in "Register and usage" is not stated again in "The idea" or
  "Exceptions". Do not write vague filler ("often shows the speaker's
  involvement", "the strength of the pattern") — either say exactly what
  it means or leave it out.
- Every word or example set you list as a category member ("verbs of
  change", "reflexive verbs") must really belong to it. Do not list
  idioms or fixed phrases as members of a pattern unless you say they are
  fixed expressions.
- If the rule has a punctuation or placement condition (a comma, a
  position of a word), state the exact condition once and obey it in
  every example.
- If the form has parts that depend on the tense, the verb type or the
  sentence type (for example "do/did only in simple tenses", a linking
  verb versus an ordinary verb), say for which cases each part applies,
  and for which it does not.

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
a neighbouring rule: name what tells them apart. Separate the markers with
commas or semicolons, never a bare run of words. Mention only mistakes that
the 10 examples of the rule really show, and write it as plain, natural
phrasing.

--- examples: exactly 10 ---
wrong:   a sentence in the TARGET LANGUAGE with the typical learner
mistake for THIS rule. It must be genuinely wrong, not merely
clumsy or a matter of style.
correct: the same sentence fixed. It differs ONLY in what this rule
governs — every other word stays identical — and it is the one
natural way to fix it.
note:    one short sentence in the TARGET LANGUAGE, up to 12 words,
saying what exactly is wrong in "wrong". It names the SPECIFIC
error of this sentence (which word or form, and what it should be),
not a generic phrase that fits every example of the rule. It must
match the error really present: if the sentence has an extra word
or a missing word, the note says so. No two notes are the same.

Link between the lesson and the examples:
- Every mistake in the block "Typical mistakes" is shown by at least one
  example, and every example shows a mistake listed there. No example
  tests something the lesson never mentions; no listed mistake stays
  without an example.
- Every variant listed in "The form" (negative, question, short form,
  each condition-dependent variant) and every situation in "When it
  applies" is covered by at least one example, where it can go wrong.
- An example checks THIS rule only. If the mistake lies in a different
  rule (another word's form, a different tense of a helper verb, a
  regional variant that is correct somewhere), do not use it. Never put
  a form in "wrong" that the lesson itself calls acceptable.
- At most 2 of the 10 examples may have the same type of mistake, and
  their notes must then differ in what they name.
- Every sentence must be natural: a real person would say it for a real
  reason. No example built only to hit a form (an unnatural combination
  such as an event that cannot repeat described as repeated).

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

Choosing them honestly:
- Level is where common courses of the TARGET LANGUAGE teach the
  construction, not how rare or "advanced" it feels. A spoken, everyday
  or basic construction is not c1/c2 just because it is informal. Do not
  inflate: c1-c2 are for constructions that are really late (rare
  literary or very formal patterns, fine nuance).
- Severity tells how much the mistake harms understanding, in line with
  the descriptions of the three severity tags. Do not use "minor" for a
  mistake that changes the meaning, nor "critical" for a form that stays
  understandable.
- Area tags describe the grammar area of the rule itself (tense, case,
  word order, ...). Never tag a rule with an area for style, frequency
  or register ("everyday", "emphasis", "common", "informal") unless the
  rule is really about that; and never with an area the rule does not
  belong to. Only tags from "tags" are allowed, and they must not
  contradict each other (for example the highest level together with
  "minor" and "everyday" for a basic spoken pattern).

====================== 6. SELF-CHECK BEFORE ANSWERING ======================

Go through this list silently before you output anything:

- the object contains "lang" and an integer "version" (1 for a new
  catalog, current + 1 when regenerating), all 3 groups, all 6 level tags, all 3
  severity tags, 12-20 area tags, and 50-90 rules covering a1-c2 with
  8-15 rules per level;
- every rule has exactly 10 examples and a description of 20-38 lines that
  opens with blocks A and B and continues with the blocks from section 5;
- description holds no full example sentences except the single one in block B; llmDescription holds no
  Markdown and is one line of up to 30 words;
- every mistake in "Typical mistakes" is shown by an example, and every
  example shows a listed mistake; no two lines of the block repeat each
  other; no more than 2 examples share the same mistake type; every note
  names the specific error and no two notes are the same;
- every variant in "The form" is covered by at least one example; any
  punctuation or placement condition in the lesson is obeyed in every
  example, and "wrong" never contains a form the lesson calls acceptable;
- levels are not inflated and tags do not contradict each other; no area
  tag is used for style, frequency or register by mistake;
- no unexplained grammar term below b2; no vague filler; no blocks that
  repeat each other; block formatting follows the same pattern in every
  rule;
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