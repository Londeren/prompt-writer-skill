# How to check that the skill works

Read this sheet before phase 4. The procedure is self-contained: it needs no external skills, scripts, or dependencies, and everything runs on the same agent and its subagents.

Why this deserves a phase of its own: a skill assembled by every rule can still fail to work, and without measurement that is indistinguishable from working. Worse, a broken skill looks more convincing than a working one, because its rules are beautifully phrased.

<test_set>

## How to assemble the request set

10-15 requests. Fewer than ten gives noise, more than fifteen does not repay the time on a first iteration.

The composition of the set:

| Share | Type of request | What it checks |
|---|---|---|
| ~half | real tasks from the user's practice | the main benefit |
| ~a quarter | situations the source never covered | the predictive power of the method |
| a few | borderline cases from the "when not to apply" fields | whether the skill overapplies the method |
| two or three | tasks off topic | whether the skill triggers when it should not |

A request has to carry substance. One-line tasks like "read the file" do not activate a skill no matter how good the description is, the agent just does them. Test with the thing the skill was built for.

Phrase the requests **in the user's words, not in the words of the method.** If the name of the framework sounds in every request in the set, you are checking keyword triggering rather than usefulness.

Write 3-5 assertions for every request as you write it, the ones it will be graded by. An assertion is checkable by a yes or no answer against the text of the output.

```yaml
- id: Q-03
  query: "Here is the text of a landing page, why does it not sell?"
  assertions:
    - "The answer names concrete rules of the method rather than general considerations"
    - "At least one place in the text is pointed at, with a quote and a breakdown"
    - "The author's names for constructs are given verbatim"
    - "There are no rules absent from the reference sheets"
```

</test_set>

<running>

## How to run the set

**Where the environment has subagents.** For every request, launch two independent subagents with a clean context: one with the skill, one without it as the baseline. Both get the text of the request and nothing else, nothing about the skill and nothing about this being a test. Save both outputs. That is what gives you a measurable delta.

**Where there are no subagents.** An honest baseline is out of reach: you wrote the skill and know what the result should look like, so a "run without the skill" by your own hand measures nothing. In this mode run with the skill only, one request at a time, and move on to a qualitative assessment by the user. Tell them plainly: the delta was not measured, there is only a check for holes.

Do not run all fifteen requests before you have looked at the first three. A systematic failure shows up immediately, and fixing it is cheaper before the full run.

</running>

<grader>

## The grader prompt

The grader runs separately, with a clean context, and sees only the request, the output, and the assertions. It must not know which of the outputs came from the skill.

> You are grading an answer to a task. You are given: the request, the answer, and a list of assertions.
>
> For each assertion return a verdict: passed true or false, and evidence, a verbatim quote from the answer that grounds the verdict. No quote means passed false, whatever the overall impression.
>
> Do not grade whether you liked the answer. Do not infer what the author meant. Check literally what the assertion says.
>
> Response format: JSON only, of the form `[{"assertion": "...", "passed": true, "evidence": "..."}]`

The summary for a run:

```
| request | with skill | baseline | delta |
|---------|------------|----------|-------|
| Q-01    | 4/4        | 1/4      | +75 pp|
```

</grader>

<thresholds>

## What counts as a pass

- The share of passing assertions with the skill is at least 80 percent.
- The delta over the baseline is at least 20 points.
- The baseline is under 70 percent. If the agent copes without the skill, the skill is not needed, and that is an honest result to be acknowledged rather than masked.

The thresholds are taken from someone else's practice and may turn out to be wrong for humanities domains. After the first run, discuss with the user whether to move the bar, but do not move it retroactively to fit the result you got.

</thresholds>

<criteria>

## What to look at in the output

The usual "is this a good answer" judgment is weak here. Check specifically:

1. **Was the method applied rather than general erudition.** Concrete rules are visible in the answer, preferably with their numbers.
2. **Were the author's constructs named exactly.** Swapping the author's name for a synonym is a sign that the skill does not hold its terminology.
3. **Did the boundaries fire.** On a borderline request the agent said "the method does not apply here" instead of stretching it.
4. **Are there invented rules.** The answer contains a rule that is not in the reference sheets. This is the most dangerous failure of all, it means the skill gave the agent a role instead of instructions.

Check point 4 in a separate pass: collect every reference to a rule out of the outputs and match them against the sheets by mechanical search, not from memory.

</criteria>

<trigger_tuning>

## How to tune the description for triggering

A separate task from the quality of the output. A skill can be excellent and never open.

The procedure:

1. Write 10-12 requests that should trigger it: different phrasings of one task, the ones where the method is not named included. Plus 3-4 requests where the skill must **not** open.
2. Split the set in half: you tune on one half and check on the other. Do not touch the second until the end, or you will fit the description to specific words.
3. Run the first half, recording for each request whether the skill opened. Where there are subagents, run every request three times, triggering is unstable.
4. Look at the failures and fix the description: add the missing trigger phrasings, sharpen the insistence, remove the vague words. Fix false triggering by narrowing the scope, not by deleting triggers.
5. Repeat no more than five times. Then run the held-out half. You take the version of the description that does better on the held-out half, not on the tuning half.

The typical cause of failure: the description says what the skill does but not when to apply it, and carries none of the words the user actually phrases the task in.

</trigger_tuning>

<troubleshooting>

## How to read a failure

| Symptom | Where to look |
|---|---|
| The skill never triggered | the frontmatter description, the trigger tuning section above |
| The answers are general, the method is not visible | the core of the method is blurred, filter 3 cut too much, look at `rejected.md` |
| The answers are right but not by the method | the baseline is high, the skill may not be needed on these tasks |
| The agent stretches the method over everything | there is no sheet with the boundaries of application, extractor D did a poor job |
| Invented rules in the output | the body of SKILL.md sets a role instead of a procedure, rewrite it into imperatives with links to the sheets |
| The answers fall apart on long tasks | the core is not front-loaded, or SKILL.md outgrew 500 lines |

The first suspect is almost always `rejected.md`, not the extraction. Material is lost in validation more often than in extraction.

</troubleshooting>

<calibration>

## How to calibrate the pipeline against a reference build

If the user has a skill assembled from the source by hand, that is the cheapest and the most honest test of the pipeline.

The order: run the pipeline on the same source without looking at the manual result. Then compare point by point:

- which rules of the manual version the pipeline never found;
- which it found but rejected, and by which filter;
- what the pipeline extracted on top of the manual version, and whether that is genuinely valuable;
- whether the split into sheets matched, and whose split is more convenient for typical tasks.

Every divergence points at a specific phase. Did not find it, the problem is in phase 1. Rejected it, the problem is in phase 2. Found it and buried it in a sheet, the problem is in phase 3.

</calibration>

<cost>

## What this costs and why it cannot be skipped

The request set with its assertions is written by the user, or by you with their corrections, and it takes an hour or two per skill and is the most boring part of the work. It is also the part most often skipped. Skipping the set means giving up the only way to tell a working skill from a beautiful one, so do not propose skipping it yourself. If the user decides to skip it, say plainly that the skill ships unverified, and record that.

</cost>
