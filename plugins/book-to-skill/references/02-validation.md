# How to reject a candidate

Read this sheet before phase 2. Validation is run by an agent that took no part in the extraction.

Where there are no subagents, you validate your own catch and say so plainly in the phase report: the roles were combined, and a filter turned on your own extraction is weaker. In that mode run filter 1 over the whole catch first and mechanically, since a search over the source does not care who extracted the unit, and treat filters 2 and 3 as a check for holes rather than as a measured rejection.

The job of the phase is to shrink the catch. A normal result is a third to two thirds of the candidates rejected. Less than a third rejected, the filters were applied pro forma, rerun the phase.

<filter_1>

## Filter 1. Is there any ground for this unit to exist

Passes if one of these holds: the unit is confirmed by at least two independent places in the source, or the author stated it directly, in one piece.

**The anchor is checked by mechanical search over the source.** Not by "it looks plausible". This is the only filter that catches a hallucination, the other two catch uselessness.

The quote is not in the source verbatim, the unit is deleted with no discussion, and the whole catch of that extractor is re-checked on a sample.

<examples>
<example>
Unit: "open a letter with the request, not with the context". Anchor: a paragraph where the author moves the request forward in one breakdown.
Verdict: rejected. One place, and the rule was derived by the reader rather than stated by the author. The material moves to type C, where it is honest as a case.
</example>
<example>
Unit: "the subject of the key sentence is the reader". Anchor in chapter 3, a second confirmation in the breakdown in chapter 7.
Verdict: passes. Two independent places.
</example>
<example>
A unit whose anchor reads "The author writes that structure matters more than words".
Verdict: delete it and check the extractor. That is a retelling in quotation marks, not a quote. A sign that the anchor was picked to fit the formulation.
</example>
</examples>

</filter_1>

<filter_2>

## Filter 2. Does the unit answer a question the source never asked

The mechanics: invent a task from the user's domain that is not in the book. Apply the unit. It gives a concrete direction, the filter is passed. It only describes the content of the book, it is a retelling.

<examples>
<example>
"The subject of the key sentence is the reader, not the company."
Passes: on any unfamiliar text it gives a concrete action.
</example>
<example>
"The author considers context important."
Rejected: it prescribes nothing, it reports the content of the book.
</example>
<example>
"The chapter on the funnel works through a telecom operator's case."
Rejected: an index card. Exactly the material that degrades a skill into a table of contents.
</example>
</examples>

What filter 2 rejects is often not junk but the wrong type. Check whether it is a case or a term before deleting it.

</filter_2>

<filter_3>

## Filter 3. Would a specialist who never read the source state this

Passes: a non-obvious threshold, a non-obvious order, a non-obvious criterion, or a contradiction of widespread practice.

Rejected: "write clearly", "respect the reader", "prepare for the meeting", "listen to the client". Units like that are not harmless, they are worse than harmless: they take up room in the context and create a sense of substance.

<examples>
<example>
"Short does not equal clear: what you cut is the padding, not the examples and explanations."
Passes with a rewrite. The first half is banal, the second is not. The non-obvious part moves into `statement`, the banal part into `why`.
</example>
<example>
"Working on words before structure is pointless, a polished sentence gets thrown out along with its paragraph."
Passes: it sets a non-obvious order and explains the cost of breaking it.
</example>
<example>
"A text should be useful to the reader."
Rejected by filter 3. No agent is going to write a useless text because the rule is absent.
</example>
</examples>

Filter 3 is the only one where the right material is easy to cut. When in doubt, keep the unit and flag it, the evals will tell.

</filter_3>

<rejection_log>

## What to do with the rejections

Keep `rejected.md`:

```
| id | statement (short) | filter | reason |
|----|-------------------|--------|--------|
| B-014 | open with the request | 1 | one indirect place, derived by the reader |
| B-031 | respect the reader    | 3 | a commonplace |
```

The file is needed in phase 4. A failed eval more often means the right rule was cut by filter 3 than that it was never extracted.

</rejection_log>

<dedup>

## How to merge duplicates

The sign of a duplicate: two units give one and the same direction in different words. Keep the one with the stronger anchor and the more exact author's name, and fold the rest in as confirmations, raising `confirmations`.

Do not merge units that sound alike but set different conditions of use. The difference between them is usually the content of the method.

Duplicates from different tiers: keep the wording of the upper tier, and an example may be taken from the lower one with its source named. A contradiction between tiers is not merged into a compromise: the upper tier wins, and the lower tier's wording goes into rejected.md with the reason "gave way to a higher tier".

<examples>
<example>
"Give every point an example" and "An abstraction with no example does not work".
Merged: one direction, two wordings.
</example>
<example>
"Give every point an example" and "Where the reader can go wrong, add a counterexample".
Not merged: different conditions and different actions. Merging would destroy half the rule.
</example>
</examples>

</dedup>

<reporting>

## The phase report

Hand the user: N extracted, M rejected broken down by filter, K duplicates merged, L left, plus three to five rejection examples. An objection from the user costs little here, and after the skill is assembled a rebuild costs a lot.

</reporting>
