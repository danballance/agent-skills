---
name: comprehension-review
description: Generate a self-contained HTML walkthrough, with review questions, for changed code. Use when a small feature has been completed and a developer needs to understand what has changed. Do not use for tiny formatting-only diffs, routine lint fixes, or general bug-hunting review.
---

# Comprehension Review

## Goal

Help a human developer genuinely understand this change, not just skim it.

## Inputs

- Any context already present after having made the change
- Current working tree changes
- Review git history to understand the change over time

## Outputs

Produce all of the following:

1. `./artifacts/comprehension-review/{title}.html`
2. A brief terminal summary with:
   - report path
   - the main behavioural areas changed
   - an offer to answer questions or discuss topics from the review

## Rules

- Focus on comprehension, not exhaustive bug-finding.
- Every important claim about code behaviour must be grounded in the changed code.
- Include code snippets from the code as examples.
- Prefer explaining _why_ a change exists, _what path it affects_, and _what invariants must still hold_.
- If evidence is weak, say so explicitly instead of guessing.
- Keep the questions section short, high-signal, and based only on the generated walkthrough.

## Procedure

1. Inspect the diff and changed files.
2. Identify the behaviour change, data flow change, interface change, and test impact.
3. Situate the change in the history of changes from the git log.
4. Produce a structured summary. Example sections:
   - overview
   - changed files
   - behavioural walkthrough
   - invariants and risks
   - glossary
   - review questions with answers and explanations
5. Print the output path and the terminal summary.

## Review question design

- Determine the number of questions based on the significance of the change and the length of the review document.
- For a simple change a single question may be enough. A large change might warrant five or six.
- Include a mix of styles:
  - intent questions
  - control-flow or data-flow questions
  - invariant/risk questions
  - test-coverage questions
  - “what would break if...” questions
- Prefer short-answer or explanation-first multiple choice over trivia.
- Avoid asking about cosmetic edits.

## Failure conditions

Stop and report clearly if:

- there is no meaningful change,
- the change is too large to explain faithfully in one pass,
- the code path cannot be inferred from available files,

## Structure and Design Style

The review artifact should not use a fixed template. Its purpose is not merely to summarize the change; its purpose is to help a developer actively re-enter the codebase, understand what changed, and reduce comprehension debt. A predictable, repetitive structure can cause the reader to skim or tune out, especially when they are already overloaded by frequent code reviews.

Instead, generate each review as a deliberately designed walkthrough. The structure should be adapted to the shape of the change, the likely knowledge gaps, and the attention needs of the developer.

### Design Goal

Create a review experience that feels:

- **Human-readable**, not mechanically generated.
- **Engaging**, without becoming gimmicky.
- **Varied**, without becoming chaotic.
- **Grounded in the actual code**, not generic commentary.
- **Useful under time pressure**, especially when the developer may need to maintain or debug the change later.

### Invariants

Although the layout should vary, every review must satisfy the following comprehension goals:

1. The reader understands **what changed**.
2. The reader understands **why the change appears to matter**.
3. The reader understands **which files, functions, classes, modules, or behaviours are affected**.
4. The reader sees the **most important code excerpts**, not just prose.
5. The reader is guided through the change in a sensible order.
6. The reader is alerted to **risks, assumptions, edge cases, or surprising details**.
7. The reader completes a short comprehension check that tests genuine understanding.

These are outcome requirements, not section headings. Do not force them into a fixed order.

### Choose a Review Shape Based on the Change

Before writing the artifact, infer the most suitable review shape. Examples include:

#### Narrative walkthrough

Use when the change has a clear story: a feature was added, a bug was fixed, or behaviour was changed from one state to another.

Possible flow:

- The problem being addressed.
- The old path through the code.
- The new path through the code.
- The important implementation details.
- The implications for future maintainers.

#### System map

Use when the change touches several components, services, modules, or layers.

Possible flow:

- A lightweight map of affected areas.
- The role of each changed component.
- How data or control now flows between them.
- Where coupling, assumptions, or failure modes exist.

#### Before-and-after comparison

Use when the diff significantly alters existing behaviour.

Possible flow:

- What the code used to do.
- What it does now.
- Why the change matters.
- What new cases are handled.
- What cases may still be fragile.

#### Debugger’s tour

Use when the change is subtle, risky, or bug-fix oriented.

Possible flow:

- The symptom or failure mode.
- The root cause as implied by the change.
- The exact code path where the issue is addressed.
- How to verify the fix.
- What might still go wrong.

#### Concept-first explanation

Use when the change introduces a new abstraction, protocol, data model, algorithm, or architectural idea.

Possible flow:

- The new concept in plain language.
- Where it appears in the code.
- Why it exists.
- How to use or modify it safely.
- What mistakes future contributors should avoid.

#### Risk-led review

Use when the change is production-sensitive, security-sensitive, performance-sensitive, or unusually broad.

Possible flow:

- Highest-risk areas first.
- Key assumptions.
- Operational implications.
- Observability, rollback, or debugging notes.
- Code excerpts that support the risk assessment.

The agent may invent other review shapes when they better fit the change. The chosen shape should be implicit in the artifact’s design; it does not need to be announced formally unless that helps the reader.

### Use a Flexible Layout, Not a Fixed Template

Do not always use the same headings. Avoid repeatedly producing artifacts with identical section names such as:

- Summary
- Files Changed
- Key Changes
- Risks
- Review Quesions

Those headings are acceptable sometimes, but they must not become the default skeleton.

Prefer headings that reflect the actual change. For example:

- `The new request path`
- `Where the behaviour forks`
- `The quiet but important change`
- `What future you will need to remember`
- `The risky assumption in this diff`
- `How this fix prevents the old failure`
- `The three files that matter most`
- `What changed at the boundary`
- `Why this looks small but matters`
- `The mental model to keep`

The review should feel designed for this specific diff, not stamped out from a generic code-review report.

### Vary the Reading Rhythm

Use a mix of explanatory modes to keep the developer engaged. Avoid long, uniform blocks of prose.

Good reviews may include:

- Short narrative paragraphs.
- Annotated code snippets.
- Small file or module maps.
- Callout boxes.
- “Pause and check” moments.
- Before/after tables.
- Mini timelines.
- Data-flow explanations.
- “Watch out for this” notes.
- Debugging scenarios.
- Maintenance notes.
- Small conceptual diagrams using HTML/CSS or simple text.

Do not use all of these in every review. Select only the devices that help the reader understand this particular change.

### Code Snippet Guidance

Code excerpts are central to the review. They should be selected carefully.

Include snippets when they show:

- A changed control path.
- A new or modified public interface.
- A non-obvious condition.
- A new data structure or schema.
- A changed error path.
- A boundary between modules.
- A test that reveals intended behaviour.
- A risky assumption.
- A detail the maintainer is likely to need later.

Avoid dumping large blocks of code. Prefer compact excerpts with surrounding explanation.

Each snippet should answer at least one of these questions:

- “What should I notice here?”
- “Why did this change?”
- “What does this imply elsewhere?”
- “What could break if I misunderstand this?”
- “How would I debug this later?”

When helpful, annotate snippets inline or immediately below them. Highlight the important lines visually, but do not over-highlight. Highlighting should direct attention, not decorate the page.

### Visual Design Language

The artifact should be a single-page HTML document with a polished but lightweight design.

The visual style should be:

- Calm.
- Modern.
- Readable.
- Developer-oriented.
- Slightly editorial.
- Optimized for sustained attention.

Prefer:

- Generous spacing.
- Clear typographic hierarchy.
- Narrow-to-medium line widths for prose.
- Distinct treatment for code, commentary, warnings, and quiz content.
- Subtle visual grouping.
- Sticky or easily accessible progress/navigation when useful.
- Accessible contrast.
- Responsive layout.

Avoid:

- Dense walls of text.
- Excessive decoration.
- Generic dashboard aesthetics.
- Overly playful UI that undermines seriousness.
- Too many colors.
- Animations that distract from reading.
- Visual elements that look interactive but are not.

The design should support comprehension. It should not compete with the code.

### Controlled Creativity

Creativity is encouraged, but it must be bounded by accuracy and usefulness.

The agent may vary:

- The title.
- The ordering of sections.
- The framing metaphor.
- The style of callouts.
- The type of walkthrough.
- The review questions format.
- The visual rhythm.
- The way snippets are introduced.
- The balance between prose, diagrams, and tables.

The agent must not vary:

- The factual content of the code.
- The meaning of the diff.
- The level of evidence required for claims.
- The need to show important code.
- The requirement to identify uncertainty.
- The requirement to include a comprehension check.

Never invent intent, requirements, or production behaviour that is not supported by the code, tests, commit message, issue context, or other available project material. When intent is inferred, label it as an inference.

### Tone

Use the tone of a thoughtful senior engineer helping another developer build a reliable mental model.

The tone should be:

- Clear.
- Direct.
- Curious.
- Technically precise.
- Occasionally conversational.
- Respectful of the reader’s time.

Avoid:

- Marketing language.
- Empty praise.
- Generic AI-sounding summaries.
- Overconfident claims.
- Excessive caution where the code is clear.
- Infantilizing quiz language.
- Saying obvious things like “this code is important” without explaining why.

The review should not sound like a lint report. It should sound like an expert-guided comprehension session.

### Engagement Techniques

Use lightweight engagement techniques to keep the reader mentally active.

Examples:

- Ask the reader to predict what happens before revealing the answer.
- Present a small “follow the data” path through the code.
- Show the old behaviour, then reveal the new behaviour.
- Point out a subtle line and explain why it matters.
- Include a “future maintainer note.”
- Include a “debug this in production” scenario.
- Ask a short question before the quiz section.
- Use a small visual checkpoint after a dense explanation.

These techniques should be used sparingly. The goal is engagement, not entertainment for its own sake.

### Quiz Design

The quiz should test comprehension, not trivia.

Questions should focus on:

- Behavioural understanding.
- Code path understanding.
- Key assumptions.
- Risks and edge cases.
- How to modify or debug the change safely.
- Why a particular implementation choice matters.

Avoid questions that only ask the reader to recall filenames, line numbers, or superficial details.

Prefer question types such as:

- “Which path does the code take when...?”
- “What would break if this condition were removed?”
- “Why does this function need to be updated alongside that one?”
- “Which test best captures the intended behaviour?”
- “Where would you start debugging if this failed in production?”
- “What assumption is this change making?”

The quiz may vary in format. It can use multiple choice, short answer, scenario-based questions, or a mixture. Include answers or revealable explanations in the HTML document so the artifact is self-contained.

The quiz should feel like a final reinforcement of the walkthrough, not a school exam.

### Anti-Patterns

Do not produce a review that:

- Uses the same layout every time.
- Summarizes the diff without teaching the code.
- Lists files mechanically without explaining their roles.
- Includes a quiz disconnected from the walkthrough.
- Overwhelms the reader with every changed line.
- Hides uncertainty.
- Treats all changes as equally important.
- Uses decorative variety that does not improve understanding.
- Produces a generic “AI review” rather than a tailored comprehension artifact.

### Final Quality Bar

Before completing the artifact, check whether the review would help a developer answer these questions:

- “What changed?”
- “How does the new code work?”
- “Where are the important moving parts?”
- “What should I be careful about?”
- “How would I debug this later?”
- “Could I explain this change to another developer?”

If the answer is not yes, revise the structure, code excerpts, or quiz until the artifact becomes a useful comprehension aid.

# AI Writing Tropes to Avoid

Add this file to your AI assistant's system prompt or context to help it avoid
common AI writing patterns. Source: [tropes.fyi](https://tropes.fyi) by [ossama.is](https://ossama.is)

---

## Word Choice

### "Quietly" and Other Magic Adverbs

Overuse of "quietly" and similar adverbs to convey subtle importance or understated power. AI reaches for these adverbs to make mundane descriptions feel significant. Also includes: "deeply", "fundamentally", "remarkably", "arguably".

**Avoid patterns like:**

- "quietly orchestrating workflows, decisions, and interactions"
- "the one that quietly suffocates everything else"
- "a quiet intelligence behind it"

### "Delve" and Friends

Used to be the most infamous AI tell. "Delve" went from an uncommon English word to appearing in a staggering percentage of AI-generated text. Part of a family of overused AI vocabulary including "certainly", "utilize", "leverage" (as a verb), "robust", "streamline", and "harness".

**Avoid patterns like:**

- "Let's delve into the details..."
- "Delving deeper into this topic..."
- "We certainly need to leverage these robust frameworks..."

### "Tapestry" and "Landscape"

Overuse of ornate or grandiose nouns where simpler words would do. "Tapestry" is used to describe anything interconnected. "Landscape" is used to describe any field or domain. Other offenders: "paradigm", "synergy", "ecosystem", "framework".

**Avoid patterns like:**

- "The rich tapestry of human experience..."
- "Navigating the complex landscape of modern AI..."
- "The ever-evolving landscape of technology..."

### The "Serves As" Dodge

Replacing simple "is" or "are" with pompous alternatives like "serves as", "stands as", "marks", or "represents". AI avoids basic copulas because its repetition penalty pushes it toward fancier constructions (I've studied this!).

**Avoid patterns like:**

- "The building serves as a reminder of the city's heritage."
- "Gallery 825 serves as LAAA's exhibition space for contemporary art."
- "The station marks a pivotal moment in the evolution of regional transit."

---

## Sentence Structure

### Negative Parallelism

The "It's not X -- it's Y" pattern, often with an em dash. The single most commonly identified AI writing tell. Man I f\*cking hate it. AI uses this to create false profundity by framing everything as a surprising reframe. One in a piece can be effective; ten in a blog post is a genuine insult to the reader. Before LLMs, people simply did not write like this at scale. Includes the causal variant "not because X, but because Y" where every explanation is framed as a surprise reveal, the em-dash dismissal "X -- not Y", and the cross-sentence reframe where the same noun is negated then repositioned: "The question isn't X. The question is Y."

**Avoid patterns like:**

- "It's not bold. It's backwards."
- "Feeding isn't nutrition. It's dialysis."
- "Half the bugs you chase aren't in your code. They're in your head."

### "Not X. Not Y. Just Z."

The dramatic countdown pattern. AI builds tension by negating two or more things before revealing the actual point. Creates a false sense of narrowing down to the truth.

**Avoid patterns like:**

- "Not a bug. Not a feature. A fundamental design flaw."
- "Not ten. Not fifty. Five hundred and twenty-three lint violations across 67 files."
- "not recklessly, not completely, but enough"

### "The X? A Y."

Self-posed rhetorical questions answered immediately in the next sentence or clause. The model asks a question nobody was asking, then answers it for dramatic effect. Thinks this is the epitome of great writing.

**Avoid patterns like:**

- "The result? Devastating."
- "The worst part? Nobody saw it coming."
- "The scary part? This attack vector is perfect for developers."

### Anaphora Abuse

Repeating the same sentence opening multiple times in quick succession.

**Avoid patterns like:**

- "They assume that users will pay... They assume that developers will build... They assume that ecosystems will emerge... They assume that..."
- "They could expose... They could offer... They could provide... They could create... They could let... They could unlock..."
- "They have built engines, but not vehicles. They have built power, but not leverage. They have built walls, but not doors."

### Tricolon Abuse

Overuse of the rule-of-three pattern, often extended to four or five. A single tricolon is elegant; three back-to-back tricolons are a pattern recognition failure.

**Avoid patterns like:**

- "Products impress people; platforms empower them. Products solve problems; platforms create worlds. Products scale linearly; platforms scale exponentially."
- "identity, payments, compute, distribution"
- "workflows, decisions, and interactions"

### "It's Worth Noting"

Filler transitions that signal nothing. AI uses these phrases to introduce new points without actually connecting them to the previous argument. Also includes: "It bears mentioning", "Importantly", "Interestingly", "Notably".

**Avoid patterns like:**

- "It's worth noting that this approach has limitations."
- "Importantly, we must consider the broader implications."
- "Interestingly, this pattern repeats across industries."

### Superficial Analyses

Tacking a present participle ("-ing") phrase onto the end of a sentence to inject shallow analysis that says nothing. The model attaches significance, legacy, or broader meaning to mundane facts using phrases like "highlighting its importance", "reflecting broader trends", or "contributing to the development of...".

**Avoid patterns like:**

- "contributing to the region's rich cultural heritage"
- "This etymology highlights the enduring legacy of the community's resistance and the transformative power of unity in shaping its identity."
- "underscoring its role as a dynamic hub of activity and culture"

### False Ranges

Using "from X to Y" constructions where X and Y aren't on any real scale. In legitimate use, "from X to Y" implies a spectrum with a meaningful middle. AI uses it as a fancy way to list two loosely related things. "From innovation to cultural transformation" -- what's in between???? Nothing!

**Avoid patterns like:**

- "From innovation to implementation to cultural transformation."
- "From the singularity of the Big Bang to the grand cosmic web."
- "From problem-solving and tool-making to scientific discovery, artistic expression, and technological innovation."

---

## Paragraph Structure

### Short Punchy Fragments

Excessive use of very short sentences or sentence fragments as standalone paragraphs for manufactured emphasis. RLHF training has pushed models toward "writing for readability" aimed at the lowest common denominator: one thought per sentence, no mental state-keeping required. It's an inhuman style. No real person writes first drafts this way because it doesn't match how humans think or speak.

**Avoid patterns like:**

- "He published this. Openly. In a book. As a priest."
- "These weren't just products. And the software side matched. Then it professionalised. But I adapted."
- "Platforms do."

### Listicle in a Trench Coat

Numbered or labeled points dressed up as continuous prose. The model writes what is essentially a listicle but wraps each point in a paragraph that starts with "The first... The second... The third..." to disguise the format. Perhaps you told it to stop generating lists and it decided to do this instead... still very common.

**Avoid patterns like:**

- "The first wall is the absence of a free, scoped API... The second wall is the lack of delegated access... The third wall is the absence of scoped permissions..."
- "The second takeaway is that... The third takeaway is that... The fourth takeaway is that..."

---

## Tone

### "Here's the Kicker"

False suspense transitions that promise a revelation but deliver a point that did NOT need the buildup. The model uses these phrases to manufacture drama before an otherwise unremarkable observation LOL. Also includes: "Here's the thing", "Here's where it gets interesting", "Here's what most people miss", "Here's the starting point", "Here's the deal".

**Avoid patterns like:**

- "Here's the kicker."
- "Here's the thing about AI adoption."
- "Here's where it gets interesting."

### "Think of It As..."

The patronizing analogy. AI constantly reaches for "Think of it as..." or "It's like a..." to simplify concepts. The model defaults to teacher mode and assumes the reader needs a metaphor to understand anything. Often produces analogies that are less clear than the original concept.

**Avoid patterns like:**

- "Think of it like a highway system for data."
- "Think of it as a Swiss Army knife for your workflow."
- "It's like asking someone to buy a car they're only allowed to sit in while it's parked."

### "Imagine a World Where..."

The classic AI invitation to futurism. To sell the argument usually begins with "Imagine" followed by a list of wonderful things that will happen if the reader agrees with the premise.

**Avoid patterns like:**

- "Imagine a world where every tool you use -- your calendar, your inbox, your documents, your CRM, your code editor -- has a quiet intelligence behind it..."
- "In that world, workflows stop being collections of manual steps and start becoming orchestrations."

### False Vulnerability

Simulated self-awareness or honesty that reads as performative. The model pretends to break the fourth wall or admit a bias, creating a false sense of authenticity. Real vulnerability is specific and uncomfortable; AI vulnerability is polished and risk-free!!!!

**Avoid patterns like:**

- "And yes, I'm openly in love with the platform model"
- "And yes, since we're being honest: I'm looking at you, OpenAI, Google, Anthropic, Meta"
- "This is not a rant; it's a diagnosis"

### "The Truth Is Simple"

Asserting that something is obvious, clear or simple instead of actually proving it. If you have to tell the reader your point is clear, it very likely isn't. Also includes the dramatic reveal variant: "but none of them is the real story. The real story is..." -- claiming privileged insight while waving away everything before it.

**Avoid patterns like:**

- "The reality is simpler and less flattering"
- "History is unambiguous on this point"
- "History is clear, the metrics are clear, the examples are clear"

### Grandiose Stakes Inflation

Everything is the most important thing ever. AI inflates the stakes of every argument to world-historical significance. A blog post about API pricing becomes a meditation on the fate of civilization.

**Avoid patterns like:**

- "This will fundamentally reshape how we think about everything."
- "will define the next era of computing"
- "something entirely new"

### "Let's Break This Down"

The pedagogical voice that assumes the reader needs hand-holding. AI defaults to a teacher-student dynamic even when writing for expert audiences. Also includes: "Let's unpack this", "Let's explore", "Let's dive in".

**Avoid patterns like:**

- "Let's break this down step by step."
- "Let's unpack what this really means."
- "Let's explore this idea further."

### Vague Attributions

Attributing claims to unnamed authorities instead of being specific. AI loves to invoke "experts", "observers", "industry reports", and "several publications" without naming anyone. It also inflates the quantity of sources -- presenting what one person said as a widely held view, or writing "several publications have cited" when it means two. If you can't name the expert, you don't have a source.

**Avoid patterns like:**

- "Experts argue that this approach has significant drawbacks."
- "Industry reports suggest that adoption is accelerating."
- "Observers have cited the initiative as a turning point."

### Invented Concept Labels

AI clusters invented compound labels that sound analytical without being grounded. It appends abstract problem-nouns (paradox, trap, creep, divide, vacuum, inversion) to domain words — "supervision paradox", "acceleration trap", "workload creep" — and uses them as if they're established, rigorously defined terms. They function as rhetorical shorthand: name a thing, skip the argument. Multiple such labels in the same piece is a strong signal of AI slop.

**Avoid patterns like:**

- "the supervision paradox"
- "the acceleration trap"
- "workload creep"

---

## Formatting

### Em-Dash Addiction

Compulsive overuse of em dashes for dramatic pauses, parenthetical asides and pivot points. A human writer might use 2-3 per piece (and naturally); AI will use 20+.

**Avoid patterns like:**

- "The problem -- and this is the part nobody talks about -- is systemic."
- "The tinkerer spirit didn't die of natural causes -- it was bought out."
- "Not recklessly, not completely -- but enough -- enough to matter."

### Bold-First Bullets

Every bullet point or list item starts with a bolded phrase or sentence. Extremely common in Claude and ChatGPT markdown output. Almost nobody formats lists this way when writing by hand. It's a telltale sign of AI-generated documentation and blog posts AND README files (especially with emojis).

**Avoid patterns like:**

- "Every single bullet point begins with a bold keyword."
- "**Security**: Environment-based configuration with..."
- "**Performance**: Lazy loading of expensive resources..."

### Unicode Decoration

Use of unicode arrows (->), smart/curly quotes, and other special characters that can't be easily typed on a standard keyboard. Real writers typing in a text editor produce straight quotes and -> or =>. Claude in particular loves the -> arrow.

**Avoid patterns like:**

- "Input → Processing → Output"
- "This leads to better outcomes → which means higher engagement"
- "“Smart quotes” instead of straight "quotes" that you’d actually type"

---

## Composition

### Fractal Summaries

"What I'm going to tell you; what I'm telling you; what I just told you" -- applied at every level of the document. Every subsection gets a summary. Every section gets a summary. The document itself gets a summary.

**Avoid patterns like:**

- "In this section, we'll explore... [3000 words later] ...as we've seen in this section."
- "A conclusion that restates every point already made in the previous 3000 words"
- "And so we return to where we began."

### The Dead Metaphor

Latching onto a single metaphor and beating it into the ground across the entire thing. A human writer would introduce a metaphor, use it then move on. AI will repeat the same metaphor 5-10 times.

**Avoid patterns like:**

- "The ecosystem needs ecosystems to build ecosystem value."
- "Walls and doors used 30+ times in the same article"
- "Every paragraph finds a way to say "primitives" again"

### Historical Analogy Stacking

ESPECIALLY COMMON IN TECHNICAL WRITING: Rapid-fire listing of historical companies or tech revolutions to build false authority.

**Avoid patterns like:**

- "Apple didn't build Uber. Facebook didn't build Spotify. Stripe didn't build Shopify. AWS didn't build Airbnb."
- "Every major technological shift -- the web, mobile, social, cloud -- followed the same pattern."
- "Take Spotify... Or consider Uber... Airbnb followed a similar path... Shopify is another example... Even Discord..."

### One-Point Dilution

Making a single argument and restating it in 10 different ways across thousands of words. The model pads a simple thesis to feel "comprehensive" by rephrasing the same idea with different metaphors, examples, and framings. An 800-word argument becomes 4000 words of circular repetition.

**Avoid patterns like:**

- "The same point, restated eight ways across 4000 words."
- "Each section rephrases the thesis with a different metaphor but adds nothing new"

### Content Duplication

Repeating entire sections or paragraphs verbatim within the same piece. This happens when the model loses track of what it has already written, especially in longer pieces. A dead giveaway of unedited AI output. Less common nowadays.

**Avoid patterns like:**

- "The same section appeared twice, word-for-word identical."
- "Paragraph 3 and paragraph 17 are the same sentence reworded"

### The Signposted Conclusion

Explicitly announcing the conclusion with "In conclusion", "To sum up", or "In summary". Competent writing doesn't need to tell you it's concluding. The reader can feel it. AI signals its structural moves because it's following a template, not writing organically.

**Avoid patterns like:**

- "In conclusion, the future of AI depends on..."
- "To sum up, we've explored three key themes..."
- "In summary, the evidence suggests..."

### "Despite Its Challenges..."

The rigid formula where AI acknowledges problems only to immediately dismiss them. Always follows the same beat: "Despite its [positive words], [subject] faces challenges..." then ends with "Despite these challenges, [optimistic conclusion].".

**Avoid patterns like:**

- "Despite these challenges, the initiative continues to thrive."
- "Despite its industrial and residential prosperity, Korattur faces challenges typical of urban areas."
- "Despite their promising applications, pyroelectric materials face several challenges that must be addressed for broader adoption."

---

Remember: any of these patterns used once might be fine. The problem is when
multiple tropes appear together or when a single trope is used repeatedly.
Write like a human: varied, imperfect, specific.
