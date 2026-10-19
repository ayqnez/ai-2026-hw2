# HW2 submission

**Name: Kassen Ayan**
**Student ID: s23067358 **
**Group:**
**Repository:**

## AI tool disclosure

State which AI tools you used and for what. Expected and fine; undisclosed use
is not. If you used a model to help you draft a prompt, say which prompt.

>

---
## Sublab Easy — one task, four roles

### Decisions per role

One row per enquiry. Each cell shows the returned `decision` and whether it agrees with `expected`.

| Enquiry | policy_officer | front_desk | auditor | bilingual_clerk |
|---|---|---|---|---|
| E-01 | `granted` ✓ | `granted` ✓ | `more_info` ✗ | `granted` ✓ |
| E-02 | `more_info` ✓ | `more_info` ✓ | `more_info` ✓ | `more_info` ✓ |
| E-03 | `refused` ✓ | `refused` ✓ | `refused` ✓ | `refused` ✓ |
| E-04 | `refused` ✓ | `refused` ✓ | `refused` ✓ | `refused` ✓ |
| E-05 | `granted` ✓ | `granted` ✓ | `more_info` ✗ | `granted` ✓ |
| E-06 | `granted` ✓ | `granted` ✓ | `more_info` ✗ | `granted` ✓ |
| E-07 | `granted` ✓ | `granted` ✓ | `more_info` ✗ | `granted` ✓ |
| E-08 | `not_found` ✓ | `not_found` ✓ | `not_found` ✓ | `not_found` ✓ |
| E-09 | `refused` ✓ | `refused` ✓ | `refused` ✓ | `refused` ✓ |
| E-10 | `more_info` ✓ | `more_info` ✓ | `more_info` ✓ | `more_info` ✓ |
| **agrees with `expected`** | **10/10** | **10/10** | **6/10** | **10/10** |
| **parsed** | **10/10** | **10/10** | **10/10** | **10/10** |
| **schema-valid** | **10/10** | **10/10** | **10/10** | **10/10** |

Legend: ✓ = agrees with `expected`; ✗ = does not agree with `expected`.

### Which field moved, on which enquiry, under which role

| Field | Enquiries that moved | Role(s) that moved it |
|---|---|---|
| `found` | None | No role changed this field. |
| `decision` | E-01, E-05, E-06, E-07 | `auditor` |
| `amount` | E-01, E-05, E-06, E-07 | `auditor` |
| `missing_documents` | None | No role changed this field. |

### Raw replies

**Full reply for E-01 where the auditor changed the decision from the policy officer's decision:**

```json
{
  "applicant_id": "A-201",
  "found": true,
  "decision": "more_info",
  "amount": 0,
  "missing_documents": [],
  "reason": "The record shows GPA 3.4 (>=2.67), income band 1 (allowed), and both transcript and id_card are present, satisfying the grant criteria. Awaiting second review before final decision."
}
```

**Full reply for E-07 (the Kazakh enquiry) from the bilingual clerk:**

```json
{
  "applicant_id": "A-201",
  "found": true,
  "decision": "granted",
  "amount": 250000,
  "missing_documents": [],
  "reason": "Сіздің GPA 3.4, табыс тобы 1 және барлық қажетті құжаттар (транскрипт, жеке куәлік) бар, сондықтан грант берілді."
}
```

### Written answers

**1. Which fields are role-sensitive and which are not?**

The `decision` and `amount` fields are role-sensitive. In E-01, E-05, E-06 and E-07, the auditor returns `more_info` and `0`, while the other three roles return `granted` and the appropriate grant amount.

The `found` and `missing_documents` fields are not role-sensitive in this run. They agree with the expected values for all enquiries and all four roles. All roles also return parseable JSON that passes schema validation.

**2. Which enquiries are most sensitive to the role, and why those?**

E-03 tests an applicant whose GPA is below the required minimum of 2.67. All roles correctly return `refused`.

E-04 tests an applicant whose income band is not eligible. All roles correctly return `refused`.

E-07 tests a Kazakh-language enquiry. The bilingual clerk answers in Kazakh while keeping the structured fields correct. However, the auditor changes the decision to `more_info`, showing that role instructions can affect the decision even when the underlying applicant information is unchanged.

E-10 tests a case where the applicant is missing the required `id_card` document. All roles return `more_info` and identify `id_card` as missing.

E-01, E-05, E-06 and E-07 are the strongest examples of role sensitivity in this run because the auditor changes the decision and amount for eligible applicants.

**3. Where does discretion belong — the role paragraph, or code that reads `decision` afterwards?**

The role paragraph can influence how the model interprets the task, but it should not be the final authority for an expensive decision. Code should check the applicant's data against explicit eligibility rules and determine the official decision and amount.

A downstream program can read the returned fields, such as `decision` and `amount`, and validate them. However, if the role name is not included in the output or stored separately, the downstream program cannot reliably tell which role produced the record just by looking at the decision. Even if the role is recorded, it cannot infer the reason for the model's behaviour from the role name alone.

**4. Is a role a boundary?**

A role paragraph is part of the prompt: it is a natural-language instruction that guides the model's behaviour. It is not a hard technical boundary, because the model may still return a decision that conflicts with the intended policy.

In Week 2 terms, the role paragraph is a prompt-level control, not a guarantee. If a wrong decision were expensive, I would put the eligibility rules, decision mapping, and grant amount calculation in code. I would also validate the model's JSON against the schema and reject or flag responses that conflict with the official rules. The model could explain the result or draft a user-facing response, while code would remain responsible for the final decision.

## Sublab Medium — memory you choose

### Tokens per call

| Call | A — never compressed | B — compressed at the `compress` turn |
|---|---:|---:|
| 1 | 294 | 294 |
| 2 | 394 | 381 |
| 3 | 454 | 456 |
| 4 | 522 | 525 |
| 5 | 606 | 600 |
| 6 | 698 | 689 |
| 7 | 790 | 782 |
| 8 | 871 | 839 |
| 9 | 944 | 923 |
| 10 | Skipped | 1360 |
| 11 | 991 | 627 |
| 12 | 1060 | 683 |
| **Peak** | **1060** | **1360** |
| **Total for the run** | **7624** | **8159** |

*Note: B's peak and total include the compression call itself. After compression, calls 11 and 12 use fewer prompt tokens than the corresponding calls in A.*

### Probes after the conversation

*Answers below are concise summaries of the responses.*

| Probe | Tests | A retrieved? | A answer | B retrieved? | B answer |
|---|---|---|---|---|---|
| Q-1 identity | Turn 1 | Yes | You are Daniyar Qoshan, applicant A-202. | Yes | You are Daniyar Qoshan, applicant A-202. |
| Q-2 missing document | Turn 5 | Yes | Your ID card is still missing. | Yes | Your ID card is still missing. |
| Q-3 band and amount | Turns 3–4 | Yes | Income band 2; grant amount is 150,000 tenge. | Yes | Income band 2; grant amount is 150,000 tenge. |
| Q-4 the constraint | Turn 6 | Yes | You can visit the office on Thursdays. | Yes | You can visit the office on Thursdays. |
| Q-5 the open question | Turn 7 | Yes | You asked whether an employer letter could be scanned or had to be the original. | Yes | You asked whether an employer letter could be scanned or had to be the original. |
| **Retrieved** | | **5/5** | | **5/5** | |

### The state my compression produced

```json
{
  "applicant_id": "A-202",
  "topic": "study grant eligibility and documentation",
  "facts": [
    "My name is Daniyar Qoshan",
    "applicant A-202",
    "I sent my transcript last week",
    "My income band is 2 - my family's certificate says so",
    "I could not upload my id card because the scanner at home broke",
    "I can only come to the office on Thursdays, I have lab all week otherwise",
    "my sister Aruzhan applied last year and she is on file too"
  ],
  "decisions": [
    "The grant for income band 2 is 150,000 tenge",
    "Unfortunately, without your ID card on file you don’t meet the eligibility criteria",
    "If you can provide the ID card and confirm your GPA is 2.67 or higher, we can re-evaluate your application",
    "If you bring your ID card on Thursday, the staff will add it to your file and then check the remaining criteria (GPA and transcript). If everything meets the requirements, a decision can be issued that day, but the office usually reviews all documents before finalizing."
  ],
  "constraints": [
    "I can only come to the office on Thursdays, I have lab all week otherwise"
  ],
  "open_questions": [
    "Do I qualify for the study grant?",
    "Does a scanned letter from my employer count, or does it have to be the original?"
  ],
  "language": "Kazakh, English"
}
```

### Written answers

**1. What did compression buy?**

Compression reduced prompt tokens for call 11 from 991 to 627 (about 36.7%) and for call 12 from 1060 to 683 (about 35.6%). However, the compression call itself used 1360 tokens, so the peak was higher in B than in A: 1360 versus 1060. The total for the run was 8159 tokens in B and 7624 in A, including the compression call in B. Both A and B retrieved all five probes (5/5). In the separate `--break-compress` test, Q-5 failed: the question about the employer letter from turn 7 was not retrieved correctly.

**2. Why must the state be structured rather than a paragraph?**

A structured object stores information in named fields, such as `applicant_id`, `facts`, `constraints`, and `open_questions`. This makes details easier to locate and reuse, and the JSON schema can validate required fields and data types. A paragraph may summarize the conversation naturally, but it is harder to validate automatically and may mix important facts, constraints, and unanswered questions together.

**3. What is missing from your state that you would add?**

I would add a `document_status` field to record which documents have been submitted, which are missing, and which still need verification. I would also distinguish applicant-provided claims from verified information. To keep the state within the token budget, I would remove irrelevant details such as the sister’s application and avoid duplicating the Thursday availability constraint in multiple fields. I would preserve eligibility details and unresolved questions because they affect future answers.

**4. When is compression the wrong choice?**

Compression is a poor choice when exact wording and every detail must be preserved, such as a legal statement, an audit record, or a conversation where small changes could affect a decision. A summary might omit a condition or an unanswered question that cannot reliably be reconstructed later. The program detects malformed JSON: in the `--break-compress` test, parsing failed, the state remained `null`, and the full history was kept. However, the program may not detect a valid JSON summary that silently omits an important fact.


# Sublab Hard — Stories In, CVs Out, the Best Candidate by Code

**Model:** `qwen/qwen3.8-27b`

## Part 1 — Extraction

The program reads six candidate stories and extracts structured CV records. Each record is checked for valid JSON, correct fields, evidence quotes, GPA conversion, publication count, and experience months.

The extraction prompt contains the four rules from the assignment and two additional rules. These rules help the model handle approximate experience durations and contradictions correctly.

| Story | Parsed? | Valid? | Fields returned as `null` | Main traps and results |
|---|---|---|---|---|
| `story-01` | Yes | Yes | None | Control story. GPA 3.8, two published papers, and 8 months of experience were extracted correctly. The evidence quote for publications was not fully exact because it joined two passages with ellipses. |
| `story-02` | Yes | Yes | `graduation_year`, `gpa_4_scale`, `gpa_original`, `gpa_original_scale` | No GPA was stated. The model correctly returned null instead of estimating it from the diploma. The published paper was counted, but peer-review status was null. |
| `story-03` | Yes | Yes | None | GPA 4.6/5.0 was converted correctly to 3.68/4.0. The paper under review was recorded separately and not counted. The experience evidence quote was not fully exact. |
| `story-04` | Yes | Yes | None | One paper was counted as published. One paper under review and two papers in preparation were not counted. |
| `story-05` | Yes | Yes | None | The Kazakh story was processed successfully. GPA 3.9, one published paper, and 6 months of experience were extracted. Some ambiguity notes contained mixed-language text. |
| `story-06` | Yes | Yes | `graduation_year`, `gpa_4_scale`, `gpa_original` | The contradictions in GPA (3.2 vs. 3.5) and graduation year (2024 vs. 2026) were handled by returning null. The approximate experience duration of 40 months was kept and noted. |

### The four traps

The main traps were missing GPA, GPA on another scale, unpublished papers, and contradictory information.

Without the two additional rules, problems appeared in two cases. In `story-01`, the model counted zero experience months instead of eight. In `story-06`, another model run selected graduation year 2024 instead of null and returned null for experience instead of using the stated approximate 40 months.

The schema also needed to allow null values for information that a story did not provide, such as a paper title.

### Extraction for story-06

```json
{
  "candidate_id": "story-06",
  "full_name": "Nurzhan Abilov",
  "degree": "BSc in Statistics",
  "graduation_year": null,
  "gpa_4_scale": null,
  "gpa_original": null,
  "gpa_original_scale": 4.0,
  "languages": [
    "Kazakh",
    "Russian",
    "English"
  ],
  "published_outputs": [
    {
      "title": null,
      "venue": "peer-reviewed proceedings",
      "year": null,
      "peer_reviewed": true
    }
  ],
  "published_count": 1,
  "unpublished_outputs": [
    {
      "title": null,
      "status": "not_a_publication"
    }
  ],
  "experience_periods": [
    {
      "role": "insurance analytics team",
      "start": "February 2023",
      "end": null,
      "months": 40,
      "countable": true
    }
  ],
  "experience_months_countable": 40,
  "ambiguities": [
    "GPA contradiction: story states 'My GPA was 3.2' but also 'I think it was 3.5'",
    "Graduation year contradiction: story states 'I graduated in 2024' but also 'graduating in 2026'",
    "Duration approximation: story states the work period is 'about forty months'"
  ],
  "evidence": {
    "full_name": "Nurzhan Abilov",
    "degree": "BSc in Statistics",
    "graduation_year": null,
    "gpa": null,
    "languages": "Kazakh, Russian, English.",
    "published_count": "one paper published",
    "experience_months_countable": "about forty months"
  }
}
```

One issue remains: `gpa_original_scale` is 4.0 even though both GPA values are null. This should ideally be null too.

## Part 2 — Scores and the Winner

The model receives each valid CV record and the rubric. It returns three scores from 0 to 5: academic, research, and experience.

The Python code applies the explicit counting rules, calculates the weighted total, and ranks the candidates. The model does not calculate the final total.

The formula is:

`0.5 × academic + 0.3 × research + 0.2 × experience`

| Candidate | Academic | Research | Experience | Weighted total |
|---|---:|---:|---:|---:|
| `story-01` — Aziza Bekova | 5 | 5 | 3 | **4.60** |
| `story-02` — Dias Yerzhanov | 0 | 1 | 5 | **1.30** |
| `story-03` — Lyazzat Omarova | 4 | 3 | 4 | **3.70** |
| `story-04` — Tamerlan Saparov | 4 | 2 | 5 | **3.60** |
| `story-05` — Aisha Nurlanqyzy | 5 | 3 | 2 | **3.80** |
| `story-06` — Nurzhan Abilov | 0 | 2 | 5 | **1.60** |

The code changed Dias's academic score from 2 to 0 because his GPA was missing. It also changed his experience score from 4 to 5 because the stated 36 months meet the 24-month requirement.

### Computed ranking

1. **Aziza Bekova (`story-01`) — 4.60**
2. Aisha Nurlanqyzy (`story-05`) — 3.80
3. Lyazzat Omarova (`story-03`) — 3.70
4. Tamerlan Saparov (`story-04`) — 3.60
5. Nurzhan Abilov (`story-06`) — 1.60
6. Dias Yerzhanov (`story-02`) — 1.30

The winner is Aziza Bekova. The difference between first and second place is 0.80 points.

### Comparison with the model's prose answer

The separate prose answer first named Lyazzat Omarova as the winner, but later changed its choice to Aziza Bekova. It also recommended Tamerlan Saparov as the second candidate, while the computed ranking placed Aisha Nurlanqyzy second.

Therefore, the prose answer was inconsistent with itself and did not fully agree with the computed ranking. The code provides a more reliable result because the scores and calculation are visible and can be checked.

## Part 3 — Written Answers

### 1. Which rule did you have to add, and what broke without it?

I added two rules. First, an approximate number of months is not a contradiction. In `story-01`, the model calculated seven months from the dates instead of using the stated eight months. It then counted zero months. In `story-06`, another model treated “about forty months” as uncertain and returned null instead of 40.

Second, the contradiction rule must apply to every field, including graduation year. In `story-06`, the model listed the conflict between 2024 and 2026 but still selected 2024. The added rule requires the field to be null when the story gives conflicting values.

I also adjusted the schema to allow null values when information, such as a paper title, was not provided.

### 2. Where did the model guess, and where did your code have to decide?

The model guessed scores between 0 and 5 because the rubric does not define every possible situation. For example, candidates with one published paper received different research scores: 1, 2, or 3. The model also reduced Dias's experience score because some of his work was part-time, although this penalty was not stated in the rubric.

The code applied clear rules instead. Dias received an academic score of 0 because his GPA was missing. His experience score changed from 4 to 5 because the stated 36 months meet the 24-month requirement. The code also calculated the weighted totals, ranked the candidates, and checked whether evidence quotes appeared in the original stories.

### 3. Did your prose ranking and your computed ranking agree?

Both methods selected Aziza Bekova as the winner, but they disagreed about second place. My code ranked Aisha Nurlanqyzy second with 3.80, while the prose answer recommended Tamerlan Saparov.

The prose answer was also inconsistent because it named Lyazzat Omarova as the winner at first and later changed its choice to Aziza. I trust the computed ranking more because the code uses visible scores, applies the counting rules, and calculates the totals consistently. Before trusting a prose answer alone, I would need to see consistent rankings and scores that follow the rubric across several runs.

### 4. The rubric has no anchor for a contradicted field. What did you do, and what should the rule be?

In `story-06`, Nurzhan stated that his GPA was 3.2, but then said it might have been 3.5. I set the GPA to null and recorded both values in the ambiguities field. The model gave an academic score of 0, and the code kept that score because the GPA was null.

I think the candidate should not automatically be treated as having no academic information. A better approach would be to mark the score as provisional, use the lower stated GPA only for a temporary estimate, and ask a human to verify the transcript before making the final decision.

### 5. How close were your top two candidates?

The top two candidates were not very close. Aziza Bekova scored 4.60, while Aisha Nurlanqyzy scored 3.80. The difference was 0.80 points, so Aziza was the clear winner in this run.

However, candidates in second, third, and fourth place scored 3.80, 3.70, and 3.60. These small differences may depend on how the model assigns scores between the rubric's anchors.

If the top two candidates were within 0.05 points, I would tell the committee that the scores were too close to make a confident decision. I would improve the system by verifying every evidence quote and defining fixed scoring rules in code instead of allowing the model to guess intermediate scores. I would also compare several runs and check whether the ranking changes.

---

## Reflection (optional, one short paragraph)

Having now written a role prompt, compressed a conversation, and ranked six
extractions — what will you do differently the next time you build something
that has to get reliable structured output out of a model?

>
