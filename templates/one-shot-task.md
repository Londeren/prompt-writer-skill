# Template: One-shot task

Used for Type C - a one-time task with a linear process. A single execution trajectory, a short prompt. No character is created.

Key feature: **the imperative is admissible**. "Do X", "Write Y", "Extract Z". Minimal structure, the focus is on the specifics of the task.

## When to use

- Summarize an article / document / transcript
- Translate a text
- Write a specific email / post / message
- Classify a list or a data set
- Extract specific information from a text
- Generate one unit of content by a concrete spec

## When NOT to use

- If the prompt will be used many times with different inputs → Type D (extraction)
- If the assistant has to interact with the user → Type A (character)
- If it needs to imitate a specific person → Type B (identification)
- If there are several response trajectories → Type D or A
- If the executor performs the actions itself in the system (files, commands, network) → Type E (agentic task)

## Structure

Type C is simple - markdown headings for sections are enough, an XML wrapper for large sections is overkill here (rule 11). But two exceptions always apply: wrap examples in `<example>` tags (rule 12), and if a large document is part of the input, put it at the TOP in a `<document>` tag, with the task itself at the end (rule 13, see the note after the skeleton). Phrase output constraints by number: not "briefly" but "up to 150 words" (rule 23); for a blurry boundary give a tie-breaker "when in doubt, X" (rule 24).

```
## Task

[What to do - clearly, imperatively, in the second person or with no pronoun. 
One to two sentences maximum]

Example: "Extract from the transcript a list of specific promises, with who 
promised what and by what date."

## Input

[What is fed in. A description of the format and content]

Example: "The input is the transcript of a work meeting, 30-90 minutes long. 
The transcript may contain timestamps, speaker names, filler words 
("um", "uh"), repetitions."

## What to do

A step-by-step procedure:

1. [Concrete step - what exactly to read/analyze]
2. [Next step - what to pull out/transform]
3. [Next step]
4. [Final step - how to shape the output]

[3-7 steps is optimal. If more, the task is too complex for one-shot, 
consider Type D]

## Output requirements

Format: [exact structure - markdown, JSON, plain text, a table]

Structure: [concrete blocks that must be in the output]
Example:
- Every promise is a separate item
- Item structure: "[Name] promises [what] by [when]"
- If no date is given, write "no date given"
- At the end, a "Possible promises" section for the ones with uncertainty

Constraints:
- Length: [concrete bounds or "no limit"]
- Style: [if it matters, describe it]
- Tone: [if it matters, describe it]

## Bans

The output must not contain:
- [Concrete anti-pattern 1 - e.g., "no inventing facts not in the 
  transcript"]
- [Anti-pattern 2 - e.g., "no summarizing the content of the meeting, 
  only the promises"]
- [Anti-pattern 3 - e.g., "no bullets with *, only a dash"]

[Being concrete is mandatory. "Don't write formally" is bad. "Don't use 
the phrases 'dear colleagues,' 'thank you,' 'best regards'" is good]

## Examples

<examples>
<example>
Input: [an input fragment - real or realistic]
Correct output: [a concrete example of a good output]
</example>
<example>
Input: [another fragment]
Correct output: [the matching output]
</example>
<example>
Input: [a BOUNDARY case - not obvious how the rules apply]
Correct output: [exactly how, in this case]
Explanation: [why exactly this way]
</example>
</examples>

Example of incorrect:
<example>
Input: [the same or a similar input]
Incorrect output: [an example of a bad result]
Why it is bad: [a concrete reason - what rule was broken]
</example>

## Self-check before delivery

Before returning the result, check:
- [Is the format followed?]
- [Are all the elements from "What to do" accounted for?]
- [Are there no elements from "Bans"?]
- [Are the style/tone requirements applied?]
- [Are the boundary cases handled as in the examples?]
```

## Placing the input document (for tasks over a long text)

If a one-shot task processes a large document (summarizing a transcript, extracting from an article), the order of sections changes: the document goes to the TOP, the instructions and the task go below it, and the trigger itself, "now do it", goes at the very end. A query at the end raises quality by up to 30% on long inputs.

Wrap the document in XML:
```
<document>
<source>[name or type of document]</source>
<document_content>
{{INPUT_DOCUMENT}}
</document_content>
</document>
```

For short tasks with no large input document (write an email, translate a phrase), the usual order applies, task first.

## Minimal one-shot prompt

For very simple tasks it can be trimmed down to:

```
## Task
[What to do]

## Input
[What is fed in]

## Output
[What to return and in what format]

## Bans
[What NOT to do]

## Example
<example>
Input: [...]
Output: [...]
</example>
```

If the task fits this structure and works, no need to make it longer.

## What must NOT be in a one-shot prompt

**Decision blocks by situation type.** A one-shot task is a single trajectory. If there are several trajectories, it is already Type D, not Type C.

**Master rules at the start.** For a one-time task, master rules are redundant - all the rules live in the task itself.

**A character description.** "The assistant is direct and gets to the point" is not needed. This is an executor of a specific task, not a character.

**Escalation.** A one-shot task has no fallback to a human - it is a self-contained execution.

**The draft → audit → final loop.** For a one-time linear task, three passes cost more than the task itself. A "Self-check before delivery" section is enough - concrete questions the model answers for itself before replying. The loop is needed for types B and D, where the prompt runs many times and the cost of error is replicated.

## Typical length

A simple one-shot task: 30-80 lines.  
A complex one with many rules and examples: 100-200 lines.

If it comes out longer than 250 lines, it may already be Type D (extraction) or Type A (character), worth reconsidering.
