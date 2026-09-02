# Reading Claude Code's Language

A guide to decoding the sentences Claude Code writes between tool calls in VS Code.

## 1. The rhythm

Almost every message follows the same three-beat loop:

1. **Intent** — "Let me X" / "Now let me Y" → what it is about to do.
2. **Tool activity** — `Ran 3 shell commands` (collapsed; click to expand and see
   the real commands). Past tense = finished. `Running 1 shell command…` with the
   ellipsis = happening right now.
3. **Finding** — a past-tense sentence about what it learned, usually with an
   em dash `—` introducing the *reason* or *evidence*.

If you read only the sentences and skip the tool blocks, you still get the whole
story. That is deliberate.

## 2. Line-by-line decoding

### "I'll finish the MCQ citations first since they are nearly traced, then build your Excel."

**Plain English:** I was already doing another job (adding source citations to the
multiple-choice questions). It is almost done. I will finish it, then start your
Excel file.

- **traced** = followed something back to its origin. "Citation tracing" = for each
  MCQ, find which source document/page it came from.
- **nearly traced** = the finding work is nearly complete.
- This is a *sequencing* sentence: it is telling you your request is queued second,
  and why.

### "MCQs sit under a numbered question with roman-numeral parts. Let me extract all seventeen."

**Plain English:** I looked at how the document is structured. Each MCQ lives
beneath a numbered question and is labelled (i), (ii), (iii). There are 17 of them.
I will now pull them out into a list.

- **sit under** = are nested beneath, structurally. Describing layout, e.g.

  ```
  Question 4
      (i)   Which of the following is an assertion about presentation?
      (ii)  The auditor's responsibility for opening balances is...
  ```
- **extract** = copy them out of the file into a working list.
- **"all seventeen"** — it has now counted them. From here on, 17 is the target
  number; watch for it to reappear as a completeness check.

### "Two of those are wrong — generic openings matched the wrong MCQ. Let me anchor on a distinctive slice and verify each match."

**Plain English:** I matched each MCQ to its source by searching for its opening
words. Two questions begin with very common wording, so the search found the wrong
question. I will instead search using a unique piece of text from each question,
and then re-check all 17 — not just the two bad ones.

- **generic openings** = first words that are not unique ("Which of the following…"
  appears in dozens of questions).
- **anchor on** = pick a fixed, reliable reference point to match against.
- **a distinctive slice** = a short unique excerpt of text. "Slice" = a substring.
  Example: instead of searching `"Which of the following"`, search
  `"impairment of goodwill acquired in the 2023 business combination"`.
- **verify each match** = re-run the check over the whole set. Note the discipline:
  when 2 of 17 are wrong, the method is suspect, so everything gets re-checked.

### "All seventeen traced and verified. Now let me write them into the chapter."

**Plain English:** All 17 are found *and* double-checked. Now I will actually edit
the chapter file to insert them.

- Two-stage completion: **traced** (found) then **verified** (proven correct).
  Claude Code separates these on purpose — finding is not the same as being right.

### "fix_layout.py deleted all seventeen — it still enforces the rule you withdrew this morning. That is my omission: I wrote the rule and left the program contradicting it."

**Plain English:** After I inserted the citations, a script in your project called
`fix_layout.py` ran and stripped every one of them out. That script contains code
applying an old policy — the policy you cancelled this morning. This is my fault:
I updated the written rule but forgot to update the code that implements it.

- **enforces the rule** = the code actively applies a policy and deletes anything
  breaking it. E.g. `fix_layout.py` contains a line like
  `lines = [l for l in lines if not l.startswith("[Source:")]`.
- **the rule you withdrew** = a policy you cancelled. "Withdraw" = take back.
- **my omission** = my mistake, specifically a mistake of *not doing* something
  (as opposed to doing something wrong). Here: not updating the script.
- **left the program contradicting it** = the code now disagrees with the documented
  rule. This is a *self-report of a bug it caused*, stated plainly and immediately —
  a good sign, not a bad one.

### "Nothing else enforces it. Let me put the seventeen citations back."

**Plain English:** I searched the rest of the codebase for any other place applying
that same old rule. There are none, so `fix_layout.py` was the only thing to fix.
Now I will redo the work that was deleted.

- Standard practice: after finding one instance of a problem, look for siblings
  before declaring it fixed.

### "Citations now survive the whole flow and the chapter passes. Now your Excel — let me survey what the records hold."

**Plain English:** I ran the entire build process end to end. The citations are
still there afterwards — nothing deletes them anymore — and the chapter's automated
checks report no errors. That job is done. Now, for your Excel: first let me look
broadly at what data your project's log/record files actually contain.

- **flow** = the pipeline; the full sequence of processing steps the project runs.
- **survive the flow** = still present after every step has run. Proof, not hope.
- **the chapter passes** = the validation checks return no failures.
- **survey what the records hold** = take a wide first look at the available data
  *before* promising the columns you asked for. It is checking whether your request
  is answerable from what exists.

### "Only 2 breaches survive in the compliance records — they keep the final verdict after repairs, so the failure history lives mostly in the validator's records. Let me look there too."

**Plain English:** The compliance files show only 2 rule violations. That number is
misleading: those files store only the *final* state of each item. If a violation
was found and then fixed, the file ends up saying "pass" and the earlier failure
vanishes. So the record of how many times things failed is not here — it is in the
validator's logs. I will read those.

- **breach** = a recorded violation of an instruction/rule.
- **verdict** = the final judgment (pass / fail) on an item.
- **keep the final verdict after repairs** = overwritten by the fixed state.
  Example: chapter 3 failed the citation rule, was repaired, and the file now reads
  `"citations": "pass"` — one real failure, invisible.
- **the failure history lives in X** = the data you want is stored in X. "Lives" is
  standard shorthand for "is stored/located".
- **This sentence is the important one for your Excel.** It is warning you that the
  obvious source would have undercounted your "How many times it failed" column.

### "The pass counts are wrong — a check that passes often says nothing at all. Let me find what the log records about which checks actually ran."

**Plain English:** I cannot count passes by counting "pass" messages, because
successful checks write nothing to the log. Silence means success. So I need to
find out which checks *ran*, and then work out passes as (ran − failed).

- A very common software convention: logs record problems, not successes. A log with
  200 lines for 5,000 checks means 200 failures, not 5,000.

### "A check that passes says nothing, so passes must be counted as 'applicable and did not complain'. Let me read each check's own scope and description from the program."

**Plain English:** Here is the counting rule I will use: for each item, if a check
*applied* to that item and produced no complaint, count it as one pass. To do that
correctly I need to know which items each check applies to, so I will read that
from the source code rather than guessing.

- **applicable** = in scope; the check was relevant to that item.
- **did not complain** = produced no error output.
- **scope** = which files/items a check covers. Example: a "roman-numeral MCQ
  numbering" check applies only to chapters containing MCQs — counting it as passing
  on all 40 chapters would inflate the numbers.
- **read ... from the program** = take the definitions from the code, which is
  authoritative, instead of from documentation that may be stale (as `fix_layout.py`
  just proved).

### "Now I have everything I need. Let me build the workbook."

**Plain English:** Data gathering is finished; I am moving from research to
producing the file.

- **workbook** = the Excel file itself (a workbook contains one or more sheets).
  Normal Excel vocabulary, not jargon.

### "Now the workbook, with a written solution for every instruction that actually failed."

**Plain English:** I am writing the file, and the "Possible solution" column will
contain real, specific advice for each instruction that genuinely failed.

- **actually failed** — the emphasis matters. It is saying the solutions column is
  grounded in real failures, rather than invented filler for rows that never failed.
  Those rows will likely show "—" or "N/A".

### "Running 1 shell command…"

In progress right now. Compare with `Ran 3 shell commands` (past tense, finished).

## 3. Glossary of recurring words

| Word/phrase | Meaning | Example |
|---|---|---|
| trace | follow back to its source | "traced each quote to its page" |
| verify | independently re-check a result | "verified each match" |
| anchor on | use as a fixed reference point for matching | "anchor on the heading text" |
| slice | a short excerpt / substring | "a distinctive slice of the question" |
| sit under / live in | is located/stored in | "the config lives in `settings.json`" |
| flow / pipeline | the full sequence of processing steps | "survives the whole flow" |
| survive | still exist after all steps have run | "the edit survives the build" |
| pass / fail | the result of an automated check | "the chapter passes" |
| verdict | the final judgment recorded for an item | "keeps the final verdict" |
| breach | a recorded rule violation | "only 2 breaches" |
| enforce | code that actively applies a rule | "fix_layout.py enforces it" |
| withdraw (a rule) | cancel a previously agreed rule | "the rule you withdrew" |
| omission | a mistake of not doing something | "that is my omission" |
| scope | which items a rule or check applies to | "each check's own scope" |
| survey | take a broad first look before committing | "survey what the records hold" |
| records / log | files storing what happened | "the validator's records" |
| workbook | the Excel file | "build the workbook" |
| green | everything passing | "drive CI to green" |
| root cause | the underlying reason, not the symptom | "root-cause the failure" |
| in scope / out of scope | inside or outside the agreed task | "that is out of scope" |

## 4. Sentence patterns you can reuse

| Pattern | What it signals |
|---|---|
| "Let me X." / "Now let me X." | Announcing the next action. Not a request for permission. |
| "I'll do A first …, then B." | Sequencing. Your request may be second in the queue. |
| "X — Y." (em dash) | Y is the *reason* or *evidence* for X. The dash carries "because". |
| "N of those are wrong." | Result of checking its own work. |
| "That is my omission / my error." | Owning a mistake. A fix always follows in the next sentence. |
| "Only N … so …" | A number is misleading, and the next clause explains why. |
| "must be counted as …" | Defining the method before applying it. Read these carefully — they determine what your numbers mean. |
| Past tense about a file | A discovery. |
| Present/future tense | A plan. |

## 5. What that session was actually doing

1. It was mid-way through a different job (citing 17 MCQs) when your Excel request
   arrived, so it finished that first.
2. Its first matching method was unreliable, so it switched to unique text and
   re-checked all 17.
3. Your build script deleted the finished work because it still enforced a
   cancelled rule; it owned that, fixed the script, checked nothing else did the
   same, and redid the work.
4. Turning to your Excel, it found the obvious data source would have *undercounted
   failures* (repaired failures are overwritten) and *miscounted passes* (successes
   write no log line).
5. So it defined a counting rule — a pass = the check applied and did not complain —
   read each check's real scope from the code, and only then started the workbook.

## 6. Useful things to say back

- "Expand that" / "show me the commands you ran" — the collapsed tool blocks are the
  ground truth; you can always open them.
- "State the counting rule in a note at the top of the sheet." — worth asking for,
  since your pass/fail numbers depend entirely on that definition.
- "Do my request first." — if you do not want your task queued behind another.
- "Why did `fix_layout.py` delete them?" — asking for the reason behind any finding
  is always fair, and answers come with file and line references.

## 7. Pack vs. Gate

Two words describing what a pipeline step *does*.

**Pack** — bundle many separate pieces into one distributable file. An *assembling*
step; it produces something and does not judge quality.

- `npm pack` → one `.tgz`; git packfiles → many objects in one `.pack`
- In this repo: each test is packed into a `.zip`, recorded in `index.json` with its
  `sha256` and `size`
- In a book-build: gathering chapters, images and metadata into the final output

**Gate** — a checkpoint deciding whether work may continue. A *judging* step; it
produces a verdict, not a file. From the physical metaphor: closed until conditions
are met.

- A "quality gate": the build fails unless coverage ≥ 80%
- In CI, a **gating** check blocks the merge; a **non-gating** check only reports
- As a verb: "gate on X" = make X a precondition. "Availability is gated" = only if
  you qualify. "Ungated" = anyone may proceed

| | Pack | Gate |
|---|---|---|
| Purpose | assemble the output | decide whether it may pass |
| Produces | a file / bundle | a verdict (pass / fail) |
| On trouble | errors — cannot build | blocks — pipeline stops |
| Position | end of the pipeline | between stages |
| Question | "what goes in?" | "is this good enough to go on?" |

**Why it matters for pass/fail reporting:** gate instructions fail loudly and often —
that is their purpose, so a high failure count is normal and those rows carry the
useful "reason" and "solution" text. Pack instructions rarely fail countably; they
either build or crash, so 0 failures means "never recorded as violated", not
"perfectly followed".
