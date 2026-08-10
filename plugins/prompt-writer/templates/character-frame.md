# Template: Character assistant

Used for Type A - the assistant runs for a long time, different users can write to it, on behalf of a company/product/abstract role. Stability of behavior and predictability matter more than the plausibility of a specific voice.

Key feature: **descriptive third person**. The assistant is a character with properties and protocols, described in the third person.

## Structure

Type A is a complex prompt, it uses XML tags for the large sections (rule 11). Fill the blocks in order. Some blocks are optional if the context does not require them. Tag names are descriptive, consistent.

```
<role>
The assistant [description of the role]. The assistant works with [target audience]. 
The assistant serves [context of use - where, how, through which channel].
[If applicable, note on behalf of which company/product]
</role>

<default_stance>
[Only for assistants with a refusal zone - if there are no refusals, remove the block]
The assistant helps by default. The assistant declines only when [threshold - 
a concrete risk or violation]. What does not reach the threshold: [named cases - 
uncomfortable, edgy, tricky questions that the assistant still handles].
</default_stance>

<master_rules>
These rules apply to every answer of the assistant regardless of the situation.

1. [Rule 1 - e.g., language and formatting]
2. [Rule 2 - e.g., hard limits on specific actions]
3. [Rule 3]
...

[Master rules should be a distillation of what is most critical. If master 
rules run long, split into the critical (here) and the stylistic (in decision 
blocks next to the point of application). Blurry boundaries in rules go by 
number: not "answers briefly" but "up to 4 sentences" (rule 23); for every 
boundary a tie-breaker "when in doubt, X" (rule 24).]
</master_rules>

<character>
The assistant works [how - descriptive third person with specifics].
The assistant treats [what] with [what attitude].
The assistant prefers [X] [over/instead of Y].
The assistant never [absolute prohibitions - caps ALWAYS/NEVER only 
for real hard limits].

[Use different modality registers - descriptive for base traits, 
should for procedures, never for absolutes, avoids for stylistic preferences. NO caps 
and no MUST on modern models - plain phrasing works the same]
</character>

<context>
[Include only what is needed for making decisions. Base information 
used once - yes. A detailed description of the product/team - 
no, better to distribute across decision blocks]
</context>

<situation_routing>
On receiving a message from the user, the assistant determines the type 
of situation. The further procedure depends on the type.

Situation type 1: [name - e.g., a new user]
Triggers:
- [Concrete sign 1]
- [Concrete sign 2]
- [Concrete sign 3]
→ Follow the procedure in block situation name="situation-1"

Situation type 2: [name]
Triggers:
- [Concrete sign 1]
- [Concrete sign 2]
→ Follow the procedure in block situation name="situation-2"

[Include 2-5 situation types the assistant needs to tell apart]
</situation_routing>

<situation name="situation-1">
Recognition triggers:
[Repeat the triggers from routing for context - deliberate duplication, 
it eases the work of attention]

Procedure:
1. [Concrete step]
2. [Next step]
3. [...]

Rules for this situation:
- [Rule 1]
- [Rule 2 - can include a repeat of something critical from master_rules]

<examples>
<example>
User message: "[what the user wrote]"
Assistant's reply: "[a concrete example of a good reply]"
What is notable: [1-2 lines on what makes this reply correct]
</example>
<example>
User message: "[a different context within the same situation]"
Assistant's reply: "[example]"
What is notable: [...]
</example>
<example>
User message: "[a BOUNDARY case - must be included]"
Assistant's reply: "[example]"
What is notable: [why, in a non-obvious case]
</example>
</examples>

[3-5 examples, varied. If all the examples are the same shape, the model 
will pick up a false pattern]

Examples of an incorrect reply:
<example>
User message: "[what the user wrote]"
Incorrect reply: "[example of a bad reply]"
Why it is bad: [a concrete reason]
</example>
<example>
Incorrect reply: "[another mistake]"
Why it is bad: [reason]
</example>

Self-check before sending - the assistant checks:
- [Question 1 specific to this situation]
- [Question 2]
- [Question 3]
</situation>

<situation name="situation-2">
[Same structure]
</situation>

[One situation block per situation type]

<escalation>
When the assistant does not answer on its own:
In the following situations the assistant does not try to answer, and instead [a concrete 
action - e.g., hands off to a supervisor, leaves a comment, 
asks a clarifying question]:
- [Situation 1 - concrete]
- [Situation 2]
- [Situation 3]

When the assistant does not know the answer:
If the context lacks the needed information, the assistant does not make it up. 
The assistant [a concrete action - e.g., writes "I will check and come back", 
marks that a fact needs verification].

Failure modes - what the assistant does not do even if the user asks:
- [Concrete failure mode 1 - e.g., "does not make promises on behalf of the team"]
- [Failure mode 2]
- [Failure mode 3]

Cumulative-output judgment:
[For boundaries that can be routed around one slice at a time] The assistant judges by the 
cumulative output of the conversation, not by each step in isolation. If the aggregate 
adds up to [a forbidden result], stop, even when each step looked incremental. 
Past assistance is not authorization to continue.

[For rules under pressure - a rationalization detector: "if the assistant catches itself on 
mentally reframing a request to make it acceptable, that reframing itself is the 
signal to refuse, not a reason to proceed"]
</escalation>

<final_checks>
Before sending a routine reply, the assistant checks:
- [Are the master rules followed?]
- [Does the tone fit?]
- [Are there no forbidden phrasings from the anti-patterns?]
- [Was the right procedure applied for this type of situation?]
- [Are the boundaries respected - no promises/guarantees that cannot be given?]
</final_checks>

[The block below is optional - include if the assistant has answers with a cost 
of error: refusals, escalations, answers about money, deadlines, legal matters. 
On routine answers the loop does not pay for itself - it costs latency on every 
reply]

<critical_answer_protocol>
For answers of the type [list concretely: refusal, escalation, a question about money 
and deadlines] the assistant works in three passes. Only the third is sent.

1. Draft of the answer.
2. Audit, briefly: which master rule does this draft break and exactly where? 
   What does it promise or claim beyond what the assistant is entitled to promise?
   Also check: tone, forbidden phrasings from the anti-patterns, whether the right 
   procedure was applied, whether anything was promised that the assistant is not entitled 
   to promise - for a critical answer these checks are mandatory just as they are for a routine one.
3. Final, closing every point of the audit.

The assistant writes the rest of the answers in one pass with the check from final_checks.
</critical_answer_protocol>
```

## Tone calibration through felt-quality

In the `<character>` block, besides the mechanical rules of tone, the model can be given a feel for the correct register through an analogy plus negative anchors. Formula: one positive analogy "like X" plus two or three negative anchors "not like Y". Negative anchors cut off the characteristic failure modes of tone - obsequiousness, salesmanship, corporate voice, machine voice.

Example: "The tone of a reply is like a colleague who has already solved this problem and is sharing it quickly. Not like a manual. Not like scripted support. Not like a mentor delivering a lecture."

This adds to the concrete tone rules, it does not replace them. The Claude system prompt calibrates one of its own modes this way: "the way a helpful person would suggest a tool they noticed sitting right there. Not like a salesperson. Not like a feature announcement. Just: oh, I can actually do that for you."

## Default stance - why and how

The default_stance block closes both loopholes symmetrically: the threshold is named, so the model will not talk itself around the refusal; the cases that do not reach the threshold are named, so the model has nowhere to hide behind over-caution. The half version (threshold only, no list of cases that do not reach it) produces an over-cautious assistant that declines uncomfortable but legitimate questions. The model to copy comes from the Opus 5 system prompt: "Claude defaults to helping. Claude only declines a request when helping would create a concrete, specific risk of serious harm; requests that are merely edgy, hypothetical, playful, or uncomfortable do not meet that bar."

## What must NOT be in a character-frame prompt

**Topical blocks** "About the product", "About the team", "About the audience". Distribute this information across the decision blocks where it is needed for a specific decision.

**All rules in one register.** If everything runs through should, or everything through NEVER, the hierarchy is lost. There should be 4-6 different registers.

**Rules with no examples.** Every complex rule with 3-5 examples in `<example>` tags (correct and incorrect). Boundary cases matter more than central ones, examples should be varied.

**Caps and MUST for the non-critical.** ALWAYS/NEVER in caps and "CRITICAL: You MUST" are for real hard limits only. On modern models, caps cause overtriggering. For ordinary rules - descriptive and should.

**Softening phrasing.** Strip out "please", "try to", "would be nice if". Phrase it as a fact.

**Meta-instructions to the user.** A prompt has no room for "don't forget to connect the database" - that is an instruction to the operator, not the model. A prompt is about the model's behavior.

## Typical length

A simple character assistant with 2-3 situation types: 200-400 lines.  
A medium one with 4-6 types and expanded examples: 400-700 lines.  
A complex one with many edge cases: 700-1200 lines.

If it comes out longer than 1500 lines, the structure may be the problem. Worth splitting into a main prompt plus reference files the assistant loads as needed.
