# Linguistic Patterns, Styles and Guidelines — Reference

**Source:** A. R. da Silva, "Linguistic Patterns, Styles, and Guidelines for Writing Requirements Specifications: Focus on Use Cases and Scenarios", *IEEE Access*, vol. 9, pp. 143506–143530, 2021. DOI: [10.1109/ACCESS.2021.3120004](https://doi.org/10.1109/ACCESS.2021.3120004). Licence: CC BY 4.0.

This file has two parts:
- **Part A** summarises the paper. Guideline IDs (G1–G38) and table contents follow the paper. The paper numbers the actor guidelines "1)–6)"; elsewhere it calls them G1–G6, and so does this file.
- **Part B** maps the paper onto KRUZE's own use case format and writing rules (`.claude/skills/create-use-case/references/writing-rules.md`). **When the paper and a KRUZE rule conflict, the KRUZE rule wins.**

---

# Part A — The Paper

## A.1 Core notions

| Notion | Definition (paper §III) |
|---|---|
| **Linguistic pattern** | A set of rules that defines the elements and the vocabulary to use in the sentences of requirements documents. It has two parts: **element rules** (attributes with name, type and multiplicity) and **vocabulary rules** (a limited set of literal terms used to categorise attributes). |
| **Linguistic style** | A concrete representation of a linguistic pattern: a template made of literal text fragments and `<attribute>` placeholders. One pattern can have many styles. |
| **Guideline** | A practical recommendation for writing the attributes of a pattern well. |
| **Controlled natural language (CNL)** | A constrained use of a natural language's grammar and a set of terms with defined semantics. CNLs are easier to read than formal languages and less ambiguous than free text. |

Multiplicity notation: none = exactly 1, `?` = 0..1, `+` = 1..*, `*` = 0..*.

The paper shows three styles:

| Style | Character | Pilot rating (24 subjects) |
|---|---|---|
| **CNL-A** | Compact: `<id> is a <type> <element> …` | Most natural, least complete |
| **CNL-B** | Verbose: `<element> <id> (<name>) is a <type>, … described as …` | Preferred by 50% as the best balance |
| **RSL** | Rigorous, with an Xtext grammar | Most precise and complete, least natural |

## A.2 Quality problems the patterns target

Natural-language requirements tend to be **inconsistent, incomplete, incorrect, ambiguous and vague**. The paper's motivating example (Fig. 1): "User-operator", "Operator" and "user-operator" all refer to one actor, and "sending an invoice to a customer" and "issue and send that invoice" may refer to one use case.

The paper's introduction also cites these general recommendations from the literature (Pohl, the INCOSE guide, Videira & Silva). In this file they are **L1–L5**:

| ID | Recommendation | Cited source |
|---|---|---|
| L1 | Use simple, clear and precise language. | [1], [11], [13], [14] |
| L2 | Follow a standardised format so all sentences are coherent and uniform. | same |
| L3 | Keep sentences simple and short, affirmative and in the **active voice** (INCOSE R2: *use active voice and identify the actor*). | same; INCOSE [11] |
| L4 | Limit the vocabulary, defining terms (INCOSE R4) and using them consistently. | same |
| L5 | Write scenario prose with **numbered steps and no "if" statements** (Cockburn's preferred format). | Cockburn [15] |

Other recommendations the paper cites: Lilly ("Name the use cases from the perspective of the actor's goals"; "Get agreement early … about the use of actor names (and other terms)") and Constantine & Lockwood (write *essential* use cases, avoiding concrete names like "John" and concrete UI like "click in the Ok button").

## A.3 Linguistic patterns

### lp1 — Actor
```
Actor :: <id:ID> <name:String>? <type:ActorType> <isA:Actor>? <stakeholder:Stakeholder>? <description:String>?
enum ActorType :: User | ExternalSystem
```
The **primary actor** has the use case's goal. A **supporting actor** gives or receives information or a service. Every actor is a stakeholder, but not every stakeholder is an actor: a "project sponsor" does not interact with the system.

### lp2 — UseCase
```
UseCase :: <id:ID> <name:String>? <type:UseCaseType> <stakeholder:Stakeholder>?
           <primaryActor:Actor> <supportingActors:Actor>* <triggeredBy:CatchEvent>?
           <dataEntity:DataEntityGeneric>? <precondition>? <postcondition>? <description>?
```

**Table 1 — Use case types** (S = single item, M = multiple items):

| Type | S/M | Description |
|---|---|---|
| EntityCreate | S | Create a data entity item |
| EntityRead | S | Read or consult a data entity item |
| EntityUpdate | S | Update or edit a data entity item |
| EntityDelete | S | Delete a data entity item |
| EntityReport | S | Produce a report based on a data entity item |
| EntityDashboard | S | Dashboard and data analysis based on a data entity item |
| EntitiesManage | M | Manage (including CRUD operations) data entity items |
| EntitiesBrowse | M | Browse and consult data entity items |
| EntitiesReport | M | Produce a report based on multiple items of a data entity |
| EntitiesDashboard | M | Dashboard and data analysis based on multiple items of a data entity |
| EntitiesInteropImport | M | Import data entity items from an external data source |
| EntitiesInteropExport | M | Export data entity items to an external data source |
| EntitiesInteropSync | M | Data synchronisation between data entity items and an external data source |
| EntitiesInteropSendMessage | M | Send a message that includes data entity items |

**Table 2 — Use case names: terms to use and to avoid**

| | Recommended | To avoid |
|---|---|---|
| **Verb** | Strong verbs: Create, Update, Merge, Calculate, Migrate, Send, Receive, Archive, Register, Activate, … | Weak or generic verbs: Make, Report, Use, Organize, Record, Find, Process, Maintain, List, … |
| **Noun** | Specific terms: Invoice, Payment, User account, Customer, … | Generic terms: Data, Paper, Report, System, Form, … |

### lp3 — Use case relationships
- **Include:** the behaviour of the included use case is added to the source use case at a given point.
- **Extension point:** a named point where other use cases may extend the source.
- **Extend:** a use case extends a target use case at one of its extension points.

### lp4 — Use case scenario
```
MainScenario :: <id>? <name> <description>? <steps:Step>*
Step         :: <id> ((<type:StepType> <actor>? <name>) | (<ucType:StepUCType> <usecase>)) <description>? <nextStep>? <scenarios>*
enum ScenarioType :: Main | Nested | Alternative | Exception
enum StepType     :: Actor | System | Event
enum StepUCType   :: Include | Extend | ExtendedBy
```
Semantics:
- **Nested:** the step breaks down into a sub-flow of sub-steps.
- **Alternative:** an optional or alternative behaviour of that step.
- **Exception:** what happens when something in that step goes wrong.

Step IDs show nesting and order (`1, 2, 2.2, 2.3a`). A step can also carry an action subtype, such as Actor-PrepareData, Actor-CallSystem, System-Execute, System-ReturnResult, Event-Timer or Event-Conditional.

## A.4 The 38 guidelines

### Actor (G1–G6)
| ID | Aspect | Guideline |
|---|---|---|
| G1 | Id | Identify the actor by a unique id so that use cases and generalisations can reference it. Some authors use the name as the id. |
| G2 | Name | Name an actor after the **user role** or the **external system** that interacts with the system. Use the common role names that already exist; **do not invent new ones**. |
| G3 | Name | **Do not use job titles** ("CEO", "CTO", "Project Sponsor"): they are stakeholders but usually do not interact with the system directly. Also avoid concrete people ("Mary", "John"). |
| G4 | IsA | Define generalisations between actors **only if needed**. |
| G5 | Stakeholder | If relevant, reference the stakeholder associated with the actor. |
| G6 | Description | If relevant, briefly describe the actor's main goals. |

### Use case (G7–G17)
| ID | Aspect | Guideline |
|---|---|---|
| G7 | Id | Identify the use case by a unique id. Whatever format you prefer, **apply it consistently**. |
| G8 | Name | Name a use case with a **verb–noun** structure that states the actor's goal ("Approve Invoices", "Register Payment"). |
| G9 | Name | Use concrete **strong verbs** instead of generalised weak ones, because **weak verbs may indicate uncertainty** (Table 2). |
| G10 | Name | Use **specific nouns** instead of generic terms. In general, strong nouns correspond to **data entity names** (Table 2). |
| G11 | Name | State the verb **from the primary actor's perspective**: "Receive Invoices not yet Paid", not "Send Invoices not yet Paid" (the system's perspective). |
| G12 | Type | Classify the use case using a classification scheme (Table 1). |
| G13 | Stakeholder | Optionally, refer to the primary stakeholder who defined the use case. |
| G14 | Actors / trigger | A use case has **one primary actor** and may have supporting actors. By default the primary actor triggers it; in that case **do not state a trigger event**, because it would be redundant. |
| G15 | Trigger | Define a **trigger event** for use cases that the system starts, not the primary actor: a timer ("Beginning of the Year") or a condition ("Invoices not paid after 30 days"). |
| G16 | Trigger | Use **a few trigger types consistently**, and only catch-event types, e.g. BPMN's **timer, conditional, signal, error**. |
| G17 | Data entity | If relevant, refer to the **data entity** the use case manipulates ("Invoice", "Customer", "Product"). |

### Use case relationships (G18–G22)
| ID | Aspect | Guideline |
|---|---|---|
| G18 | Extension points | Give extension points suggestive names (`xp_Create`, `xp_PrintInvoice`). |
| G19 | Include | Identify the included use case by its identifier. |
| G20 | Include | Do not worry about the order of includes; order belongs in the scenario steps. |
| G21 | Extend | Identify the extending use case by its unique id, plus one of the target's extension points. |
| G22 | Extend | Do not worry about the order of extends; order belongs in the scenario steps. |

### Use case scenarios (G23–G38)
| ID | Aspect | Guideline |
|---|---|---|
| G23 | Scenarios | Determine the **happy path** to the actor's goal first. Ignore other paths at first and write them later as alternative or exception sub-scenarios. |
| G24 | Scenarios | Write a scenario as a **sequence of steps ordered by time**. |
| G25 | Scenarios | If a sub-scenario is complex or important, document it as a **separate use case** linked by include (nested) or extend/extendedBy (alternative or exception). |
| G26 | Scenarios | Do not use both *extend* and *extendedBy*; pick one and **use it consistently**. |
| G27 | Scenario id | Do not give the main scenario an id, or use a special value such as "0". |
| G28 | Scenario id | Give each nested, alternative or exception scenario an id that **combines the step id with a letter** ("3.a", "Step2.b"). |
| G29 | Scenario name | Give every scenario a **short, readable name** ("SearchItems", "CustomerNotSelectedException"). |
| G30 | Scenario description | Optionally, describe the scenario briefly ("Customer not yet selected"). |
| G31 | Scenario type | State the scenario type: Main, Nested, Alternative or Exception. |
| G32 | Scenarios | Be **consistent in how scenarios begin and end**: always with a system step or always with an actor step. Pick one convention and use it throughout. |
| G33 | Rejoin | Do not use "Rejoin at" for nested scenarios, because control returns to the next top-level step automatically. |
| G34 | Rejoin | Use "Rejoin at" for alternative or exception scenarios **only when** control does not resume at the next top-level step. |
| G35 | Steps | State the **step type** explicitly (Actor, System or Event), and do not repeat "actor" or "system" in the sentence: "3. Actor: Select a customer". |
| G36 | Steps | Event-triggered use cases usually start with an **event step**. Use the event types from G16 and **one consistent format**, e.g. "starts on <date> [at <time>] [, repeat every <time-period>]". |
| G37 | Steps | Write each step as a **simple explanatory statement with no branching, exceptions or groups of steps**. Put those in nested, alternative or exception scenarios. |
| G38 | Steps | Write steps as **consistent, easy-to-read statements**. Steps must **not describe UI details, preliminary design, software architecture or quality requirements**. Avoid technical terms; define your own verbs and objects (Table 3) and use them consistently. |

**Table 3 — Step verbs and terms to use and to avoid**

| Class | Recommended | To avoid |
|---|---|---|
| Verbs triggered by the System | Show, Display, Create, Save, Ask, Check, Validate, Send, Export, Import, … | Show Web Page, Create X object, Save to Database, HTTP POST, GET API, … |
| Verbs triggered by an Actor | Fill, Select, Set, Enter, Confirm, Receive, … | Click, Double-click, Drag-Drop, Scroll, Tap, Swipe, Pinch, … |
| Verbs triggered by an Event | Start on, Trigger, Trigger when, … | Happen, Occur, … |
| UI-related terms | User interface, UI, Interaction Space, Dialog, UI Element, Action, Option, … | Page, Form, Dialog-box, Label, Text box, Check box, List box, Menu, Menu Option, Link, Button, … |

## A.5 User stories (from the paper's Appendix C)
The paper writes a very compact style as user stories, `As a <Actor> I want to <Verb Noun> [When <event>]`, reusing the actor names and use case names. It lists patterns for user stories as **future work**. Applying G2/G3 and G8–G11 to stories (Part B) is a KRUZE extension, not a recommendation from the paper.

---

# Part B — How the paper applies to KRUZE

KRUZE use cases follow `.claude/skills/create-use-case/templates/uc-template.md`, which has Metadata, a numbered Main Flow with "The [actor] …" and "The system …" steps, AF-N and EF-N, BR-N and PG-N. Do not ask authors to rewrite into CNL-A, CNL-B or RSL. Check the **linguistic substance** of each guideline within the KRUZE format.

Status values:
- **Apply:** check it and report violations.
- **Adapted:** check the KRUZE equivalent described.
- **Overridden:** a KRUZE rule contradicts part of the guideline. Follow the KRUZE rule and never report the paper's version.
- **Not adopted:** the KRUZE template has no such element. Do not report it.

| Guideline | Status | What to check in KRUZE |
|---|---|---|
| L1, L3 | Apply | Short, affirmative, active-voice sentences with an identified actor. Flag passive voice that hides who acts ("the invoice is sent"). |
| L2, L4 | Apply | One term per concept across the document set. Flag synonyms for the same actor or entity (the paper's Fig. 1 problem). |
| L5, G37 | Apply | No "if", "when … otherwise", "either … or" or multi-branch logic inside a Main Flow step. Move the branch to an AF or EF (Rule 21). |
| G1, G2 | Adapted | Actor names (Metadata, steps, story "As a …") must match a term in `docs/product/backlog/GLOSSARY.md` → *Actors*, or a Primary Actor value in the UC template. Do not invent roles. Flag the same role spelt differently ("Admin", "admin user", "administrator"). |
| G3 | Apply | No job titles ("business owner", "accountant", "manager") or real people as actors unless the glossary defines them as a role. |
| G4, G6 | Apply (Suggestion only) | Suggest a generalisation or glossary definition only when two roles visibly share behaviour, or a role is used but undefined. |
| G5, G13 | Not adopted | No stakeholder field in the template. |
| G7 | Adapted | IDs follow `UC-NNN`, `AF-N`, `EF-N`, `BR-N`, `PG-N` and story numbering. Flag inconsistent or duplicate IDs. |
| G8, G11 | Apply | The title is Verb + Noun from the **primary actor's** perspective (also Rule 2). |
| G9 | **Partly overridden** | Flag Table 2's **weak verbs** (Make, Report, Use, Organize, Find, Process, Maintain, List, also "Handle", "Do", "Manage …" used vaguely) in titles and steps. **Do not** recommend Create, Update or Validate: KRUZE Rule 5 forbids them (use Add, Change, Verify). "Record" is a weak verb in the paper but a KRUZE business verb; accept it in steps and flag it only in titles where a stronger goal verb exists. |
| G10, G17 | Adapted | Flag generic nouns (data, information, record, item, details, stuff, report, form, system) where a specific entity is meant. Specific nouns must match business entity names in `GLOSSARY.md` or the business meaning of tables in `shared/schema.ts`, e.g. "sale", "invoice", "roast", "stock movement". Never use table or column names in prose (Rule 3). |
| G12 | Not adopted | No UC type field. You may mention the Table 1 type in a Suggestion when it shows the UC mixes two capabilities (Rule 1). |
| G14 | Apply | Exactly one Primary Actor. Do not add a trigger sentence when the actor starts the flow. |
| G15, G16, G36 | Apply | If the system starts the flow (scheduled job, webhook, payment notification, threshold), the UC must state the trigger as a **timer**, **conditional**, **signal/message** or **error** event, in one consistent phrasing, e.g. "Every day at 02:00, …" or "When the payment provider reports a completed payment, …". |
| G18, G20, G22 | Not adopted | No extension points; ordering lives in the Main Flow. |
| G19, G21, G25, G26 | Adapted | Refer to other use cases by `UC-NNN` and name the step where the hand-off happens. Phrase cross-references consistently within a document. Suggest splitting out a long or complex AF as its own UC. |
| G23, G24 | Apply | One happy path, steps in time order (also Rule 21, Use-Case 3.0). |
| G27 | Apply | The Main Flow has no ID other than its heading. |
| G28, G33, G34 | **Overridden by Rules 19–20** | Every AF states its branch step and rejoin step or end; every EF states its detection step. The paper's "Rejoin only if needed" does not apply. Check that step numbers in "At step N" exist and match the text. |
| G29, G30 | Apply | Every AF and EF has a short readable name; the name describes the situation, not the system's reaction. |
| G31 | Adapted | AF = Alternative and EF = Exception. Flag a misclassified flow, e.g. an EF in which the actor still reaches the postcondition (also Rule 16). Nested sub-flows have no section in the template: describe them as one step plus a referenced UC, or flag them as G37. |
| G32 | Apply (cross-document) | Main flows within the same interface (Admin, POS, Storefront) begin consistently, normally with an actor step, and end consistently, normally with the system confirming the outcome. |
| G35 | Adapted | The sentence subject ("The admin", "The cashier", "The system") is the step type (Rule 10). Flag steps with no subject, two actors in one step, or a subject that is not a defined actor or "the system". |
| G38 + Table 3 | **Partly overridden** | Flag UI widgets and gestures (click, tap, button, link, menu, page, form, text box, check box, dropdown, modal, dialog, toast, tab, badge), design or architecture terms, and quality requirements inside steps. **Do not** recommend the paper's "Interaction Space", "Dialog", "Action" or "Option": Rule 4 replaces them with intent phrasing ("The admin indicates they want to …", "The system asks for …", "The system shows …"). Table 3's actor verbs (Fill, Select, Set, Enter, Confirm, Receive) and event verbs are fine. Its system verbs Create and Validate are replaced by Add/Record and Verify/Check (Rule 5). |

## B.1 Applying the paper to user stories
Stories live in `docs/product/backlog/epic-*/story-*.md`. Apply only the guidelines below. INVEST and Cohn's principles belong to `/review-stories`.

| Part of the story | Guidelines |
|---|---|
| **As a** … | G2, G3: a glossary role or the actor of the parent UC. No job titles. |
| **I want to** … | G8–G11: a strong verb, a specific noun and the actor's perspective; L3 active voice. |
| **So that** … | L1: one concrete benefit, no vague value ("better experience", "more efficient"). |
| Acceptance criteria | L4, G10: entity and actor names consistent with the UC and glossary; G37: one condition per criterion. UI terms in criteria are **Suggestions** only, because stories may carry presentation detail that use cases must not. |

## B.2 Vague-term list (L1)
Flag these in UCs and story statements when they leave the meaning open: *appropriate, relevant, some, several, various, etc., and so on, as needed, if necessary, normally, usually, user-friendly, fast, efficient, easy, flexible, robust, basic statistics, relevant information, handle, manage (without object detail), process (as a verb), support (as a verb)*. Suggest a specific replacement every time. This list is a KRUZE addition in the spirit of L1 and Rule 11, not a list from the paper.
