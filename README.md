# EHSAAS — Multilingual Conversational Support for Indian Youth

EHSAAS is a research project exploring culturally adapted, multilingual
conversational AI for providing practical and emotionally appropriate
support to Indian youth.

The project focuses on building high-quality conversational data and
evaluating how conversational AI can respond naturally across different
emotional states, user goals, languages, and everyday youth situations.

## Project Status

**Current stage:** Dataset design and synthetic data generation

**Target dataset:** ~3,000 high-quality conversations

**Languages:**
- English
- Hinglish
- Roman Hindi

**Conversation lengths:**
- 2 turns
- 4 turns
- 6 turns

The dataset and evaluation pipeline are currently under development.

---

## Motivation

Many conversational AI systems can produce fluent responses, but
fluency alone does not guarantee that a response is appropriate for the
user's actual situation.

EHSAAS focuses on several dimensions of conversational quality:

- understanding the user's actual goal
- appropriate emotional responsiveness
- controlling response depth
- practical and actionable support when appropriate
- balanced decision support
- natural multilingual communication
- preservation of language register
- gender-grammar consistency when relevant
- meaningful multi-turn progression
- cultural plausibility
- safety-aware responses
- conversational diversity

The goal is not to create a system that responds to every message with
the same style of empathy or advice.

Instead, the system should adapt its response to what the user is
actually trying to accomplish.

---

## Research Questions

The project investigates questions such as:

1. How does explicit response-depth control affect conversational quality?

2. Does modeling the user's goal separately from the conversation topic
   improve response relevance?

3. How can synthetic conversations maintain meaningful diversity rather
   than producing repeated templates?

4. How should multilingual conversational data preserve natural
   differences between English, Hinglish, and Roman Hindi?

5. How can multi-turn synthetic conversations maintain genuine
   progression rather than repeating the same emotional response?

6. How can practical support and decision support be represented without
   making the assistant overly directive?

7. How can safety constraints be incorporated into synthetic data
   generation and evaluation?

---

## Dataset Design

The planned dataset contains approximately 3,000 conversations.

Each conversation is generated using controlled scenario attributes
rather than relying only on free-form prompting.

Important dimensions include:

| Dimension | Examples |
|---|---|
| Language | English, Hinglish, Roman Hindi |
| Register | neutral, tu, tum, aap |
| Gender grammar | feminine, masculine, gender-neutral, unknown |
| Conversation length | 2, 4, 6 turns |
| User goal | information, emotional support, planning, decision support |
| Request type | question, disclosure, advice request, practical request |
| Response depth | direct, brief support, moderate, deep |
| Emotion state | frustration, anxiety, embarrassment, loneliness, uncertainty |
| Problem domain | academics, career, relationships, family, finances, etc. |
| Risk tier | ordinary, sensitive, safety-critical |
| Conversation mode | information, support, planning, decision support |

The metadata is primarily used for dataset control, analysis, filtering,
and evaluation.

---

## Multi-Turn Design

EHSAAS does not treat a multi-turn conversation as several independent
single-turn examples.

A meaningful multi-turn conversation should contain progression.

For example:

```text
Turn 1:
User describes a problem.

Turn 2:
Assistant responds to the immediate situation.

Turn 3:
User introduces new information or a constraint.

Turn 4:
Assistant adapts its response.

Turn 5:
User clarifies their actual goal.

Turn 6:
Assistant responds to the updated goal.
