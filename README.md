# Ehsaas: Empathetic Conversational AI for Indian Youth

**Ehsaas** ('Feelings') is a research-driven project developing a **multilingual (Hinglish/English)** conversational framework tailored for the emotional support of Indian youth (ages 18–25). 

---

##  Project Overview
Most emotional support agents are English-centric and follow Western therapeutic norms. **Ehsaas** bridges this gap by focusing on:
* **Code-Mixed NLP:** Handling natural Hinglish/Roman Hindi dialogue.
* **Cultural Grounding:** Addressing specific Indian youth stressors (academic pressure , family expectations).
* **Responsible AI:** A non-clinical, empathy-first approach that prioritizes listening over diagnosis.

##  Dataset Contribution
The core of this project is a curated dataset of **5,000 conversational turns** designed for fine-tuning Large Language Models (LLMs).
* **Language Mix:** 60% Hinglish (Roman), 30% English, 10% Roman Hindi.
The core contribution is five emotional pillars:
1. **The Daily Grind (1,500 entries):** Academic stress and career confusion.
2. **Heart & Social (1,250 entries):** Relationships and loneliness.
3. **The Inner Critic (1,000 entries):** Imposter syndrome and self-doubt.
4. **Deepening & Complexity (750 entries):** Mixed emotions and venting.
5. **Safety & High Stakes (500 entries):** Crisis detection and escalation
[View the full Dataset Methodology and Distribution here](./data/DATASET_DETAILS.md)

##  Dataset Access
Click on any pillar to view the raw JSON data:

* [**pilar_1.json: The Daily Grind**](./data/pilar_1.json) – Academic stress, burnout, and career confusion.
* [**pilar_2.json: Heart & Social**](./data/pilar_2.json) – Breakup pain, loneliness, and relationship nuances.
* [**pilar_3.json: The Inner Critic**](./data/pilar_3.json) – Imposter syndrome, self-doubt, and addiction.
* [**pilar_4.json: Deepening & Complexity**](./data/pilar_4.json) – Mixed emotions, venting, and emotional resistance.
* [**pilar_5.json: Safety & High Stakes**](./data/pilar_5.json) – Panic attacks, crisis ideation, and abuse/bullying.

## Technical Roadmap 
currently executing the following research pipeline:
1. **Dataset Synthesis:** Constrained prompting using LLMs to generate culturally authentic, safe, and diverse empathetic dialogues.
2. **Model Fine-Tuning:** Performing **Supervised Fine-Tuning (SFT)** on **Gemma-3 4B** using **PEFT/LoRA** to adapt the model to the Ehsaas dataset.
3. **Dialogue Management:** Deploying via a modular architecture using **Voiceflow** as the dialogue manager to handle state tracking and human-in-the-loop evaluation.
