---
name: brainlift
description: >
  Build or refine a BrainLift — a DOK-framework research document that proves
  domain expertise through depth. Uses a bottom-up workflow (Purpose → Scope →
  Sources → Knowledge Tree → Insights → SPOVs) and applies the writing-guide
  rubric at every step. Human generates ideas; skill shapes, challenges, and
  ghostwrites drafts for human approval. Use for "/brainlift <topic>" to start
  new, "/brainlift refine <path>" to critique and improve existing, or
  "/brainlift pdf <path>" to regenerate the PDF.
args:
  - name: args
    type: string
    description: >
      Topic for a new BrainLift, "refine <path>" to improve an existing doc,
      "pdf <path>" to regenerate PDF only, or topic + optional flags:
      --research (run Scholar GPT + Claude WebSearch parallel research phase),
      --categories N (target number of Knowledge Tree categories, default 3-5),
      --spovs N (target number of SPOVs, default 3-5),
      --experts N (target number of experts, default 5-8).
---

# BrainLift Skill Invoked

User has requested: `/brainlift {{args}}`

---

## Always load first

Read `~/.claude/skills/brainlift/WRITING_GUIDE.md`. It contains:
- The structural rubric (Title → Purpose → North Star → Scope → SPOVs → Experts → Insights → Knowledge Tree → Assumptions → Self-Critique)
- Writing voice rules (short sentences, no hedging, no em dashes if author's style avoids them)
- Quality checks (interview test, teammate test, "so what" test, depth test, standalone test)
- What separates top-ranked BrainLifts from the rest

Cite the guide throughout facilitation. When the user's draft violates a rule, name the rule.

---

## Step 0: Parse args and detect mode

**Flags (strip from args after detection):**
- `--research` — run the optional parallel research phase (Step 0.5) before Step 1
- `--categories N` — override default category target (3-5)
- `--spovs N` — override default SPOV target (3-5)
- `--experts N` — override default expert target (5-8)

**Mode detection (after stripping flags):**
- First word is `refine` and remaining arg is a path to an existing `.md` file → **Refine mode**, skip to Step 9
- First word is `pdf` and remaining arg is a path → **PDF-only mode**, skip to Step 10
- Anything else → **New BrainLift mode**, treat args as the topic

**For new BrainLifts:**
- Derive slug from topic — lowercase, hyphens, max 40 chars
- Output path: `brainlifts/<slug>/BRAINLIFT.md`
- Create directory

**Confirm with user before proceeding:** "Topic: [X]. Slug: [Y]. Output: [path]. Flags: [list]. Ready?"

---

## Step 0.5 (optional): Parallel research phase

**Only run if `--research` flag was passed** OR user explicitly says they don't yet have a source list.

Goal: map the research landscape before the user writes their Purpose. Two tracks run in parallel — Scholar GPT (user-operated) and Claude WebSearch (automated).

### Scholar GPT track (user runs this)

Give the user this prompt to paste into Scholar GPT (or ChatGPT with the Scholar GPT plugin):

```
You are a domain researcher. Use Scholar GPT's academic grounding to map the research landscape for a topic.

TOPIC: <topic>

Ask 4-6 research questions via Scholar GPT. For each, investigate one of these dimensions:
1. Key researchers and thinkers — names, affiliations, what they're known for, where they DISAGREE
2. Foundational theories and frameworks — with citations
3. Active debates — conventional wisdom vs challengers
4. Empirical findings — studies, meta-analyses, effect sizes. Prefer recent work (2015+) that contradicts older findings
5. Adjacent domains — transferable insights from other fields
6. Common misconceptions among practitioners

Be specific — cite real researchers and real papers, not generic claims.

OUTPUT format:
- Researchers: [name, affiliation, known for, which camp]
- Frameworks: [name, description, citation]
- Debates: [topic, conventional view, challenger view, challenger name]
- Findings: [claim, source, year, effect size if applicable]
- Adjacent domains: [domain, insight]
- Misconceptions: [common belief, actual finding]

Return findings as a structured list. Do NOT ask questions. Do NOT summarize at the end.
```

### Claude WebSearch track (skill runs this)

In parallel, spawn a Claude subagent via Agent tool with this prompt:

```
You are a domain researcher doing deep extraction from academic and research sources.

TOPIC: <topic>

TOOLS: Use WebSearch to find sources, then WebFetch to extract details.
Run WebSearch calls SEQUENTIALLY (not parallel) to avoid rate limits.

TARGET SOURCES: Google Scholar results, university faculty pages, ResearchGate profiles, journal abstracts, conference proceedings, systematic reviews.

BUDGET:
- Run 4-6 WebSearch calls sequentially
- WebFetch the top 4-6 results
- For each fetch, ask specific extraction questions

For each search, target a different angle:
- "<topic> systematic review OR meta-analysis"
- "<topic> leading researchers disagreement"
- "<topic> empirical evidence recent findings"
- "<topic> misconceptions practitioners"
- "<topic> [adjacent field] transferable insights"

OUTPUT: Write findings to brainlifts/<slug>/.research-claude.json using the same schema as Scholar GPT (researchers, frameworks, debates, findings, adjacent_domains, misconceptions).

If you encounter errors, include whatever you found. Do NOT ask questions.
```

### Merge

When both tracks return:
1. Deduplicate researchers by name. Mark `confidence: high` if both tracks found them, `confidence: single-source` otherwise.
2. Deduplicate frameworks and findings.
3. Flag contradictions between tracks explicitly.
4. Save merged artifact to `brainlifts/<slug>/.research.json`.

**Then present the landscape to the user before Step 1:** "Here are the key thinkers, the main debates, and the empirical findings. Before we write the Purpose, what stands out to you?"

---

## Step 1: Purpose, North Star, Scope

### Purpose (2-3 sentences, 3-belief pattern)

Ask the user:
1. **What is this BrainLift proving?** (The domain, the claim)
2. **Who is the population?** (Specific — not "athletes," say "intermediate lifters" or "children ages 4-7")
3. **What three beliefs does this rest on?** (Each one becomes a research question)

Ghostwrite a draft Purpose in the guide's pattern:

> "The purpose of this BrainLift is to [build / prove / establish] [what] for [who]. It starts from three beliefs: [belief 1], [belief 2], and [belief 3]."

**Apply rules:**
- No hedging
- Product named as motivation, NOT as proven claim (see BL examples: "build a research-backed foundation for [product]")
- If draft runs over 3 sentences, cut

**One refinement round** — user revises, you apply.

### North Star (one testable question)

Ask: **"What single question does this BL answer? If you had to filter every design decision through one question, what is it?"**

Ghostwrite a draft. Test against the guide's examples. Must be:
- Testable (yes/no answer possible)
- Specific (names the mechanism, not generic)
- Short

### In Scope (5-8 bullets, chain structure)

Ask: **"What's the argument chain? Walk me from the problem to the solution."**

Each bullet must:
- Follow logically from the one before (a chain, not a shopping list)
- Correspond to evidence in the eventual Knowledge Tree
- Name the topic + what specifically about it

Include opposing arguments in scope, don't quarantine them.

### Out of Scope (4-6 bullets)

Ask: **"What's adjacent but deliberately out? What would a reader expect to see that you're choosing not to cover?"**

Each bullet: name what's out + short reason. **Never reference team members by name** (per guide). The document must read standalone.

### Checkpoint

Save to file. Generate PDF. User reviews before continuing.

---

## Step 2: Experts — with camp-grouping

### Target count: 5-8 (per guide; override with `--experts N`)

From research (if Step 0.5 ran) OR from user's existing knowledge, compile candidate experts. Present to the user in **camps** — groups that represent the tensions in the field:

```
**Camp A** (argues X):
- [Researcher 1] — [position, key work]
- [Researcher 2] — [position, key work]

**Camp B** (argues Y):
- [Researcher 3] — [position, key work]
- [Researcher 4] — [position, key work]

**Bridge / Synthesis thinkers:**
- [Researcher 5] — [what they contribute that others miss]

**Adjacent domain voices:**
- [Researcher 6] — [different field, transferable insight]
```

Ask: **"Which of these resonate? Whose work changed how YOU think about this problem? Are there experts you already follow who should be on this list?"**

Collect the user's curated list (5-8 experts). Per expert, record:
- **Who** (name + title)
- **Focus** (1-2 sentences)
- **Why Follow** (specific dependency on their work — not generic relevance)
- **Where** (link)

**Rule:** if the author can't explain in conversation why an expert's work matters to a specific claim, cut them.

---

## Step 3: Sources — Knowledge Tree structure

### Target count: 9-15 unique sources (per guide)

### Category structure

Ask: **"How do your sources group? What are the main evidence categories?"**

Target 3-5 categories (override with `--categories N`). Each category must have at least one Supporting and one Challenging subcategory. Emerging subcategories are for evidence pointing toward the thesis but not yet tested in the author's specific context.

### Source selection — interactive process

For each category:
1. Ask user for the strongest Supporting paper they know
2. Ask for the strongest Challenging paper (what argues the opposite)
3. Ask for any Emerging papers (recent, pointing forward)

**If user doesn't know a Challenging paper for a category**, offer either:
- **Scholar GPT prompt** — draft a targeted prompt for the user to run, with specific inclusion criteria and output format
- **Claude WebSearch** — run WebSearch + WebFetch to find and verify candidate papers

Return 2-3 finalists with:
- Full citation (DOI verified)
- One-sentence summary of what it proves
- Honest weakness or limit

User picks. Don't invent citations.

### Source reuse

If a paper serves two categories, use it in both — but write DIFFERENT DOK 1 facts and DIFFERENT DOK 2 summaries for each appearance. Shows deeper engagement.

### Checkpoint

Save experts + source list + category structure to file. User reviews.

---

## Step 4: Knowledge Tree — DOK 1 Facts + DOK 2 Summaries

### DOK 2 review prompts (before writing)

After the KT structure is populated, ask the user:

```
I've organized the research into [N] categories. Before we write the DOK 1/2 entries, I want to know what stands out to you:

1. **What surprises you here?** What contradicts something you believed before reading this?
2. **What's missing?** You know this domain — what important perspective or finding is absent?
3. **Where do you disagree with the research?** Not where you wish it said something different — where your experience tells you the findings are incomplete or wrong.
```

Record their answers. These become raw material for DOK 3 insights later.

### For each source

**DOK 1 — Facts (1-3 per source)**

Rules:
- Each fact must be distinct — don't repeat the same thing three ways
- Cite specific findings from the paper, not interpretations
- If only one fact, that's fine. Don't pad.

**DOK 2 — Summary (2-4 sentences per source)**

Rules:
- Write in the author's voice. Short sentences. No hedging.
- Don't restate the facts — interpret them for the argument
- Challenging summaries end on the challenge, not a rebuttal (save rebuttals for SPOVs)
- No academic tone. Sound like explaining to a smart person over coffee.

### Workflow

For each paper:
1. **If the author has read it:** ask them for the 1-3 facts and the summary. Ghostwrite polish if voice needs tightening.
2. **If the author has not read it:** offer to fetch the abstract and draft facts + summary for their review. Flag explicitly that they need to read the paper before defending it.

### Checkpoint

Save Knowledge Tree to file. User reviews each entry.

---

## Step 5: DOK 3 — Insights (3-5)

### What an insight is

An original conclusion that combines 2+ sources and says something neither source says alone. Synthesis, not summary.

### Socratic prompts (use these verbatim)

Ask the user:

```
Now I want to help you find the insights buried in your reactions. These are your ideas — not summaries of what you read, but the new connections your brain is making.

Try these prompts (answer whichever ones spark something):

1. **"Most people in this field think ___, but actually ___."** (Fill in the blank. What's the conventional wisdom that your research suggests is incomplete or wrong?)

2. **"The thing that [Expert A] gets right that [Expert B] misses is ___."** (Where does one expert's blind spot get filled by another?)

3. **"I used to think ___, but after reading this I now think ___."** (What belief did you update? The bigger the update, the more interesting the insight.)

4. **"If I had to explain the ONE thing that makes this problem hard to solve well, it's ___."** (What's the core difficulty that most designs get wrong?)

5. **"[Finding from domain A] actually explains why [problem in domain B] is so persistent."** (Cross-domain connection — this is where the spikiest insights live.)

Take as many or few of these as are useful. I'm looking for 3-5 insights.
```

### Evaluate responses

- **Restatement of a single source** → push back: "That sounds like it's coming from [source]. What does it mean when you combine it with [different source]?"
- **Generic** → push for specificity: "Can you give me the concrete version? What design decision does this change?"
- **Genuinely novel** → reinforce and probe: "That's interesting — what would someone who disagrees with you say? How would you defend it?"

### Ghostwrite drafts

Per insight:
- **Theme label** (e.g., "The Invisible Layer," "The Signal Problem")
- **Body:** name specific sources and years. State what each says individually. State what they say together. One paragraph.

**Guide's pattern:**
> "[Source A] found [X]. [Source B] found [Y]. Neither paper talks to the other. Together they say something neither says alone: [Z]."

### Rules

- Every insight names specific sources with years
- Each insight must connect to at least one SPOV (check later)
- 3-5 insights total

### Checkpoint

User reviews drafts. One refinement round max.

---

## Step 6: DOK 4 — SPOVs (3-5)

### What a SPOV is

A contrarian, defensible claim synthesized from 2+ insights. "Spiky" because it makes someone in the industry disagree.

### The tests

- **Teammate test:** If a teammate reads it and immediately nods along, it's not spiky enough
- **Named-counter test:** Every SPOV must name a specific person, product, or school of thought that would disagree
- **Rebuttal test:** Every SPOV must include an honest rebuttal — stating the strongest version of the counter-argument, not a strawman

### Facilitation

Ask the user:
- **"Where do you disagree with the field?"**
- **"What does the industry believe that your research suggests is incomplete?"**
- **"Who would read this and push back hardest? What would they say?"**

Draft structure per SPOV:
- **Title:** short, punchy, reframes the problem. Pattern: take what the industry believes and flip it.
- **Elaboration:** one paragraph. Cite 2-3 sources from KT. Name the counter-argument. Deliver the rebuttal.

### Evaluate SPOVs

- **Safe / consensus** → "I don't think anyone would argue with that. What's the version that would make a smart colleague push back?"
- **Contrarian without evidence** → "That's provocative, but what's the evidence trail? Can you walk me from DOK 2 facts through DOK 3 insights to this claim?"
- **Strong** → "Good. Now — what's the strongest argument AGAINST this position? And does your SPOV survive it?"

### Rules

- SPOVs should build on each other — each sets up the next
- 3-5 SPOVs. Quality over quantity.
- Name names. Competitors, products, researchers.
- The counter-argument must be the strongest version, not a strawman.

### Checkpoint

User reviews. One refinement round max.

---

## Step 7 (optional): Assumptions + Self-Critique

### Key Assumptions

Format: "[Assumption]. Fail: [what would prove it wrong]."

Ask: **"What does your thesis depend on that you haven't proven yet? If it turned out to be false, what would you see?"**

### Self-Critique

Add a `## Self-Critique` section at the bottom of the document. Apply this checklist honestly:

```
- [ ] Every SPOV has a named counter and rebuttal
- [ ] Every insight traces to DOK 2 sources
- [ ] DOK 2 includes both supporting AND challenging evidence per category
- [ ] Every In Scope bullet connects to the Knowledge Tree
- [ ] Experts map to specific claims, not generic relevance
- [ ] Document is defensible per the interview test
- [ ] Gap: [honest gap — don't hide these]
```

Flag honest gaps explicitly. Do NOT silently fix them — the human decides what to strengthen.

---

## Step 8: Assembly + PDF

Compile the full document from all collected sections.

**Writing voice rules (from guide):**
- Short sentences
- No hedging
- No em dashes if author's style avoids them
- Summaries sound human, not abstract-y
- Every claim defensible in conversation

Save to `brainlifts/<slug>/BRAINLIFT.md`.

### Generate PDF

```bash
/Users/rajatarora/Gauntlet/CapStone/brainlifts/.venv/bin/python ~/.claude/scripts/brainlift_pdf.py <md-path> <pdf-path>
```

If venv path doesn't exist, note and skip (markdown is the primary deliverable).

### Final quality check — apply all 5 tests from guide

1. **Interview test** — can the author answer "what does this paper say" for every source?
2. **Teammate test** — does any SPOV get a nod from a teammate? If so, rewrite.
3. **"So what" test** — every In Scope bullet connects to KT; every insight connects to SPOV
4. **Depth test** — 9-15 sources, each defensible
5. **Standalone test** — can an outside reader understand the argument without team context?

Report results to user. Flag any failures explicitly.

---

## Step 9: Refine Mode

If Step 0 detected `refine <path>`:

1. Read the existing BrainLift
2. Load the writing guide (`~/.claude/skills/brainlift/WRITING_GUIDE.md`)
3. Apply each section rule from the guide to the current document
4. Present findings as section-by-section critique:
   - Purpose: does it follow the 3-belief pattern? Is it 2-3 sentences?
   - North Star: is it testable?
   - Scope: is it a chain? 5-8 bullets? Each defensible?
   - SPOVs: spiky? Named counter? Rebuttal? Would a teammate nod?
   - Experts: each tied to a specific claim?
   - Insights: synthesis or summary? Named sources with years?
   - Knowledge Tree: each category have Supporting + Challenging? 9-15 sources?
5. Ask user which weaknesses to address
6. Run targeted facilitation for weak sections (Steps 1-7 as needed)
7. Rewrite the improved document to the same path
8. Regenerate PDF

---

## Step 10: PDF-Only Mode

If Step 0 detected `pdf <path>`:

1. Verify the markdown exists at `<path>`
2. Run the PDF script:
   ```
   /Users/rajatarora/Gauntlet/CapStone/brainlifts/.venv/bin/python ~/.claude/scripts/brainlift_pdf.py <path> <path with .pdf>
   ```
3. Report result

---

## Step 11: Report

```
BrainLift assembled: brainlifts/<slug>/BRAINLIFT.md
PDF: brainlifts/<slug>/BRAINLIFT.pdf

Sections:
- Purpose + North Star + Scope: confirmed
- Experts: <count>
- Knowledge Tree: <category count> categories, <source count> sources
- DOK 3 Insights: <count>
- DOK 4 SPOVs: <count>
- Assumptions: <count or "skipped">
- Self-Critique: <gap count or "clean">

Quality checks:
- Interview test: pass / gaps flagged
- Teammate test: pass / SPOV N needs sharpening
- So-what test: pass / orphaned sections flagged
- Depth test: pass / over-sourced or under-sourced
- Standalone test: pass / team-specific references flagged
```

---

## What this skill does NOT do

- **Invent DOK 3 insights or DOK 4 SPOVs.** The positions, connections, and claims come from the human's brain. The skill facilitates and ghostwrites drafts; the human approves or revises.
- **Skip interactive checkpoints.** Each step requires human input. Don't run steps in batch without confirmation.
- **Over-iterate.** One refinement round per section. The author's conviction and voice matter more than polish.
- **Silently fix weak sections.** Self-critique flags gaps visibly. The human decides whether to strengthen or accept.
- **Add sources the author hasn't read or can't defend.** If a paper makes the cut, the author must pass the interview test for it.
- **Run research without permission.** The `--research` flag is opt-in. Fresh BLs with no source list benefit from it; BLs where the user already knows their sources should skip it.
