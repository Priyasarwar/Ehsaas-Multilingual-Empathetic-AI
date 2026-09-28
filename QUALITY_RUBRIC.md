# EHSAAS — Quality Rubric for Synthetic Conversation Dataset

## 0. Purpose

This rubric is the quality gate for the EHSAAS synthetic conversation pipeline.
It is used after generation to decide whether a candidate conversation should be:

- `KEEP` — suitable for the final dataset
- `REWRITE` — useful scenario, but assistant/user dialogue needs correction
- `DUPLICATE` — semantically too similar to another example
- `SAFETY_REVIEW` — requires human safety review before any decision
- `REMOVE` — unusable, implausible, or not worth repairing

The rubric is designed for a 3,000-conversation target produced in batches of 150 final conversations. It is intentionally more practical than a research-only annotation scheme: automatic checks handle obvious failures, semantic evaluation handles harder quality issues, and humans handle safety, borderline cases, and calibration.

---

## 1. Core Principle

A conversation is not good merely because it sounds empathetic.

A high-quality EHSAAS example should perform the **right conversational function** for the user's actual request, at the appropriate depth, in natural language, while remaining coherent, useful, culturally plausible, diverse, and safe.

The highest-level question is:

> **Did the assistant respond in the way this user needed at this point in the conversation?**

---

# 2. Evaluation Order

Use this order. Do not average away a critical failure.

### Gate 1 — Structural validity

Check:
- valid JSON
- required fields present
- valid role sequence
- turn count matches `messages`
- no empty messages
- no duplicate IDs

If failure is purely structural and easy to repair → `REWRITE`.

### Gate 2 — Safety

Check the actual dialogue for:
- self-harm/suicide risk
- abuse or assault
- immediate danger
- severe medical concern
- dangerous instructions
- unsafe normalization or romanticization

Safety failures override ordinary quality scores.

A safety-sensitive case is not automatically bad; it is `SAFETY_REVIEW` until appropriately reviewed.

### Gate 3 — Scenario and factual grounding

Check whether the assistant invents important unsupported facts, diagnoses, statistics, medical claims, legal claims, citations, organizations, or authoritative-sounding information.

Ordinary synthetic scenario details are allowed when they remain consistent with the scenario.

### Gate 4 — Conversation quality

Evaluate goal alignment, response depth, strategy, emotional understanding, usefulness, coherence, language, and cultural fit.

### Gate 5 — Diversity / redundancy

Check whether the conversation adds useful variation to the existing dataset.

Do not retain an example only because it uses different wording from an existing near-copy.

---

# 3. Scoring Scale

For dimensions scored 1–5:

**5 — Excellent**
Clear strength. No meaningful correction needed.

**4 — Good**
Works well; minor imperfections only.

**3 — Acceptable**
Usable but has a noticeable weakness; consider `REWRITE` if the weakness affects the training signal.

**2 — Weak**
Important problem. Usually `REWRITE` or `REMOVE`.

**1 — Failing**
Fundamental failure. `REMOVE` unless the scenario is unusually valuable and repairable.

Do not calculate a single overall average and use it as the final decision. Critical dimensions are gated separately.

---

# 4. Primary Quality Dimensions

## 4.1 User-Goal Alignment

**Question:** Did the assistant do what the user was actually asking for?

Score 5:
- Directly addresses the user's current goal.
- Strategy fits the request.
- No unnecessary shift into another mode.

Score 3:
- Partially addresses the goal but adds some unnecessary material.

Score 1:
- Answers a different problem.
- Gives advice when the user explicitly wanted to vent.
- Gives emotional reflection when the user asked for a straightforward answer.
- Makes a decision for the user when decision support was requested.

Examples:

`simple_question` → direct answer

`does_not_want_advice_yet` → support/space rather than immediate problem-solving

`planning_request` → actionable plan

`decision_request` → relevant trade-offs and criteria, not a verdict

**Gate:** score must normally be ≥4.

---

## 4.2 Response-Depth Match

**Question:** Is the amount of detail appropriate for the user's request and the current turn?

Use the intended labels:

- `direct`
- `brief_support`
- `moderate`
- `deep`

Score 5:
- Depth is proportionate and purposeful.

Score 3:
- Slightly too short or too long, but still useful.

Score 1:
- Major over-answering or under-answering.

### Over-answering failure

A simple request receives:
- long emotional analysis
- unsolicited coping techniques
- multiple questions
- motivational lecture
- extensive background

### Under-answering failure

A user asks for a practical plan and receives only:
- validation
- vague encouragement
- a generic question

**Gate:** no `direct` example should receive a clearly deep response unless safety or ambiguity genuinely requires it.

---

## 4.3 Emotional Understanding

**Question:** Did the assistant accurately notice and respond to the emotional content that matters?

Score 5:
- Captures the user's actual emotional state without exaggeration.
- Does not invent emotion.
- Handles mixed emotions naturally.

Score 3:
- Generally correct but somewhat generic.

Score 1:
- Misreads the emotion.
- Overstates the emotional intensity.
- Uses a stock empathy statement without engaging the actual concern.

Important:

Emotion labels are contextual metadata, not rigid response commands.

A user can feel anxious without wanting a long emotional response.

---

## 4.4 Emotion-Cause Understanding

**Question:** Did the assistant respond to why the user feels this way, not just what they feel?

Examples:

- fear because of financial uncertainty
- guilt because parents sacrificed money
- anger because of repeated unfair comparison
- loneliness because of relocation

Score 5:
- Connects response to the stated cause when relevant.

Score 1:
- Focuses on emotion only and misses the underlying situation.

Do not reward invented causes.

---

## 4.5 Response Strategy / Functional Effectiveness

**Question:** Was the chosen conversational strategy appropriate?

Possible strategies:
- direct answer
- brief validation
- emotional presence
- clarification
- exploration
- practical guidance
- step-by-step planning
- decision support
- option comparison
- communication support
- reframing
- boundary support
- follow-up
- safety escalation

Score 5:
- Strategy clearly matches the user's current need.

Score 1:
- Wrong strategy or mechanically repeated strategy.

Example:

User: "I know what I need to do; I just feel awful about it."

Bad strategy: immediately giving a five-step plan.

Better strategy: acknowledge the emotional conflict and create room for the user to decide whether they want practical help.

---

## 4.6 Practical Usefulness

Apply when practical help is requested or clearly appropriate.

Score 5:
- Advice is concrete, feasible, and tied to the user's constraints.
- Gives a manageable next step or useful framework.

Score 3:
- Some useful suggestions but generic in places.

Score 1:
- Generic motivational language.
- Advice is impossible, unrealistic, or disconnected from the user's constraints.

Avoid:
- "Just believe in yourself."
- "Stay positive."
- "Work harder."
- "Everything will work out."

---

## 4.7 Decision-Support Quality

Apply to career, education, family, relationship, financial, or other meaningful choices.

Score 5:
- Clarifies priorities, constraints, risks, benefits, reversibility, evidence, and missing information.
- Supports user agency.

Score 3:
- Gives some useful comparison but is incomplete.

Score 1:
- Tells the user what major decision they should make without sufficient basis.

Preferred pattern:

`clarify → compare → identify uncertainty → suggest low-risk way to learn more`

Not:

`hear dilemma → declare the answer`

---

## 4.8 Multi-Turn Progression

This dimension is critical for 4- and 6-message examples.

Score 5:
- Later user turns add meaningful information.
- Assistant incorporates new information.
- Conversational strategy evolves naturally.
- No redundant paraphrasing.

Score 3:
- Some progression, but one turn is weak or repetitive.

Score 1:
- Conversation is artificially extended.
- Assistant repeats the same empathy/question pattern.
- New information is ignored.

### Required progression

2 messages:
- Valid initial response.

4 messages:
- Normally at least one meaningful development.

6 messages:
- Normally at least two meaningful developments.

Useful developments:
- new constraint
- changed emotion
- user rejects suggestion
- new evidence
- new goal
- financial/family/social limitation
- clarification that changes the response

---

## 4.9 Contextual Consistency

**Question:** Does the assistant remember and respect information already given?

Score 5:
- No contradictions.
- Later response uses relevant earlier information.

Score 1:
- Ignores or contradicts key information.

Examples of failure:

Turn 1: "I don't want to tell my parents yet."

Turn 4: Assistant: "Why don't you tell your parents?"

The assistant ignored an explicit earlier preference.

---

## 4.10 Topic Relevance

Every assistant response should contribute to the actual conversation.

Score 5:
- Focuses on the user's problem.

Score 3:
- Mostly relevant with some unnecessary material.

Score 1:
- Goes into unrelated or generic content.

---

# 5. Language and Style Dimensions

## 5.1 Language Naturalness

Score separately for English, Hinglish, and Roman Hindi.

### English

Look for:
- natural contemporary phrasing
- conversational rhythm
- appropriate informality
- no unnecessary formality

### Hinglish

Look for:
- natural Hindi-English mixing
- realistic Indian conversational rhythm
- no mechanical English-to-Hinglish conversion

Bad:

"This situation tumhare liye difficult hai and you should consider discussing it with your parents."

Potentially more natural:

"Situation tricky hai, especially jab ghar walon ki expectations bhi involved ho."

Do not enforce one fixed Hinglish style.

### Roman Hindi

Look for:
- primarily Hindi in Roman script
- natural informal spelling
- no unexpected Devanagari
- no textbook-style stiffness unless the user is formal

**Gate:** language should normally score ≥4 for the target language.

---

## 5.2 Language Consistency

Check:
- user language matches assistant language
- no unexplained script switching
- no accidental language drift

Accept natural code-switching within Hinglish.

Reject random switching that makes the response unnatural.

---

## 5.3 Register Consistency

Check:
- neutral
- tu
- tum
- aap

A conversation should not randomly move:

`tu → aap`

or:

`aap → tu`

without a conversational reason.

---

## 5.4 Gender-Grammar Consistency

For Hindi/Hinglish conversations, verify gendered grammar when the example specifies:

- feminine
- masculine
- gender_neutral
- unknown

Example:

Feminine user:
"main soch rahi thi"

Natural response:
"tum kya soch rahi ho?"

Masculine user:
"main soch raha tha"

Natural response:
"tum kya soch rahe ho?"

Do not infer gender from stereotypes.

Do not force gendered language when neutral wording would be natural.

---

# 6. Tone Rubric

The intended tone is:

**warm + grounded + natural + proportionate + useful**

### Good tone

- acknowledges without dramatizing
- sounds attentive
- uses ordinary conversational language
- does not perform empathy
- does not sound like a clinical script

### Poor tone

- overly therapeutic
- overly enthusiastic
- artificially intimate
- childish
- excessively slang-heavy
- repetitive
- cold or robotic

### Stock-phrase check

Monitor repeated phrases such as:

- "I totally understand"
- "that sounds really difficult"
- "I'm always here for you"
- "everything will be okay"
- "you are not alone"
- "have you tried talking to..."

These are not universally forbidden.

The problem is repeated use as a default strategy.

---

# 7. Cultural Plausibility

Score 1–5.

Check whether the scenario and response are plausible for Indian youth without stereotyping.

Consider:
- education and competitive exams
- parents/family
- sibling dynamics
- financial constraints
- hostel/commuting/relocation
- internships and jobs
- government-job expectations
- social comparison
- language barriers
- family reputation
- family business
- parental sacrifice
- social media pressure

Do not assume:

"Indian parents always..."

"Indian students always..."

"Girls are expected to..."

"Boys should..."

Use the actual scenario rather than cultural stereotypes.

---

# 8. Grounding / Hallucination Control

## Allowed

Synthetic conversational details may be invented when they are ordinary, internally consistent, and compatible with the scenario.

Examples:
- a parent conversation
- a college assignment
- a weekend routine
- a friend interaction
- a project deadline

## Not allowed without trusted source context

- fabricated statistics
- fabricated research findings
- invented medical diagnoses
- invented medical histories
- medication claims
- legal claims stated as facts
- employment statistics
- financial statistics
- fake citations
- fake named organizations as authorities
- invented emergency numbers
- scientific claims presented as fact

A generated conversation can contain fictional facts about the fictional user. It should not pretend that an externally verifiable claim is true merely because the generator invented it.

---

# 9. Safety Rubric

Safety is a **gate**, not an average score.

## SAFE / ORDINARY DISTRESS

No indication of immediate danger.

Normal emotional support can be used.

## ELEVATED CONCERN

Distress is significant or deteriorating, but there is not enough evidence of immediate danger.

Response should be proportionate and supportive.

## SAFETY-CRITICAL

Examples may include:
- active suicidal intent or imminent self-harm risk
- immediate physical danger
- severe abuse/assault requiring urgent support
- dangerous medical emergency

These require human review.

### Safety failure examples

- romanticizing self-harm
- providing harmful instructions
- ignoring imminent danger
- treating a clear crisis as ordinary motivation
- escalating a benign situation unnecessarily

Do not use the metadata `risk_tier` as the sole safety signal.

Evaluate the actual messages.

---

# 10. Diversity / Redundancy Rubric

A dataset can contain 3,000 different IDs and still be low-diversity.

Evaluate diversity at the semantic level.

Check variation across:

- problem
- scenario
- context
- emotional cause
- user goal
- request type
- support context
- language
- register
- gender grammar
- response depth
- turn count
- conversational strategy
- education/life stage
- financial constraints
- living situation
- personality/communication style

### Duplicate types

**Exact duplicate:** same or nearly same text.

**Paraphrase duplicate:** same situation and same conversational trajectory with different wording.

**Structural duplicate:** same sequence of assistant behaviors with only minor scenario changes.

**Useful variant:** same broad problem but a materially different goal, constraint, emotional cause, or trajectory.

Only the last category should normally be retained as a meaningful variation.

Recent instruction-tuning research shows that diversity can improve robustness, especially in worst-case instruction following, so diversity should be treated as a quality dimension rather than a cosmetic property. citeturn881488search1

---

# 11. Information Density

A strong conversation should contain useful information progression rather than filler.

Ask:

- Does each turn add something?
- Does the user reveal a meaningful constraint?
- Does the assistant use that information?
- Is the conversation becoming more specific?
- Is the assistant merely paraphrasing?

Especially for 4- and 6-message conversations, reject dialogues that add turns without adding information.

This aligns with recent multi-turn selection research that evaluates whole dialogues for trajectory coverage, information progress, topic grounding, and query-answer consistency. citeturn881488search5

---

# 12. Assistant Behavior Failure Taxonomy

Use one or more labels when a candidate has problems.

### Goal / relevance

- `GOAL_MISALIGNMENT`
- `TOPIC_DRIFT`
- `WRONG_STRATEGY`

### Depth

- `OVER_ANSWERING`
- `UNDER_ANSWERING`
- `UNNECESSARY_QUESTION`

### Emotional behavior

- `GENERIC_EMPATHY`
- `EMOTION_MISREAD`
- `EMOTION_OVERSTATEMENT`
- `UNSUPPORTED_PSYCHOLOGICAL_LABEL`

### Practical reasoning

- `VAGUE_ADVICE`
- `UNREALISTIC_ADVICE`
- `MISSING_CONSTRAINT`
- `DIRECTIVE_DECISION`

### Multi-turn

- `NO_INFORMATION_PROGRESS`
- `REPEATED_RESPONSE_PATTERN`
- `IGNORED_PRIOR_CONTEXT`
- `ARTIFICIAL_EXTENSION`

### Language

- `LANGUAGE_DRIFT`
- `SCRIPT_DRIFT`
- `HINGLISH_UNNATURAL`
- `ROMAN_HINDI_UNNATURAL`
- `REGISTER_MISMATCH`
- `GENDER_GRAMMAR_ERROR`

### Diversity

- `SEMANTIC_DUPLICATE`
- `STRUCTURAL_DUPLICATE`
- `LOW_INFORMATION_DIVERSITY`

### Grounding

- `UNSUPPORTED_FACT`
- `FABRICATED_STATISTIC`
- `FABRICATED_CLAIM`
- `UNSUPPORTED_MEDICAL_CLAIM`
- `FABRICATED_CITATION`

### Safety

- `SAFETY_MISSED`
- `UNNECESSARY_ESCALATION`
- `HARMFUL_GUIDANCE`
- `ROMANTICIZED_HARM`

---

# 13. Hard Rejection / Mandatory Review Rules

A candidate should not enter the normal KEEP pool when it has any of the following:

### Mandatory `SAFETY_REVIEW`

- credible imminent self-harm/suicide risk
- active dangerous situation
- serious abuse/assault case requiring safety-sensitive handling
- dangerous medical situation
- uncertain safety interpretation

### Mandatory `REWRITE` or `REMOVE`

- major goal misalignment
- major response-depth mismatch
- incorrect language/script
- severe register mismatch
- repeated or contradictory assistant behavior
- unsupported medical/psychological claims
- fabricated factual claims presented as fact
- obvious hallucinated citations
- severe cultural implausibility
- artificial multi-turn extension

---

# 14. Recommended Automatic Checks

These should be automated before human review.

## Structural

- JSON parser
- schema validation
- required-field validation
- role sequence validation
- turn-count validation
- duplicate-ID validation

## Language

- language identification
- Devanagari detection in Roman Hindi
- script consistency
- basic language drift detection

## Register / gender

- pronoun consistency checks
- common Hindi gender-agreement patterns
- obvious `tu/tum/aap` switching

These automated checks are a first-pass filter, not the final judge.

## Repetition

Track:
- exact phrase frequency
- repeated sentence templates
- repeated openings
- repeated question endings

Use similarity detection to identify semantic duplicates.

---

# 15. Recommended Semantic Evaluation

For each conversation, evaluate at least:

```text
user_goal_alignment: 1–5
response_depth_match: 1–5
emotional_understanding: 1–5
emotion_cause_understanding: 1–5
strategy_effectiveness: 1–5
practical_usefulness: 1–5
contextual_consistency: 1–5
topic_relevance: 1–5
language_naturalness: 1–5
cultural_plausibility: 1–5
multi_turn_progression: 1–5
information_density: 1–5
```

Plus:

```text
safety_status: PASS / REVIEW / FAIL

issues: [...]

status: KEEP / REWRITE / REMOVE / DUPLICATE / SAFETY_REVIEW
```

Use a separate validation pass/model where practical; do not assume the generating model is an unbiased judge of its own outputs.

---

# 16. Human Review Policy

## B001 — Calibration

Review the full final set when feasible.

Purpose:
- establish the quality standard
- discover recurring model errors
- calibrate the rubric
- create gold examples

## Later ordinary batches

Review a sample of normal-risk conversations, with extra attention to:
- new patterns
- low-confidence evaluations
- language edge cases
- multi-turn cases
- borderline quality

## Safety-critical / borderline safety

Human review should be 100%.

The aim is not to manually read thousands of conversations. The aim is to use human attention where automated checks are weakest and to keep the quality standard calibrated.

This human-in-the-loop approach is consistent with recent work on Indian-language post-training data that combines synthetic expansion with human curation and explicitly tracks multilingual coverage, multi-turn dialogue, instruction fidelity, safety, and cultural nuance. citeturn881488search4

---

# 17. Final Decision Rule

Use this order:

```text
SAFETY CHECK
    ↓
SCHEMA CHECK
    ↓
GROUNDING CHECK
    ↓
GOAL + DEPTH CHECK
    ↓
CONVERSATION QUALITY CHECK
    ↓
LANGUAGE / REGISTER / GENDER CHECK
    ↓
MULTI-TURN PROGRESSION CHECK
    ↓
DIVERSITY CHECK
    ↓
HUMAN REVIEW WHERE REQUIRED
```

A candidate is normally `KEEP` when:

- no safety failure
- no major grounding failure
- user-goal alignment ≥ 4
- response-depth match ≥ 4
- language naturalness ≥ 4
- no major multi-turn failure
- no major contradiction
- no serious cultural issue
- not a semantic/structural duplicate

A candidate is `REWRITE` when:

- the underlying scenario is valuable
- the problem is repairable
- changing the response/dialogue can fix the training signal

A candidate is `REMOVE` when:

- the scenario itself is low-value
- the dialogue is fundamentally implausible
- it is redundant and adds little value
- repairing it would effectively require creating a new example

A candidate is `SAFETY_REVIEW` whenever safety interpretation or response is uncertain.

---

# 18. Batch-Level Quality Report

After every 150-final batch, calculate:

```text
batch_id:

candidates_generated:
final_count:

KEEP:
REWRITE:
REMOVE:
DUPLICATE:
SAFETY_REVIEW:

average_user_goal_alignment:
average_response_depth_match:
average_emotional_understanding:
average_strategy_effectiveness:
average_practical_usefulness:
average_contextual_consistency:
average_language_naturalness:
average_cultural_plausibility:
average_multi_turn_progression:

language_distribution:
turn_distribution:
response_depth_distribution:
user_goal_distribution:
problem_distribution:

most_common_failures:
- ...
- ...
- ...

repeated_phrases:
- ...
- ...

coverage_gaps:
- ...
- ...

research_result:
...

changes_for_next_batch:
- ...
- ...
```

The batch report is an input to the next Batch Specification.

Do not simply generate the next 150 without reviewing what the previous batch taught you.

---

# 19. Research Basis

This rubric is informed by several recent findings relevant to the EHSAAS design:

### Emotion + cause

ECC argues that empathy systems benefit from understanding emotions together with their underlying causes and demonstrates a scalable generation framework around that structure. citeturn881488search0

### Fine-grained problems and multidimensional evaluation

EmoCare expands emotional-support problem coverage through fine-grained problem, scenario, and profile augmentation and evaluates responses across emotional understanding, strategy effectiveness, contextual consistency, and topic relevance. These dimensions are reflected directly in this rubric. citeturn881488search6

### Quality + diversity

QDIT research finds a trade-off between quality and diversity and reports that greater diversity can improve worst-case instruction-following robustness. This is why this rubric treats semantic diversity as a first-class quality dimension rather than simply measuring different wording. citeturn881488search1

### Multi-turn dialogue

Recent multi-turn instruction-tuning work argues for dialogue-level evaluation using trajectory coverage, non-redundancy, topic grounding, information progress, and query-answer form consistency. These principles motivate the multi-turn progression and information-density sections above. citeturn881488search5

### Indian-language post-training

Pragyaan describes a human-in-the-loop pipeline for Indian-language post-training data and emphasizes multilingual coverage, cultural grounding, task diversity, multi-turn dialogue, instruction fidelity, safety alignment, and cultural nuance. These dimensions are explicitly represented in this rubric. citeturn881488search4

---

# 20. Golden Rule for EHSAAS

The best example is not the example with the most empathy, the longest answer, or the most emotional language.

The best example is the one that demonstrates:

> **the right response, for the right user goal, at the right depth, in the right language and tone, with meaningful conversational progress, useful reasoning where needed, and proportionate safety behavior.**

Quality first. Diversity second. Scale follows from a reliable pipeline.
