# Template: Extraction / Transformation prompt

Used for Type D - extract/transform tasks where the input is data/content and the output is a structured result by a specific methodology. The prompt is used many times with different inputs.

Key feature: **decision blocks following the structure of the deliverable**. Every block of the result (Summary, Bullets, Skills, and so on) is its own decision block with its own rules and examples.

## When to use

- A prompt for writing resumes by a methodology
- A prompt for extracting insights from transcripts / articles
- A prompt for generating product descriptions from a brand guide
- A prompt for grading answers by criteria
- A prompt for structured data extraction from unstructured text
- Any case where a guide/methodology exists that gets applied to different inputs

## When NOT to use

- A one-time task with no reuse → Type C
- If the result is a long back-and-forth with the user → Type A
- If it needs to imitate a specific author → Type B

## Structure

Type D is a complex, reusable prompt, it uses XML tags (rule 11). A critical feature of this type: if a large document (a transcript, an article, a data set) is part of the input, its position and wrapper strongly affect quality - see the block about the input below.

```
<role>
You are [role - e.g., "an assistant for writing resumes by the [name] 
methodology"]. You work with [type of input] and produce [type of output] in a format 
that matches the methodology.
[If applicable, for which context/audience]
</role>

[--- PROCESSING THE INPUT: see the separate block below about WHERE to put the document ---]

<master_rules>
Always apply when forming the output:
1. [Rule 1 - e.g., language format]
2. [Rule 2 - e.g., factual accuracy, no inventing]
3. [Rule 3 - e.g., hard limits]
...
</master_rules>

<methodology>
[A block on reasoning - why the methodology is built this way. A model 
that understands the purpose applies the rules more correctly in new situations]

Principle 1: [name] - [explanation, what it solves]
Principle 2: [name] - [explanation]
Principle 3: [name] - [explanation]
</methodology>

<input_analysis>
Before starting the work, determine:

What kind of input:
- [Parameter 1 - type/genre of input]
- [Parameter 2 - volume/complexity]
- [Parameter 3 - target audience of the result]

Completeness of the data:
- [What has to be in the input for quality work]
- [What to do if something is missing - flag gaps explicitly, do not invent]

Special cases:
[Typical special cases and how to handle them]
</input_analysis>

<deliverable_structure>
[Which blocks/sections are in the output, in what order]
Example for a resume: Header → Summary → Experience → Skills → Education → 
extra sections as needed. [If the structure depends on the input, give 
variants with the selection conditions]
</deliverable_structure>

<section name="block-1">
[E.g., Summary]

Rules:
- [Rule 1 - e.g., length by number: "3-4 lines", not "briefly" (rule 23)]
- [Rule 2 - required elements]
- [Rule 3 - taboos]

Formula: [if there is one - e.g., "3-4 lines, opens with the role and years 
of experience, closes with a value proposition"]

<examples>
<example>
[A concrete example of a good block]
What is notable: [why it is good]
</example>
<example>
[Another example, a different context]
What is notable: [...]
</example>
<example>
[A BOUNDARY case - the rules apply non-obviously]
Explanation: [exactly how the rules were applied]
</example>
</examples>

Examples of incorrect:
<example>
[A bad example]
Why it is bad: [a concrete reason]
</example>
<example>
[Another bad example]
Why it is bad: [reason]
</example>

Self-check for this block:
- [Question 1 specific to the block]
- [Question 2]
</section>

<section name="block-2">
[E.g., Experience bullets]

Rules: [specific to the block]
Formula: [e.g., "action verb in past simple + what was done + a metric/impact. 
Forbidden: was responsible for, worked on, helped with"]

<examples>
[3-5 varied examples of correct in example tags]
</examples>

Examples of incorrect:
[2-3 in example tags with rationale]

Self-check for this block: [questions]
</section>

[One section block per block of the result]

<global_antipatterns>
Besides the self-check of each block, before delivery check the whole 
document for the absence of:
- [Anti-pattern 1 - e.g., passive voice]
- [Anti-pattern 2 - vague action verbs]
- [Anti-pattern 3 - absence of measurable impact]
Fix it if found.
</global_antipatterns>

<output_format>
[Exactly what the final file/answer looks like]
- Format: [markdown / plain text / DOCX / other]
- Structure: [how sections are separated]
- Metadata: [if needed]
[If there is a concrete output template, attach it]
</output_format>

<draft_audit_final>
The result is formed in three passes. Only the third is delivered.

1. Draft. Fill in every block of the deliverable by the rules of its section. 
   The draft is always written, even when only the final goes out: the audit 
   needs an object to check, without it the second pass checks emptiness.

2. Audit. Answer for yourself, briefly and concretely:
   - Which methodology rule is broken, and in which block? Name the block 
     and the rule, not a general assessment.
   - Which statements of the draft are not supported by the input data? 
     Write out every fact, number, name, date, quote that is not in the input.
   - Which facts, numbers, and quotes from the input did not reach the result? 
     Write out every one missed and decide for each: dropped by a methodology 
     rule, or lost.
   - Which global anti-patterns remain in the text, and where?
   Also check: are all blocks filled in, does each block's self-check pass, 
   does the format match the requirements, are data gaps flagged explicitly.

3. Final. Rewrite so that every audit point is closed: methodology 
   violations fixed, unsupported statements removed or flagged as a data 
   gap, lost facts put back. A result where the audit found a problem 
   and the final did not close it is not delivered.
</draft_audit_final>
```

## Processing the input document - critical for quality

If the input is a large text (a transcript, an article, a data set from ~20k tokens), its position in the prompt affects quality by up to 30%. Rule: the document goes ABOVE the instructions.

Two options depending on how the prompt is used:

**Option 1 - system/user split (for the API, Claude Project, agents).** The instructions (the whole skeleton above) go into the system prompt. The document is supplied as a separate user turn. This naturally puts the document "at the top" of the content being processed. The preferred option when there is a technical way to split them.

**Option 2 - a single prompt with a placeholder (when everything is in one text).** The document goes near the top, right after a short `<role>`, ABOVE the detailed methodology and instructions. The query "now process this" goes at the very end.

Wrap the document in XML in both cases:
```
<documents>
<document index="1">
<source>[name or type of document]</source>
<document_content>
{{INPUT_DOCUMENT}}
</document_content>
</document>
</documents>
```

**Ground in quotes for long documents.** As the first step of the procedure, ask the model to first write out the relevant parts of the document (verbatim quotes), and only then work from them. This cuts through the noise of the rest of the text and raises accuracy. Example instruction for the prompt: "First write out in `<relevant_quotes>` the verbatim fragments of the document relevant to [the task]. Then work only from them."

## What must NOT be in an extraction prompt

**Decision blocks by interlocutor type.** That is a pattern for character-frame. In extraction, blocks follow the structure of the deliverable.

**Identification as a character.** Not "You are Anna, an HR expert." Just "An assistant for writing resumes." Identity is not needed here.

**Escalation to a live human.** Extraction usually works autonomously. If escalation is needed, that is already a character pattern (Type A).

## Working with the user's methodology

If the user has a guide/methodology that needs to be applied, it has to land in the prompt in the right places:

**Methodology principles** → the `<methodology>` block, as reasoning
**Concrete formulas and rules for block X** → the `<section name="X">` block, its Rules section
**Lists of allowed/forbidden constructions** → the `<section>` block they apply to
**Methodology anti-patterns** → the `<global_antipatterns>` block

Do not dump the whole methodology into one "Methodology" block - that is a topical structure. Distribute it across the `<section>` blocks where the rules apply.

## Mutually exclusive routes - a numbered ladder

When the prompt chooses one of several mutually exclusive routes (output format, methodology variant, input-processing mode), the choice is built as a numbered ladder, stopping at the first match, rather than a list of independent conditions:

```
Step 0: is [X] even needed? No - route A, stop.
Step 1: does [condition Y] hold? Yes - route B, stop.
Step 2: does [condition Z] hold? Yes - route C, stop.
Otherwise - the default route D.
```

Plus a ban on re-interpreting categories: "a match is a category match, not a style preference; do not split a category into subcategories to justify a different route." Without this ban, the model justifies its preferred route by claiming the case is "special" - the Opus 5 system prompt closes this explicitly: "'Fit' means category match, not style preference... such subdivision is a style opinion, not a category mismatch."

Why a ladder rather than a list of conditions: a list has no order of checking and no stopping rule - if two conditions fire, the model picks on its own. A ladder makes the choice deterministic: the first matching route closes the question.

## Typical length

A simple extraction (1-2 input types, 2-3 output blocks): 200-400 lines.  
A medium one (several input types, 4-6 output blocks): 400-800 lines.  
A complex one (a rich methodology, many rules, many examples): 800-1500 lines.

Length is justified in this type when every line carries a rule / example / procedure. If it comes out longer than 1500 lines, consider splitting into a main prompt plus reference files the model loads as needed.
