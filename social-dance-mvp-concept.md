# Social Dance Lab — MVP Concept

**Status:** Concept specification, v0.2  
**Product type:** Browser-based, reproducible social experiment platform  
**Domain:** Social partner dancing across traditions and event formats

## 1. Purpose

Social Dance Lab investigates how invitation conventions, spatial arrangements, population characteristics, and individual behavior influence **who dances with whom**, participation rates, and inequality of dance opportunities.

The product is an **agent-based simulation and analysis tool**, not an animated dance-floor game. A user configures a synthetic social dance event, runs a deterministic simulation, and examines exactly two analytical perspectives: **Individual** and **Population**.

No single dance tradition is the default model of human behavior. Finnish *lavatanssit* provides useful initial example presets, but the simulation must also accommodate other styles and event formats. Results explore consequences of stated assumptions, not empirical predictions unless the model is calibrated and validated.

## 2. Core questions

- How do different invitation rules affect participation and inequality?
- Who invites, who receives invitations, who dances, and who remains without a partner?
- How do invitation areas, resting areas, and social norms affect approaches and declines?
- How do persona composition, age, perceived attractiveness, dance skill, confidence, and preferences shape outcomes?
- What is the experience of one focal dancer compared with peers and the population?
- How robust are the results across random seeds and rule changes?

## 3. Product principles

1. **One experiment, two perspectives.** Both analytical views use the same event history.
2. **Tradition-neutral engine.** Dance styles supply configurable event, invitation, role, spatial, and etiquette presets; no style-specific rules are hard-coded into decisions.
3. **Reproducible by URL.** All result-affecting settings, model version, and an explicitly editable seed are shareable.
4. **Explainable decisions.** Recorded factors explain why simulated agents wait, invite, accept, or decline.
5. **Transparent assumptions.** Persona weights and social norms are hypothetical, configurable model parameters.
6. **Analysis, not animation.** Charts, distributions, group comparisons, and event logs are the product.
7. **Minimal infrastructure.** MVP can run in the browser without accounts or server-side experiment storage.

## 4. Experiment configuration

### 4.1 Event

- Number of participants (`n`)
- Duration, start time, and time zone if clock schedules are used
- **Dance opportunity unit:** a configurable dance set (one song, two songs, or another defined duration); time advances in discrete sets
- Dance/event preset, or custom configuration
- Invitation schedule and rules
- Venue zones and associated conventions
- Explicit, editable, randomizable integer seed (`seed`)
- Versioned simulation model and preset definitions (`v`, preset version)

### 4.2 Generic invitation rules

Model invitations using independent configurable dimensions:

- **Who may initiate:** any participant, selected invitation categories, selected dance roles, or groups according to a time schedule
- **Who may receive an invitation:** eligible/compatible partners subject to role or event constraints
- **When:** intervals in the event timeline (including alternating periods and special windows)
- **Where:** designated invitation zones and other permitted locations, each with approach/acceptance modifiers
- **How:** direct approach in the MVP; potential future protocols such as nonverbal signaling can be represented later
- **Social norms:** expected versus technically permitted approaches, and contextual differences in likelihood and perceived social cost of declining

Separate **eligibility** from **behavior**: being permitted to invite does not mean the agent will do so. Similarly, a socially expected convention need not be an absolute ban. The MVP should offer strict/soft rule settings where meaningful.

Represent time-dependent rules as a sequence of intervals with configurable eligible initiators, eligible recipients, location conventions, and rule strength. This avoids hard-coding named customs.

### 4.3 Example presets, not core assumptions

Presets are saved starting configurations; every relevant parameter may be overridden.

- **Unrestricted social:** any eligible participant may invite throughout the event.
- **Alternating invitation groups:** groups alternate initiation privileges on a configurable schedule.
- **Majority/minority invitation windows:** one group normally initiates, with a shorter window assigned to another group.
- **Finnish lavatanssit:** examples of *sekahaku*, *vuorohaku*, and *miestenhaku + naistentunti* (60/90 minutes), along with *miestenrivi*, *naistenrivi*, and *hakualue* conventions.

Other contexts such as salsa/bachata socials, tango milongas, and swing events should be representable by custom rules, but **not** claimed to share uniform etiquette. Tradition-specific behaviors (e.g., nonverbal invitation protocols) are outside the initial engine unless separately specified and implemented.

### 4.4 Configurable zones

Use **discrete semantic zones**, not continuous physical coordinates. The engine recognizes configurable zones with properties, rather than Finnish-specific zone identifiers:

- `invitation_area_a`, `invitation_area_b` (optional designated areas)
- `social_area` / `rest_area`
- `dance_floor` (occupied during accepted dance sets)
- Optional `other` zone

A preset may label these as *miestenrivi*, *naistenrivi*, *hakualue*, etc. Another preset may use only an open social area. A dancer can be invited outside a designated invitation area when the rules permit it; location may affect approach probability, acceptance probability, and the perceived acceptability of declining.

**Location is not willingness.** Entering or leaving a zone is a modeled decision. Standing in an invitation zone never forces acceptance; sitting outside it does not inherently prohibit invitations.

### 4.5 Population and roles

- Population size, proportions of defined participant/invitation categories, and distributions of dancer attributes
- Configurable dance-role compatibility (e.g., leader/follower, either-role, or unconstrained); **dance role is not inherently gender**
- Invitation categories may reflect the conventions of a particular preset; participant identity, gender, invitation eligibility, and dance role remain distinct fields
- Editable proportions of personas, normalized to 100%
- Distributions for age, perceived attractiveness, skill, confidence, risk aversion, selectiveness, sociability, familiarity preference, and willingness/energy
- Optional focal individual with specified profile for Individual view, included in the same population simulation

MVP presets may simplify a tradition's categories and pairing assumptions, but those simplifications must be visible and replaceable.

### 4.6 Personas and preferences

Personas are behavioral archetypes, not fixed identities. Each persona includes:

- Name and description
- Attribute defaults/distributions
- Weights for common decision factors (skill compatibility, age compatibility, appearance, familiarity, social comfort, rejection risk, etc.)
- Initiative, willingness, and selectiveness tendencies

Initial examples: Enthusiast, Cautious Newcomer, Technique Purist, Social Butterfly, Selective Expert, Quiet Regular. They are generic and editable; people vary within archetypes.

**Age and perceived physical attractiveness** are separate from persona selection. Appearance is a subjective perception in the model, not an objective ranking. Effects of age and appearance depend on the evaluator's preferences and experimentally chosen weights.

## 5. Agent model and simulation

### 5.1 Agent state

Each participant has:

- Stable ID and configurable invitation category/identities and dance-role capabilities
- Age, persona, and individual attribute values
- Partner preferences and perception weights
- Current zone, available/busy/resting state, willingness/energy
- Limited interaction history (invitations, accepted/declined dances, prior partners)

Actual attributes and other agents' perceptions can diverge; simple deterministic observer-specific variation suffices for the MVP.

### 5.2 Per-set decision sequence

1. Resolve active event rule interval and the current set duration.
2. Each participant decides whether to seek a dance, where to wait, or whether to rest.
3. Eligible, available agents decide whether to initiate.
4. Initiators evaluate feasible partners by perceived compatibility, location, familiarity, and context.
5. Invitations are made and recipients decide whether to accept or decline.
6. Accepted partners become occupied for the set; declines and nonparticipation are logged.
7. Update agent history and aggregate statistics.

Decisions are probabilistic but deterministic given the model version, full configuration, and seed. Ensure stable agent ordering and conflict resolution; no participant can join two accepted dance pairs in the same set. For the first release, default to at most one invitation attempt by an initiator per set (configurable later).

### 5.3 Explainability and normative effects

Record factor contributions when meaningful: eligibility, opportunity, zone choice, candidate evaluation, invite/accept/decline, and dance completion. Separate:

- **Probability of approaching** someone
- **Probability of accepting** the invitation
- **Perceived social cost/acceptability of declining** under the convention

Normative costs can influence decisions without being represented as universal moral judgments. A high decline probability outside an invitation area is an adjustable scenario assumption, not a universal truth.

## 6. Exactly two analytical views

### 6.1 Individual perspective

Choose an actual simulated participant, or specify a focal profile embedded in the population. Show:

- Completed dance sets and invitations sent/received
- Incoming/outgoing acceptances and declines
- Participation over time; available sets without dancing
- Time in invitation versus rest/social zones (where applicable)
- Unique/repeat partners, if tracked
- Population percentile and comparisons to similar profiles where sample size permits
- Chronological decision/event log explaining outcomes

Changing the selected person or switching views **does not regenerate** the experiment.

### 6.2 Population perspective

Show:

- Total couple dance sets and mean/median sets per participant
- **Dance-count Gini coefficient**
- **Invitations-received Gini coefficient**
- Dance-count distribution and percentage with zero dances
- Invitations sent/received and acceptance/decline rates
- Breakdowns by persona, invitation category, dance role, age, perceived attractiveness, skill, and zone behavior where applicable
- Dances per available set as a complement to raw participation

For nonnegative counts `x[1..n]`:

`G = sum_i sum_j |x_i - x_j| / (2 * n * sum_i x_i)`

If all counts are zero, report Gini as **undefined**. Distinguish completed dances from received invitations, and label subgroup comparisons clearly.

## 7. Reproducibility and URL sharing

The experiment URL must encode all result-affecting settings, either directly as readable parameters or as a versioned serialized configuration. Include:

- Engine/model version and preset definition version
- **Explicit editable seed** (one user-facing integer)
- Event duration and dance set length
- Invitation categories, eligible pairings, time-based schedules, and rule strength
- Zone definitions, invitation modifiers, and social norms
- Population size, category distributions, persona mix, attribute distributions, and decision weights
- Focal profile when customized

Illustrative shorthand for a preset-backed URL:

```text
/sim?v=1&preset=lavatanssit-v1&seed=42817&n=100&system=sekahaku&personas=20,20,15,15,15,15
```

A general custom event may use a canonical encoded configuration (`?config=...`) when its complete settings exceed practical readable query parameters. A versioned preset is valid only if its default definitions remain immutable for that version.

The same **complete configuration + seed + model version** must produce exactly the same population, event log, and statistics. Validate malformed URL parameters and provide **Copy experiment link**. Optional view parameters (e.g. `view=individual&dancer=42`) affect presentation only.

The seed stays constant when the user edits another parameter; a **Randomize seed** control generates a different experiment. Internally, independent deterministic random streams should stabilize population profiles across rule-only comparisons.

## 8. Experiment workflow

1. Load a preset or custom configuration from a URL.
2. Edit invitation rules, zones, population, personas, preferences, and seed.
3. Run the deterministic simulation.
4. Inspect either Individual or Population statistics.
5. Change one parameter while keeping the seed to compare conditions; share either URL.
6. Optionally repeat across seeds to investigate variability.

Comparison is part of the experimental workflow but **not a third view**. MVP comparison may be manual (two links) or a small baseline-to-current delta within the Population view.

## 9. MVP boundaries

**Included:** generic configurable invitation rule engine; event presets including Finnish lavatanssit; optional zones; configurable participation categories and compatible pairings; persona mixes and individual attributes; age and perceived-attractiveness influences; seeded decisions and event logs; Gini and descriptive analytics; two views; shareable URLs.

**Deferred:** animated dance floor, continuous spatial movement, sophisticated nonverbal invitation protocols, rich music-specific rules, detailed social networks, complex emotional evolution, accounts, server storage, empirical calibration, and a standalone comparison screen.

## 10. Acceptance criteria

1. Users can edit and randomize the seed.
2. A full configuration can be loaded from and shared through a URL.
3. Same seed + configuration + model version yields identical agents, history, and metrics.
4. Users can choose a generic event setup or a tradition-specific preset and override the preset.
5. Invitation eligibility, recipient eligibility, time intervals, and location effects are represented independently of dance tradition.
6. The engine works **without** gendered waiting rows or separate invitation categories (e.g., unrestricted invitations in one general zone).
7. The Finnish lavatanssit preset supports sekahaku, vuorohaku, and miestenhaku plus naistentunti, including configurable hakualue effects.
8. Persona proportions, age, attractiveness, and other decision factors are configurable.
9. No accepted set double-books an agent; accepted and declined invitations are logged.
10. Individual and Population views are consistent with the same underlying run.
11. Population statistics include dance-count Gini, invitations-received Gini, zero-dance percentage, and a defined zero-total treatment.
12. Results explicitly identify behavioral assumptions as simulation choices rather than observed facts.

## 11. Open design decisions

- Minimal universal schema for invitation groups, roles, and compatibility, including how best to represent complex multi-role dances.
- Which norms are hard constraints versus soft probability modifiers.
- Minimum useful set of generic zones and whether a preset can define additional zones.
- Whether all sets are pairwise dances in v0.1 (recommended yes), and how durations and breaks are modeled.
- Which dance-style presets are actually shipped versus merely representable by the configuration schema.
- Whether multi-seed uncertainty estimates fit the first release or the following iteration.
- Canonical URL encoding and schema-migration policy.

**MVP definition:** A generic, reproducible, linkable social dance experiment platform that simulates configurable invitation conventions and exposes the experience of one individual and participation outcomes across a population, with Finnish lavatanssit as an example preset—not a baked-in rule set.
