# Modality registers, a detailed reference

Six core registers of instruction strength. Each has its own effect, its own zone of application, and examples of correct and incorrect use.

Use them deliberately. The hierarchy of registers is the model's main signal about rule priority. A monochrome prompt, every rule in one register, loses that hierarchy.

## Register 1: Descriptive third person, the strongest

**Form:** "The assistant does X," "The assistant is Y," "The assistant works Z"

**Effect:** activates the model's "character description" cluster from the training data. Creates identity, not a command. The most resistant to manipulation and injection.

**Zone of application:** core character traits, base properties that hold always regardless of context. Identity level.

### Examples of correct use

```
The assistant works directly and to the point.
The assistant treats credentials as secret information.
The assistant checks facts before asserting them.
The assistant writes without em dashes.
```

### Examples of incorrect use

```
Bad: The assistant works directly when appropriate.
Why: adding "when appropriate" to a descriptive statement weakens it to should
level. If the point is identity, drop the qualifier.

Bad: The assistant should work directly.
Why: "should" shifts descriptive into the should register. Weaker at the
identity level.

Bad: Be direct and get to the point.
Why: a second-person imperative activates a different pattern (receiving a
command), not identity. Less resistant to override.
```

### When not to use

Do not use descriptive for procedures and conditional rules. "The assistant checks the context first, then answers" is a procedure: it belongs in the should register or an imperative with an explicit condition.

## Register 2: NEVER / ALWAYS, the absolute

**Form:** "The assistant NEVER does X," "The assistant ALWAYS does Y"

**Effect:** a hard rule with no exceptions. Capitals add weight. Signals that a violation is always worse than any possible benefit.

**Important for modern models (Claude 4.5+ and the Claude 5 family):** caps and "CRITICAL: You MUST" cause overtriggering on modern models: the model overshoots and becomes too rigid. The official Anthropic guide recommends replacing "CRITICAL: You MUST" with a plain "Use this when". So keep caps ALWAYS/NEVER for real hard limits only (safety, legal, reputation-critical), and even there plain register often works. A rule's force comes from its position next to the point of application and from its examples, not from caps lock. The historical reason for caps: old models undertriggered, new ones overtrigger instead. For the unbreakable 5-10% of rules, caps do not stand alone: they come as part of the full arsenal for hard limits (a numeric threshold, duplication at the end, a self-check, consequences, examples). Full composition in full-rules.md, section 2.7.

**Zone of application:** safety, legal constraints, reputation-critical actions. Use rarely, to keep the weight. If half the rules run through NEVER, the model stops telling what matters more.

### Examples of correct use

```
The assistant NEVER shares customer personal data with third parties.
The assistant ALWAYS verifies a link is valid before sending it to the user.
The assistant NEVER gives a final price without confirmation from a manager.
```

### Examples of incorrect use

```
Bad: The assistant NEVER uses em dashes.
Why: this is a stylistic rule, not safety-critical. Descriptive or avoids
fits better here: "The assistant writes without em dashes."

Bad: The assistant NEVER writes long replies.
Why: "long" is subjective, and long replies are sometimes appropriate. NEVER
closes an override that should stay open.

Bad: The assistant NEVER does X. The assistant NEVER does Y.
     The assistant NEVER does Z. The assistant NEVER does W.
Why: four NEVERs in a row devalue all four. The model cannot tell which
matters more. Pick the one or two most critical, move the rest to
should/avoids.
```

### When not to use

Do not use for stylistic preferences. Do not use when valid exceptions exist (the override should stay open). Do not use broadly: it loses weight.

## Register 3: Should, a normative recommendation

**Form:** "The assistant should do X," "The assistant is to do Y"

**Effect:** default behavior. A strong direction, but with room for the model's contextual judgment in edge cases.

**Zone of application:** correct in 90% of cases. Cases where a rare exception is justified by specific context.

### Examples of correct use

```
The assistant should search the web for questions about current events.
The assistant should check the order status before answering a shipping
question.
The assistant should offer the customer a demo call after three clarifying
questions.
```

### Examples of incorrect use

```
Bad: The assistant should NEVER use em dashes.
Why: mixing registers. Should is a recommendation, NEVER is an absolute.
Choose one: either "The assistant writes without em dashes," or "The
assistant NEVER uses em dashes."

Bad: The assistant should be helpful.
Why: "helpful" is abstract, no operational meaning. Make concrete what
helpful means in context: "The assistant should offer specific next steps at
the end of every reply."
```

### When not to use

Do not use for identity (descriptive is stronger there). Do not use for safety-critical rules (NEVER is for those). Do not use for permissions (can is for those).

## Register 4: Can, a permission

**Form:** "The assistant can do X," "The assistant can discuss Y"

**Effect:** lifts hypothetical constraints. Protection against the model's over-caution. An explicit permission where the model might otherwise invent a prohibition on its own.

**Zone of application:** where there's a risk the model will refuse when it shouldn't. Sensitive topics that are appropriate to discuss in this context.

### Examples of correct use

```
The assistant can discuss complex clinical cases frankly. The audience here
is licensed physicians.

The assistant can use strong language in code comments. This is an internal
tool for the engineering team.

The assistant can give direct investment recommendations. The users here
are financial advisors, not retail clients.

The assistant can keep an em dash when quoting a user's own text verbatim.
```

### Examples of incorrect use

```
Bad: The assistant can work directly.
Why: "directly" is identity, not a permission. Descriptive fits here: "The
assistant works directly."

Bad: The assistant can answer user questions.
Why: that's its core function, not a permission. A permission is needed only
where a default constraint is being lifted.
```

### When not to use

Do not use when there is no default constraint to lift. If the default behavior already fits, descriptive or should is more appropriate.

## Register 5: Avoids, a behavioral norm

**Form:** "The assistant avoids X," "The assistant tries not to do Y"

**Effect:** a soft prohibition with context-dependent exceptions. Weaker than NEVER, but more explicit than a plain should.

**Zone of application:** stylistic preferences, patterns broken only on an explicit user request.

### Examples of correct use

```
The assistant avoids emojis (unless the user uses them first).
The assistant avoids long preambles and gets straight to the point.
The assistant avoids technical jargon with non-technical audiences.
```

### Examples of incorrect use

```
Bad: The assistant avoids sharing personal data.
Why: this is safety-critical: it should be NEVER. Avoids is too soft.

Bad: The assistant avoids em dashes.
Why: for an absolute stylistic rule, descriptive works better: "The
assistant writes without em dashes." Avoids leaves room for a violation
that isn't needed here.
```

### When not to use

Do not use for absolute rules (descriptive or NEVER is for those). Do not use when the override needs to stay explicitly open: that calls for a default + override structure with a specific condition.

## Register 6: Prefers, a priority

**Form:** "The assistant prefers X over Y," "The assistant more often uses X"

**Effect:** ranks alternatives. Both forms are valid, but one is preferred.

**Zone of application:** several valid approaches exist, and the prompt needs to point to the preferred one without banning the rest.

### Examples of correct use

```
The assistant prefers short replies over long ones, unless the context
needs detail.
The assistant prefers original sources over aggregators.
The assistant prefers asking the user directly over guessing their intent.
```

### Examples of incorrect use

```
Bad: The assistant prefers not to use em dashes.
Why: this is either a rule (descriptive/avoids) or it isn't. Prefers
creates a false impression that em dashes are sometimes acceptable.

Bad: The assistant prefers to be helpful.
Why: the alternative is undefined. Prefers is for when there are two
concrete alternatives, A or B.
```

### When not to use

Do not use when the alternative is undefined. Do not use when one of the options is effectively forbidden (descriptive/avoids/NEVER is for those).

## Default + override structure

Often the correct phrasing combines registers with an explicit condition.

### Structure

```
[Default behavior in one register], unless [condition], in which case
[override].
```

### Examples

```
The assistant avoids emojis, unless the user uses them first, in which case
the assistant matches that style.

The assistant replies in English, unless the user writes in another
language, in which case the assistant switches to the user's language.

The assistant NEVER gives a final price, unless the context shows explicit
confirmation from a manager, in which case the assistant can share the
price.
```

### Why this matters

A hard rule with no override creates frustrating edge cases. An override with no rule creates drift. Default plus an explicit override condition gives predictability plus adaptability.

## Hierarchy of registers by strength

From strongest to weakest:

1. **Descriptive third person** - identity, unbreakable because it describes the essence
2. **NEVER / ALWAYS** - hard rule, unbreakable because it's an explicit absolute
3. **Avoids** - a stable norm with room for a contextual exception
4. **Should** - a recommendation with room for judgment
5. **Prefers** - a priority between alternatives
6. **Can** - a permission that lifts a constraint (does not ban the opposite)

## Register distribution in a typical prompt

In a good prompt of medium complexity (300-600 lines), the distribution runs roughly like this:

- Descriptive - 20-30% of rules (identity, core traits)
- NEVER/ALWAYS - 5-10% (safety-critical only)
- Should - 30-40% (most procedures and default behavior)
- Avoids - 10-15% (style)
- Can - 5-10% (explicit permissions)
- Prefers - 5-10% (priorities between options)

If every rule in your draft sits in one register, that's a problem. If the register gets picked at random, that's a problem too. Every register should be a deliberate choice.

## Test: is the register chosen correctly

For every important rule, ask:

1. **Is this about who the assistant is, or about what it does?**  
   About identity → descriptive. About an action → should/imperative.

2. **Are there valid exceptions?**  
   None → NEVER/ALWAYS. Rare → avoids. Contextual → should.

3. **Does something need explicit permission that the model might otherwise ban on its own?**  
   Yes → can.

4. **Are there several valid options with one preferred?**  
   Yes → prefers.

5. **Is this a default with a conditional override?**  
   Yes → the default + override structure.
