# Media_AI

## Abstract

Media texts do more than transmit factual information. Through selection, association, emphasis, sequencing, emotional language and the construction of social groups, a text may progressively transform how information is interpreted. Conventional approaches to sentiment analysis, emotion classification and media-bias detection frequently reduce these processes to categorical labels or aggregate scores. Such representations can obscure how emotional and social effects develop through a text, how several emotions coexist, how social groups are constructed and related, and how factual and emotional information interact.

**Media AI** investigates whether these processes can be represented computationally through an interpretable multidimensional architecture built upon a pretrained language model. The pretrained model provides the contextual semantic substrate rather than being treated as the principal object of the study. Media AI introduces additional **transformative, recurrent and confluent components** intended to model how textual information changes emotional and social representations as an article develops.

The architecture is organised around four distinct computational questions:

**Backbone — What does the text mean?**

The pretrained backbone processes the complete observed text and produces contextual semantic representations. Its internal attention coefficients are not interpreted as Media AI emotional weights.

**Transformative layer — What emotional change does the new information produce?**

The transformative component uses contextual backbone representations to estimate signed changes across a multidimensional emotional state. It processes all tokens appearing in the observed text rather than relying on a predefined vocabulary of emotional terms. Emotional contributions are therefore calculated contextually and on demand. The same token may have negligible emotional effect in one context and substantial transformative effect in another.

**Recurrent layer — What emotional weight is carried forward?**

The recurrent component determines how much of the resulting emotional state is transmitted into subsequent textual context. It models reinforcement, persistence, attenuation and reversal rather than performing a second emotion classification.

**Confluent layer — Around whom or what does the emotional state converge?**

The confluent component relates emotional and social states to persons, institutions, concepts, events and potentially overlapping social groups. It provides the relational structure required to investigate identification, distancing, inclusion, exclusion, solidarity, hostility, integration and polarisation.

Media AI distinguishes **emotional state**, **emotional transformation** and **emotional weight carried forward**. If the backbone representation of textual unit (t) is denoted by (H_t), the transformative component estimates a multidimensional signed change:

[
\Delta E_t = T(H_t, R_{t-1})
]

where (R_{t-1}) represents the emotional weight carried from preceding context. The immediate emotional state is then:

[
E_t^{*}=R_{t-1}+\Delta E_t
]

and the recurrent component estimates the state transmitted further:

[
R_t=\mathcal{R}(E_t^{*},\Delta E_t,H_t)
]

This separation allows Media AI to examine not only whether an emotion is detectable, but how it is introduced, strengthened, reduced, reversed and carried through the text.

The psychological representation is designed as a multidimensional, multi-label emotional space rather than a single positive-versus-negative sentiment score. Multiple emotional states may coexist, and positive and negative processes are treated symmetrically. Fear and reassurance, anger and compassion, tension and relief, hostility and solidarity, and polarisation and integration are therefore represented as substantive directions rather than assuming that framing is inherently negative.

The architecture also distinguishes **reported emotional content** from **model-estimated emotional transformation**. A text may report that an individual or population is afraid without necessarily introducing fear-inducing framing. Conversely, information containing no explicit emotional terminology may produce a substantial emotional transformation because of the context established by preceding text.

Media AI additionally distinguishes **factological weight** from **emotional weight**. Factological weight represents the contribution of externally assessable informational content, including explicit claims, observations, quantities, evidence, attribution, certainty and speculation. It is not interpreted as the probability that a statement is true. Emotional weight represents the strength and persistence of multidimensional emotional information and emotional transformation. Their relationship can therefore be examined across textual sequences without treating either dimension normatively.

Interpretability is treated as an architectural requirement. Aggregate emotional or social conclusions must remain decomposable into their underlying dimensions and traceable through article, paragraph, sentence and token levels. Colour-based inspection is planned as a visual projection of these numerical representations rather than as independent evidence.

The empirical study follows the predefined sequence:

**Research Question → Hypotheses → Method → Experiment → Evidence → Interpretation**

This structure is intended to separate assumptions made during model construction from observations generated by the experiments and from conclusions drawn afterwards.

The pilot corpus uses randomly sampled full-text English-language media articles. Article acquisition is selective only according to technical usability and does not filter by topic, sentiment, publisher, political orientation or anticipated framing. Original provider records are preserved separately from derived analytical datasets to maintain source traceability.

The proposed transformative, recurrent and confluent architecture will be evaluated against simpler pretrained-model baselines through held-out testing, component ablation, directional-symmetry analysis, controlled counterfactuals and independent supervision. Model outputs are treated throughout as estimates of operationally defined constructs rather than established psychological, social or factual ground truth.

The central objective of Media AI is therefore not simply to classify emotion or assign a media-bias label, but to investigate whether the **transformation of interpretation through language can itself be represented as a transparent, sequential, multidimensional and relational computational process**.

---

# Research Framework

## Research Question

The central research question is:

> **Can factual information, emotional transformation and social framing in media text be computationally represented as distinguishable but interacting processes while preserving their development across tokens, sentences, paragraphs, social groups and the article as a whole?**

A related architectural question is whether explicitly transformative, recurrent and confluent components capture information that is not adequately represented by a conventional pretrained language model alone.

The project deliberately avoids defining the research problem as the classification of articles as biased, unbiased, manipulative, trustworthy or untrustworthy. Such categories would introduce normative conclusions before the underlying constructs have been operationalised and validated.

Instead, Media AI investigates how contextual information changes model-estimated emotional and social states and whether these changes can be represented, traced and experimentally evaluated.

## Hypotheses

The pilot is organised around several falsifiable hypotheses.

### H1 — Representational Separation

Factological, emotional and social information can be represented as distinguishable but interacting dimensions rather than being collapsed into a single sentiment or framing score.

### H2 — Contextual Emotional Transformation

A contextual transformative component can identify emotional state changes produced by both explicit emotional language and indirectly consequential information.

This includes cases where individual words or sentences contain little overt emotional vocabulary but produce substantial emotional change because of preceding context.

### H3 — Sequential Dependence

A recurrent representation improves the modelling of emotional effects that accumulate, persist, attenuate or reverse across textual sequences.

### H4 — Emotional Carry-Forward

The recurrent component can estimate how much emotional weight resulting from one textual unit is carried into subsequent context rather than treating every sentence or paragraph as an independent observation.

### H5 — Group Confluence

Explicit modelling of dynamically constructed and potentially overlapping social groups improves representation of identification, distancing and relationships between groups.

### H6 — Directional Symmetry

The architecture can represent positive and negative emotional and social transformations without treating one direction as the default.

Examples include:

* fear and reassurance;
* anger and compassion;
* tension and relief;
* hostility and solidarity;
* exclusion and inclusion;
* polarisation and integration.

### H7 — Hierarchical Attribution

Article-level model estimates can be meaningfully traced to paragraphs, sentences and contextual tokens contributing to the result.

### H8 — Architectural Contribution

The combined transformative, recurrent and confluent architecture provides measurable improvements over simpler pretrained-model and projection-based baselines on predefined evaluation criteria.

The hypotheses may be supported, partially supported or not supported. Increased architectural complexity is justified only where experimental evidence demonstrates a measurable contribution.

---

# Methodological Principles

## Complete-Text Processing

Media AI processes the complete observed text rather than preselecting words assumed to carry emotional content.

For tokens:

[
X=(x_1,x_2,\ldots,x_n)
]

the pretrained backbone produces contextual representations:

[
H=(h_1,h_2,\ldots,h_n)
]

These representations depend on context. Media AI therefore does not maintain a predefined emotional table for the backbone vocabulary.

The persistent learned object is the projection or transformation function. Emotional contributions are calculated dynamically only for the contextual token representations occurring in the text currently being analysed.

Accordingly:

[
\boxed{\text{all observed tokens are processed}}
]

while:

[
\boxed{\text{emotional weights are generated contextually on demand}}
]

A token such as *open* may have almost no emotional consequence in one passage and contribute substantially to fear or tension in another.

## Emotional State and Transformation

The project distinguishes three quantities:

[
\text{emotional state}
\neq
\text{emotional transformation}
\neq
\text{model confidence}
]

The transformative component estimates signed emotional change rather than merely predicting absolute emotional intensity.

A transformation may therefore be positive or negative for a given emotional dimension:

[
\Delta E_{t,e}\in[-1,+1]
]

where a positive value increases the relevant emotional state and a negative value reduces it.

This permits the model to represent the emergence and resolution of emotional states rather than only their presence.

## Transformer Attention Is Not Emotional Weight

Attention coefficients produced by the pretrained backbone contribute to contextual language representation.

They are not interpreted as emotional weight.

Media AI emotional representations are produced by additional trained mappings and subsequently evaluated through attribution, supervision and experimental comparison.

This distinction prevents internal Transformer attention from being relabelled as an explanation of psychological effect.

## Factological and Emotional Weight

Factological and emotional weight are maintained separately.

Factological weight concerns the relative informational contribution of externally assessable claims, observations, quantities, evidence, attribution, certainty and speculation.

Emotional weight concerns multidimensional emotional content and transformation.

Neither quantity is automatically normative.

A passage with high emotional weight is not necessarily manipulative, and a passage with high factological weight is not necessarily true.

## No Aggregate Without Decomposition

Media AI should not expose a broad psychological or social score without allowing it to be decomposed into the dimensions from which it was derived.

An emotional result should therefore remain inspectable through the emotional spectrum.

A social result should remain inspectable through the represented groups, relational dimensions and relevant textual evidence.

## Model Output Is Not Ground Truth

Throughout the research:

[
\boxed{\text{model output}\neq\text{ground truth}}
]

Terms such as *estimated*, *model-assigned*, *model-detected* and *according to the operational definition* are preferred where appropriate.

Claims concerning real psychological or social effects require independent validation beyond the existence of a model score.

---

# Notebook Architecture

The pilot is organised into five reproducible Google Colab notebooks.

Each notebook is intended to remain relatively compact, with approximately ten substantial code blocks supported by detailed Markdown explanations.

The Markdown documentation is part of the research methodology rather than merely an explanation of the Python implementation.

Each relevant section explains:

* purpose;
* methodological reasoning;
* inputs;
* outputs;
* assumptions;
* safeguards;
* interpretation;
* limitations.

The intended notebook sequence is:

```text id="j9v8pn"
01 — Data Acquisition and Source Traceability
02 — Data Sanitisation and Preparation
03 — Supervision and Annotation
04 — Transformative, Recurrent and Confluent Architecture
05 — Evaluation, Inspection and Interpretation
```

---

# Notebook 01 — Data Acquisition and Source Traceability

## Objective

Notebook 01 establishes the empirical input to the Media AI research pipeline.

Its responsibility is limited to:

* establishing a reproducible computational environment;
* configuring persistent research storage;
* isolating API credentials;
* defining acquisition safeguards;
* maintaining persistent API usage accounting;
* specifying article eligibility rules before acquisition;
* preserving original provider records;
* validating the external API;
* performing controlled candidate acquisition;
* reviewing technical eligibility and exact duplicates;
* creating a reproducible candidate ordering;
* generating a persistent handover manifest for Notebook 02.

Notebook 01 deliberately does **not** perform semantic sanitisation, emotion analysis, tensor construction, supervision or model inference.

This maintains a clear boundary between acquired source material and subsequent analytical transformation.

---

## Environment and Reproducibility

The notebook establishes project metadata, execution timestamps and a fixed random seed before external data are acquired.

The project currently uses:

```python id="wxyak5"
RANDOM_SEED = 42
```

The fixed seed supports reproducible random ordering and subsequent experiments where deterministic execution is available.

Runtime metadata record the project version, Python version, principal package versions, platform information and UTC execution time.

The environment block performs no external API request.

---

## Restricted Persistent Storage

Google Colab runtimes are temporary, so persistent storage is required for source records and experiment artefacts.

Media AI uses the Google Drive API rather than mounting the user's complete Google Drive filesystem.

The notebook creates a dedicated project structure including:

```text id="fn2lhx"
Media_AI/
├── data/
│   ├── raw_api/
│   ├── full_text/
│   ├── cleaned/
│   ├── segmented/
│   ├── annotated/
│   ├── gold/
│   └── demo/
├── models/
│   ├── checkpoints/
│   └── adapters/
├── outputs/
│   ├── metrics/
│   ├── colours/
│   └── explanations/
└── logs/
```

Folder creation is idempotent. Re-executing the storage block reuses existing project folders rather than deliberately creating duplicate structures.

No Google Drive filesystem mount is required.

---

## API Credential Isolation

The pilot currently uses the GNews API.

The API credential is stored through Google Colab Secrets under:

```text id="6l6iop"
GNEWS_API_KEY
```

The credential is not embedded in notebook cells, printed in notebook outputs or written to research manifests.

Opening the notebook from GitHub therefore does not expose the original researcher's API credential.

Researchers who wish to perform new acquisition provide their own API key.

---

## Acquisition Master Switch

New article acquisition is disabled by default:

```python id="u8q8oq"
DOWNLOAD_NEW_DATA = False
```

The notebook must be deliberately switched into acquisition mode before candidate data can be requested.

This protects against accidental API traffic caused by reopening the notebook or executing all cells.

After live acquisition, the recommended state is to return the switch to `False`.

---

## API Usage Safeguards

The pilot defines independent research-side usage limits rather than relying solely on provider limits.

At the current pilot configuration:

```text id="axncyf"
Approved requests/day       1,000
Research requests/day         200

Approved articles/request      25
Research articles/request       5

Research articles/day         200

Usage fraction                20%
```

These settings are intentionally conservative.

The notebook checks limits before each live request.

---

## Persistent API Usage Ledger

API accounting is stored persistently rather than only in temporary Colab memory.

The usage ledger records:

* UTC date;
* requests used;
* articles requested;
* articles returned;
* last update timestamp.

Persisting the ledger prevents a Colab runtime restart from silently resetting research-side usage counters.

Every completed connection or acquisition request is entered into the ledger.

---

## Content-Neutral Sampling Design

Article-selection criteria are specified before observing the research corpus.

The governing principle is:

> **Random by content; selective only by technical usability.**

The acquisition process does not select articles according to:

```text id="8qle5f"
Topic                         False
Sentiment                     False
Publisher preference          False
Political orientation         False
Expected framing              False
```

The purpose is to reduce researcher-induced selection effects and avoid constructing the corpus around phenomena that the proposed architecture is expected to discover.

---

## Technical Eligibility

The pilot technical criteria are:

```text id="jfarqv"
Target language           English
Minimum article length    300 words
Maximum article length    None
Full text                 Required
Source information        Required
Exact duplicate           Excluded
```

The minimum-length criterion is architectural rather than editorial.

Media AI is intended to examine transformations across sequential context. Very short texts provide limited information for analysing how emotional and social states develop through an article.

No maximum article length is imposed during acquisition. Model context-window constraints are handled later during preparation.

---

## Exact and Near-Duplicate Separation

Notebook 01 detects exact duplicates through deterministic textual fingerprints based on conservatively normalised content.

The fingerprint is used only for exact identity.

It does not identify:

* syndicated articles with minor changes;
* paraphrases;
* semantically similar reports;
* different reports of the same event.

Near-duplicate detection is deliberately deferred to Notebook 02, where similarity methods can be evaluated explicitly.

---

## Original Source Records

The complete provider record is preserved separately from derived research data.

The observed GNews article schema currently contains:

```text id="8qxq4m"
content
description
id
image
lang
publishedAt
source
title
url
```

Media AI maps selected fields into a standard source-record schema while retaining the complete provider payload.

The source record contains information such as:

* internal Media AI record identifier;
* source-record schema version;
* provider name;
* provider article identifier;
* acquisition method;
* UTC acquisition timestamp;
* publication timestamp;
* source metadata;
* source URL;
* article language;
* title;
* word count;
* textual fingerprint;
* technical eligibility result;
* acquisition context;
* preserved provider payload.

Analytical outputs are excluded.

The source record therefore does not contain:

* emotional weight;
* factological weight;
* emotion classifications;
* transformative-state values;
* recurrent-state values;
* confluent representations;
* Social Polarisation Distance;
* supervisory labels;
* model confidence.

Those belong to subsequently derived datasets.

---

## Source Traceability

The separation between source and derived data establishes the research chain:

```text id="rpfx58"
External Source
      ↓
Original Acquisition Record
      ↓
Derived Research Dataset
      ↓
Model Input
      ↓
Model Output
      ↓
Interpretation
```

A later model conclusion should therefore remain traceable to the textual evidence from which it originated.

The original provider record is not overwritten by later preprocessing.

---

## Controlled API Validation

Before acquiring research candidates, Notebook 01 performs a minimal controlled connection test.

The test requires an additional explicit switch and requests only one article.

The first live GNews validation returned:

```text id="6an4xv"
Test status               SUCCESS
Articles returned         1
Top-level response        articles, totalArticles
```

The observed article fields were:

```text id="qyv8pd"
content
description
id
image
lang
publishedAt
source
title
url
```

The test request was recorded in the persistent usage ledger.

The connection-test article was used for integration validation rather than treated as part of the research corpus.

---

## Controlled Candidate Acquisition

The first controlled candidate acquisition requested five articles, corresponding to the configured per-request research limit.

The initial acquisition produced:

```text id="ztadzn"
Articles requested         5
Articles returned          5
Technically eligible       3
Technically ineligible     2
Stored source records      5
```

All five returned provider records were preserved.

Technical ineligibility does not result in deletion of the original source record.

This allows the exclusion process itself to remain auditable.

---

## Persistent Original-Source Storage

Each acquisition batch is stored as a timestamped research object containing:

* acquisition batch identifier;
* UTC acquisition timestamp;
* provider;
* source-record schema version;
* complete raw API response;
* canonical Media AI source records.

Existing acquisition batches are not deliberately overwritten.

This permits later reconstruction of the input to the eligibility and preparation stages.

---

## Eligibility Review

The first acquisition batch produced:

```text id="bntv43"
Source records reviewed     5
Technical exclusions        2
Exact duplicates            0
Eligible unique records     3
Sampling seed               42
```

Exact duplicate control was applied after technical eligibility.

The resulting eligible records were placed into a reproducible random order using the fixed project seed.

Near-duplicate analysis remains deferred to Notebook 02.

---

## First Acquisition Audit

At completion of the first acquisition cycle, the persistent usage ledger reported:

```text id="d1azfe"
API requests recorded       2
Articles requested          6
Articles returned           6
```

The totals reconcile as:

```text id="4lb8m9"
1 controlled connection-test request
+
1 five-article candidate acquisition request
=
2 API requests

1 connection-test article
+
5 candidate articles
=
6 articles requested and returned
```

This accounting confirms that connection-test traffic and research acquisition traffic are both represented in the persistent usage record.

---

## Notebook 01 Handover Manifest

Notebook 01 produces:

```text id="d10fq1"
notebook_01_handover_manifest.json
```

The manifest records the information required for Notebook 02 to identify and verify its input without relying on temporary Colab state.

It contains:

* project metadata;
* source provider;
* source-record schema version;
* acquisition batch identifier;
* acquisition batch storage reference;
* reviewed candidate file reference;
* source-record counts;
* technical exclusion count;
* exact-duplicate count;
* candidate-record count;
* language criterion;
* article-length criterion;
* sampling seed;
* content-neutral selection settings;
* persistent API usage summary;
* relevant research storage references;
* planned Notebook 02 processing stage.

The manifest explicitly records that API credentials are not contained in the handover data.

---

## Notebook 01 Result

Notebook 01 establishes a complete reproducible acquisition path:

```text id="nnmhjr"
Research Environment
        ↓
Restricted Persistent Storage
        ↓
Credential Isolation
        ↓
API Usage Safeguards
        ↓
Content-Neutral Eligibility Rules
        ↓
Controlled Provider Validation
        ↓
Candidate Acquisition
        ↓
Original Source Preservation
        ↓
Technical Eligibility Review
        ↓
Exact-Duplicate Control
        ↓
Reproducible Random Ordering
        ↓
Acquisition Audit
        ↓
Notebook 02 Handover
```

The first controlled pilot run resulted in three technically eligible, unique candidate articles from five acquired research records.

No semantic sanitisation, emotional analysis, framing classification or model inference is performed during Notebook 01.

The original source records remain unchanged.

---

## Handover to Notebook 02

Notebook 02 begins from the persistent acquisition manifest rather than making new API requests.

Its responsibility is to create derived analytical data while preserving the original records established in Notebook 01.

The next stage will therefore begin with:

```text id="0id2my"
Load Handover Manifest
        ↓
Verify Input Integrity
        ↓
Load Candidate Records
        ↓
Technical Data Sanitisation
        ↓
Unicode and Text Normalisation
        ↓
Markup and Boilerplate Assessment
        ↓
Near-Duplicate Detection
        ↓
Article Segmentation
        ↓
Sequence Construction
        ↓
Dataset Quality Diagnostics
        ↓
Prepared Corpus
```

The governing principle for Notebook 02 is:

> **Sanitise structure, not meaning.**

Technical artefacts may be removed or normalised, but wording relevant to factual, emotional, psychological or social interpretation must not be neutralised merely because it may later influence Media AI measurements.
