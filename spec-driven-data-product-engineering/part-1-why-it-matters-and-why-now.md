# Spec-Driven Data Product Engineering: Part 1, Why It Matters and Why Now

*Part 1 of 2. This piece makes the case for spec-driven data product engineering. Part 2 goes inside a real build: what a spec actually has to carry, how the process runs end to end, and what a working proof of concept showed.*

A data team writes a solid-looking requirements document for a customer analytics pipeline: source tables, key metrics, a rough definition of "active customer." They hand it to a general-purpose AI coding agent, expecting the document to do what a good requirements doc always did: get the build to the right answer. The agent reads it, builds a pipeline in an afternoon, passes its own tests, and ships a dashboard that looks sharp. Three weeks later, in a leadership review, someone asks why the "active customer" count doesn't match the number Finance has been using all year. It turns out the document specified a 90-day lookback for one metric and never specified the window for this one, a gap a senior engineer would have caught and gone back to clarify. The agent didn't flag it. It picked a reasonable-looking interpretation and built confidently on top of it. Nobody in the room can point to a decision that says that choice was right, because nobody ever made that decision. The agent quietly made it for them.

That's the more realistic version of this problem: this isn't a story about nobody writing anything down. Something was written down. It just wasn't precise enough to be built from literally, and nothing in the process checked the finished build against it before it reached a stakeholder. Handing a generic coding agent a document is not the same thing as running a spec-driven build, and that gap is exactly where the risk lives.

## Why a generic coding agent doesn't solve this

It's tempting to assume the fix is simply "write something down and hand it to the agent," the way the team in the opening did. That's closer, but it still isn't enough. A general-purpose coding agent is built and evaluated against a fairly narrow bar: does the code compile, do the tests pass, does it follow reasonable conventions. That bar is sufficient for most software, where correctness is a property of behavior: a function either returns the right value or it doesn't, and a test catches the difference.

A data product breaks that bar in a specific way. A pipeline can be functionally flawless (clean code, green tests, sensible structure) and still be *substantively* wrong, because it encodes the wrong definition of the business. Some agents can flag ambiguity when asked to. Few workflows actually ask them to, and fewer still stop the build until a human answers. A generic agent handed an imprecise document, left to its default behavior, doesn't fail loudly. It fills the gap with a reasonable-looking guess and keeps going, the same way it did in the opening.

There's a third failure mode worth naming separately, and it happens even earlier. Before an agent can get a business definition right, it has to know what already exists: which tables are real, how they relate, where a given metric actually lives. A generic agent starting from a blank spec doesn't have that visibility either, so it isn't only guessing at meaning, sometimes it's guessing at structure too.

<div align="center">

![Three kinds of correctness: functional, contextual, and substantive](images/functional-vs-substantive-correctness.png)

*<sub>Passing tests proves the code runs. Knowing the data estate proves it isn't guessing at structure. Neither one proves it means what the business needs it to mean.</sub>*

</div>

That's the case for treating this as a discipline problem, not just a documentation problem or a tooling problem. It takes both a spec precise enough to be built from literally, and a process that checks the build against it before anyone trusts the output. Neither one alone closes the gap.

## What actually goes into building an AI-native data product

Part of why that gap is so easy to fall into is that "build the pipeline" undersells how much a data product actually requires. A working, trustworthy data product isn't one artifact. It's several, each with its own logic and its own way of going wrong:

- **A data model:** entities, relationships, grain, and keys, which determine whether a number even means what it claims to mean
- **Ingestion and transformation logic:** how raw data becomes something usable, and every rule applied along the way
- **Validation and quality tests:** not just "does the pipeline run" but "is this record actually correct," checked against real business rules
- **Metadata and lineage:** what a field means, where it came from, and what changed it, so a number can be explained months later by someone who didn't build it
- **Access and governance controls:** who can see what, what needs masking, and which regulatory boundaries apply
- **Documentation and runbooks:** how to operate, troubleshoot, and extend the product without the original builder in the room
- **Monitoring and freshness guarantees:** how anyone knows the product is still correct after the day it shipped, not just on the day it was demoed
- **A semantic and ontology layer:** the mapping from raw schema to business meaning, the layer that lets a person or an agent ask what "active" means and get an answer instead of a guess

An AI-native data product adds one more requirement on top of that list: a consumption layer built to be queried directly, whether that's a conversational interface, dashboards, or both, sitting on top of the semantic layer rather than the raw schema, with a path for a wrong answer to correct the meaning it was drawn from, not just the code that generated it.

<div align="center">

![The components of an AI-native data product](images/eight-components-data-product.png)

*<sub>Eight components make the data trustworthy. The consumption layer on top is where it gets used, and where a wrong answer should correct the meaning, not just the code.</sub>*

</div>

A generic coding agent, or a human engineer working from a thin requirements doc, can generate pipeline code quickly. The rest, the data model's rigor, validation, metadata, governance, documentation, monitoring, and the semantic layer that makes any of it queryable, is where the real work sits, and where the real risk lives.

## The time problem

Each of those components tends to have its own owner, its own review cycle, and its own dependency on the one before it: the data model has to be validated before the pipeline gets built, the pipeline has to be trusted before governance signs off, and governance has to sign off before anything reaches a stakeholder. That dependency chain, not the coding itself, is why data products have traditionally taken so long to design, build, and deliver. Weeks turn into months, and most of that time isn't spent writing code. It's spent in the coordination between components that were never fully specified up front.

<div align="center">

![Time to working code versus time to a trustworthy data product](images/time-to-trustworthy-data-product.png)

*<sub>AI compresses the coding step. The other seven components still have to happen, whether or not anyone can see them happening.</sub>*

</div>

This is the pressure AI agents are being brought in to relieve, and it's exactly where doing it carelessly makes things worse, not better. An agent can compress the coding step from weeks to hours. It cannot compress the modeling, validation, governance, and documentation work that made the old process slow in the first place. If that work isn't captured in a spec the agent can build from, it doesn't happen faster. It either doesn't happen at all, or it happens after the fact, in production, in front of the client or the board asking why the numbers don't match.

## The gap isn't "no spec." It's "spec written for the wrong reader."

Most data products aren't built with nothing written down. BRDs get written, tickets get filed, design gets reviewed, at least in organizations with any real engineering maturity. The idea that data teams generally work ad hoc, with no spec at all, doesn't hold up as the common case.

The real gap is who that spec was written for. It was written to get sign-off from a stakeholder: readable enough for a business sponsor, precise enough for an engineer to start from. And that worked, because the engineer reading it could fill in the gaps. A vague requirement like "flag high-risk patients" gets resolved by a senior engineer who's sat in the domain long enough to know what "high-risk" means in practice, what edge cases matter, and which shortcuts are safe to take.

An AI agent doesn't have that judgment. It takes the spec literally, which means the spec has to *be* literal. That's the actual shift underway: the spec now has to carry the context a senior engineer used to carry in their head, because there's no longer a person in the loop to quietly fill the gap.

## Why this is harder for data products specifically

Specifying a typical software feature is largely about behavior: given this input, produce this output. Specifying a data product means specifying the data itself, and that's a different and harder problem, for reasons any architect will recognize:

- **The data model carries meaning, not just structure.** Getting the grain or the dimension/fact split wrong doesn't throw an error. It silently produces a number that's confidently wrong.
- **Source data is never as clean as the schema implies.** Undocumented codes, inconsistent formats, and missing reference data mean the real behavior of a system only shows up once you profile the actual data, not the data dictionary.
- **Business context can't be inferred from a schema.** What counts as a valid patient cohort, a completed order, or a churned customer is domain knowledge, not something a table definition encodes.
- **Institutional judgment is the hardest thing to write down.** The edge cases a senior person would "just know" to handle, because they've been burned by them before, are exactly the things a spec has to make explicit: an AI builder has no scar tissue to draw on.

Get it wrong in typical software and a feature misbehaves visibly. Get it wrong in a data product and you get a dashboard that looks correct, gets presented to a client or a board, and is quietly wrong underneath.

## What spec-driven engineering actually is

Put the pieces together and the definition is simple: a spec-driven data product treats the specification as the contract the build is continuously judged against, not a document written once for approval and then abandoned. The build isn't "done" when the code runs. It's done when it satisfies the spec, and every step from spec to build to validation stays traceable.

<div align="center">

![Spec-driven data product engineering operating model](images/spec-driven-operating-model.png)

*<sub>The spec is the one artifact both the human and the AI builder are accountable to. The loop back into it is what keeps a gap from becoming production's problem.</sub>*

</div>

The loop matters more than the linear read suggests. When validation finds a gap, the fix isn't a patch to the code. It's a correction to the spec, followed by a rebuild. That discipline is what keeps the spec authoritative instead of becoming stale documentation within a quarter.

This overlaps with two things worth naming briefly: data contracts, which govern the ongoing relationship between systems once a pipeline exists, and spec-driven development, which applies "specification before code" to AI-built software generally. What's described here is the version of that discipline aimed specifically at getting a data product built correctly the first time.

## Does this just move the bottleneck?

If the spec now has to carry the judgment a senior engineer used to carry, doesn't writing it become the new bottleneck? Not exactly. The expensive human time moves rather than disappears, from debugging a build after the fact to getting the spec right before one starts. It also isn't a blank-page exercise: an agent can profile the source schema, draft candidate validation rules from what the data actually looks like, and surface ambiguous fields before a human opens the document. That doesn't remove the human. It changes the job from typing table definitions to reviewing and approving a first draft of intent. The bottleneck shrinks down to the part that was always going to need a person: deciding what "active" actually means, not transcribing a schema anyone could have profiled automatically.

## The judgment has to live somewhere

None of this is really a story about AI agents. It's a story about what happens to judgment that used to live quietly in a senior engineer's head, the instinct to stop and ask what "active" actually means, instead of picking an answer and moving on. That judgment doesn't disappear when an agent takes over the build. It just has nowhere left to live except the spec.

There's a sharper way to say this: the goal isn't more documentation, it's determinism. An ambiguous spec doesn't just risk one wrong guess, it risks a different wrong guess every time the build runs, ninety days this month, sixty the next. Once a judgment call gets made and written into the spec, the rest of the build should produce the same output from the same spec and the same data, every time. Part 2 gets into what actually makes that true.

One caveat: not every AI builder starts from zero. This is the contextual correctness column from earlier, and it's the one a platform-native agent can actually check by default. A platform-native agent like Databricks' Genie Code already sits inside Unity Catalog, with schemas, lineage, and governance in view, so it isn't reconstructing the data estate from nothing. That closes part of this problem, the part where an agent invents structure because nobody told it anything. It doesn't close the harder part, substantive correctness is still not for sale: Genie Code still can't know that "active" means ninety days for one metric and something else for another, because that's a business judgment, not a catalog fact. A context-aware agent changes what the spec has to spend its words on, less time describing a data estate it can already see, more time capturing the judgment a senior engineer used to supply.

Write the spec precisely, check the build against it, and the speed AI promises is real. Skip either step, and the outcome is the same dashboard from the opening, just built faster, and trusted by more people before anyone finds the gap.

Part 2 goes inside an actual build: what a real spec has to carry in detail, how the process runs when a pipeline spec and an intelligence-layer spec work together, and what a working proof of concept showed, including where the model held up under real pressure and where it didn't.

## Further reading

- [Data Contracts: Developing Production-Grade Pipelines at Scale](https://www.oreilly.com/library/view/data-contracts/9781098157623/), Chad Sanderson, Mark Freeman, and B.E. Schmidt: the current, book-length treatment of the data contracts movement referenced above.
- [GitHub Spec Kit](https://github.com/github/spec-kit): an open-source toolkit for spec-driven software development with AI coding agents, the general-software counterpart to the data-specific argument made here.
- [Genie Code](https://docs.databricks.com/aws/en/genie-code/), Databricks documentation: the official overview of the platform-native agent referenced in the closing section.
- [What's New in Genie Code at Data + AI Summit 2026](https://www.databricks.com/blog/whats-new-genie-code-data-ai-summit-2026), Databricks: covers Genie Ontology, the context layer behind the "closes part of the gap, not all of it" argument above.
