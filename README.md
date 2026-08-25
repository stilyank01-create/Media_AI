Media AI

Abstract

Media AI is a research prototype for representing how factual information, psychological framing and social relations develop through media text as distinguishable but interacting sequential processes.

Rather than reducing an article to a sentiment label or a single bias score, the project constructs traceable representations across four textual levels:

Token → Sentence → Paragraph → Article

and three principal representational spaces:

Factual → Psychological → Social

A pretrained language model supplies the contextual semantic substrate. Media AI then adds project-specific confluent, representational, transformative, Transformative-Weight and recurrent mechanisms. The resulting architecture is designed to ask not only what a sentence represents, but how new information changes an existing state, how strongly that change is admitted into the recurrent trajectory, and how the resulting state develops across an article.

The completed pilot architecture contains:

Component

Parameters

Representation architecture

894,003

Transformative modules

8,187

Trainable Transformative-Weight parameters

49

Total persisted Media AI architecture

902,239

The current pilot corpus contains 3 independent English-language articles and 54 canonical sentences. The architecture has been validated for deterministic multi-article execution, causal article-local recurrence, article-boundary resets, supervision masking, parameter-scope isolation and persistent notebook-to-notebook restoration.

Controlled pilot optimisation reduced the equal-pathway recurrent objective from 0.21064013 at the original Notebook 09 pre-training boundary to 0.20858341, and continued Notebook 10 pilot optimisation reduced the corresponding objective from the inherited 0.20858338 baseline to 0.20655662.

These results demonstrate architectural learnability and controlled in-sample optimisation only. They are not evidence of out-of-sample generalisation, because the current optimisation and evaluation use the same three-article pilot corpus.

Model outputs throughout the project are treated as estimates of operationally defined constructs rather than psychological, social or factual ground truth.

Research Framework

Research Question

Can factual information, psychological transformation and social framing in media text be computationally represented as distinguishable but interacting processes while preserving their development across tokens, sentences, paragraphs, social groups and the article as a whole?

A related architectural question is whether explicit transformative, recurrent and confluent mechanisms provide useful structure beyond a conventional contextual language representation.

The project deliberately avoids defining the task as classifying an article as biased, unbiased, manipulative, trustworthy or untrustworthy. Such labels would introduce normative conclusions before the underlying constructs have been operationalised and evaluated.

Hypotheses

H1 — Representational Separation

Factual, psychological and social information can be represented as distinguishable but interacting spaces rather than collapsed into a single score.

H2 — Contextual Transformation

A contextual transformative mechanism can represent signed changes produced by both explicit language and information whose effect depends on preceding context.

H3 — Sequential Dependence

A recurrent state can represent effects that accumulate, persist, attenuate or reverse across textual sequences.

H4 — Controlled Carry-Forward

A learned Transformative-Weight mechanism can regulate how much of a candidate transformation contributes to the subsequent recurrent state.

H5 — Social Confluence

Social representations can preserve multiple relational dimensions, including dimensions that remain deferred when supervision is insufficient.

H6 — Directional Symmetry

Positive and negative changes can be represented without assuming that framing is inherently negative.

H7 — Hierarchical Attribution

Article-level estimates should remain traceable to lower textual levels and their underlying dimensions.

H8 — Architectural Contribution

Additional architectural complexity is justified only when controlled experiments show a measurable contribution relative to simpler alternatives.

Core Methodological Principles

Complete-text processing

Media AI does not preselect a vocabulary of supposedly emotional words. Contextual representations are generated from observed text and downstream contributions are calculated from those contextual states.

For contextual textual representations:

$$
H = (h_1, h_2, \ldots, h_n)
$$

the meaning of a token depends on its context. The persistent learned object is therefore the mapping applied to contextual representations, not a static emotional dictionary.

Representation, transformation and recurrence are distinct

For pathway (p), let the current representation be (z_t^{(p)}), the preceding recurrent state be (r_{t-1}^{(p)}), and the candidate transformation be:

$$
\Delta_t^{(p)} = T_p\left(z_t^{(p)}, r_{t-1}^{(p)}\right)
$$

A dimension-wise Transformative-Weight vector (w^{(p)}) gates the candidate transformation:

$$
\widetilde{\Delta}_t^{(p)} = w^{(p)} \odot \Delta_t^{(p)}
$$

The current pilot uses an additive recurrent update:

$$
r_t^{(p)} = r_{t-1}^{(p)} + \widetilde{\Delta}_t^{(p)}
$$

with a deterministic zero initial condition at every article boundary:

$$
r_0^{(p)} = \mathbf{0}
$$

No recurrent state propagates between articles.

Transformative-Weight parameterisation

Active dimensions use a latent parameter (\alpha) mapped to a bounded effective weight:

$$
w = \sigma(\alpha), \qquad 0 < w < 1
$$

The pilot initialises active latent parameters at zero:

$$
\alpha_0 = 0 \quad \Rightarrow \quad w_0 = 0.5
$$

Deferred dimensions remain structurally present but fixed at zero and are not trainable.

Supervision-derived transformation targets

Where current-state supervision is available, signed transformation targets are reconstructed within each article as:

$$
y_t^{(p)} = s_t^{(p)} - s_{t-1}^{(p)}
$$

with a deterministic zero preceding condition for the first sentence of every article.

Missing supervision is represented by masks and remains distinct from a genuine zero-valued target.

Objective

Each pathway uses mask-aware mean squared error over supervision-valid and activation-eligible elements:

$$
\mathcal{L}p =
\frac{\sum{t,d} m_{t,d}^{(p)}
\left(\widetilde{\Delta}{t,d}^{(p)} - y{t,d}^{(p)}\right)^2}
{\sum_{t,d} m_{t,d}^{(p)}}
$$

The pilot objective gives the factual, psychological and social pathways equal weight:

\frac{1}{3}
\left(
\mathcal{L}{\mathrm{factual}}
+
\mathcal{L}{\mathrm{psychological}}
+
\mathcal{L}_{\mathrm{social}}
\right)
$$

Model output is not ground truth

$$
\boxed{\text{model output} \neq \text{ground truth}}
$$

Terms such as estimated, model-assigned, model-detected and according to the operational definition are preferred where appropriate.

Architecture

End-to-end research pipeline

flowchart TD
    A[01 Data acquisition<br/>source traceability] --> B[02 Sanitisation<br/>structural preparation]
    B --> C[03 Supervision<br/>annotation]
    C --> D[04 Representation<br/>architecture]
    D --> E[05 Contextual representations<br/>experimental preparation]
    E --> F[06 Factual pathway]
    F --> G[07 Social pathway<br/>representation finalisation]
    G --> H[08 Transformative mechanism]
    H --> I[09 Transformative Weight<br/>recurrent architecture]
    I --> J[10 Multi-article validation<br/>continued pilot optimisation]

The notebooks are deliberately cumulative. Persistent handovers allow later notebooks to restore validated upstream state without relying on temporary Colab memory.

Model architecture

flowchart LR
    X[Contextual sentence vector<br/>768] --> B[Backbone<br/>Linear 768→768]
    B --> C[Global confluent module<br/>768→256]
    C --> R[Representation core<br/>256→128]

    R --> P[Psychological head<br/>128→34]
    R --> F[Factual head<br/>128→10]
    R --> S[Social head<br/>128→7]

    P --> TP[Psychological transformative<br/>68→68→34]
    F --> TF[Factual transformative<br/>20→20→10]
    S --> TS[Social transformative<br/>14→14→7]

    WP[34 active TW gates] --> GP[Gate candidate]
    WF[10 active TW gates] --> GF[Gate candidate]
    WS[5 active + 2 deferred<br/>TW dimensions] --> GS[Gate candidate]

    TP --> GP
    TF --> GF
    TS --> GS

    GP --> RP[Psychological recurrent state<br/>34]
    GF --> RF[Factual recurrent state<br/>10]
    GS --> RS[Social recurrent state<br/>7]

The transformative modules consume the current pathway representation together with the immediately preceding pathway-specific recurrent state. Their input dimensions are therefore twice the pathway state dimension.

Causal recurrent execution

flowchart LR
    Z0[Article boundary] --> R0[Zero recurrent state]
    R0 --> S1[Sentence 1]
    S1 --> D1[Candidate transformation]
    D1 --> W1[Transformative Weight gate]
    W1 --> R1[Recurrent state 1]

    R1 --> S2[Sentence 2]
    S2 --> D2[Candidate transformation]
    D2 --> W2[Transformative Weight gate]
    W2 --> R2[Recurrent state 2]

    R2 --> SN[... Sentence t]
    SN --> DN[Candidate transformation]
    DN --> WN[Transformative Weight gate]
    WN --> RN[Recurrent state t]

    RN --> END[End of article]
    END --> RESET[Discard runtime state]
    RESET --> NEXT[Next article starts from zero]

No future sentence is supplied to an earlier recurrent step, supervision is never supplied as recurrent model input, and runtime article states are not persisted as learned parameters.

Parameter architecture

pie showData
    title Media AI persisted parameter architecture
    "Representation — 894,003" : 894003
    "Transformative — 8,187" : 8187
    "Transformative Weight — 49" : 49

The Transformative-Weight layer is intentionally small. It provides 49 trainable latent gates over the already constructed factual, psychological and social transformative pathways.

Current Architecture Contract

Pathway

Representation dimension

Transformative input

Transformative hidden

Transformative output

Active TW dimensions

Deferred TW dimensions

Factual

10

20

20

10

10

0

Psychological

34

68

68

34

34

0

Social

7

14

14

7

5

2

Total

51





51

49

2

The two deferred social dimensions are:

economic cohesion — index 1;

religious cohesion — index 3.

They remain structurally represented but fixed at zero under the current supervision and activation contract.

Notebook Architecture

The project evolved beyond the original five-notebook plan into a ten-notebook pilot research pipeline. The extension was intentional: later architectural mechanisms were separated into independent notebooks so that restoration, parameter scope, training boundaries and persistence could be audited explicitly.

01 — Data Acquisition and Source Traceability
02 — Data Sanitisation and Preparation
03 — Supervision and Annotation
04 — Representation and Model Architecture
05 — Contextual Representation and Experimental Preparation
06 — Factual Representation Path
07 — Social Representation and Architecture Finalisation
08 — Transformative Mechanism
09 — Transformative-Weight and Recurrent Architecture
10 — Multi-Article Validation and Pilot Completion

Notebook 01 — Data Acquisition and Source Traceability

Objective

Notebook 01 establishes the empirical input to the Media AI research pipeline.

Its responsibility is limited to:

establishing a reproducible computational environment;

configuring persistent research storage;

isolating API credentials;

defining acquisition safeguards;

maintaining persistent API usage accounting;

specifying article eligibility rules before acquisition;

preserving original provider records;

validating the external API;

performing controlled candidate acquisition;

reviewing technical eligibility and exact duplicates;

creating a reproducible candidate ordering;

generating a persistent handover manifest for Notebook 02.

Notebook 01 deliberately does not perform semantic sanitisation, emotion analysis, tensor construction, supervision or model inference.

This maintains a clear boundary between acquired source material and subsequent analytical transformation.

Environment and Reproducibility

The notebook establishes project metadata, execution timestamps and a fixed random seed before external data are acquired.

The project currently uses:

RANDOM_SEED = 42

The fixed seed supports reproducible random ordering and subsequent experiments where deterministic execution is available.

Runtime metadata record the project version, Python version, principal package versions, platform information and UTC execution time.

The environment block performs no external API request.

Restricted Persistent Storage

Google Colab runtimes are temporary, so persistent storage is required for source records and experiment artefacts.

Media AI uses the Google Drive API rather than mounting the user's complete Google Drive filesystem.

The notebook creates a dedicated project structure including:

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

Folder creation is idempotent. Re-executing the storage block reuses existing project folders rather than deliberately creating duplicate structures.

No Google Drive filesystem mount is required.

API Credential Isolation

The pilot currently uses the GNews API.

The API credential is stored through Google Colab Secrets under:

GNEWS_API_KEY

The credential is not embedded in notebook cells, printed in notebook outputs or written to research manifests.

Opening the notebook from GitHub therefore does not expose the original researcher's API credential.

Researchers who wish to perform new acquisition provide their own API key.

Acquisition Master Switch

New article acquisition is disabled by default:

DOWNLOAD_NEW_DATA = False

The notebook must be deliberately switched into acquisition mode before candidate data can be requested.

This protects against accidental API traffic caused by reopening the notebook or executing all cells.

After live acquisition, the recommended state is to return the switch to False.

API Usage Safeguards

The pilot defines independent research-side usage limits rather than relying solely on provider limits.

At the current pilot configuration:

Approved requests/day       1,000
Research requests/day         200

Approved articles/request      25
Research articles/request       5

Research articles/day         200

Usage fraction                20%

These settings are intentionally conservative.

The notebook checks limits before each live request.

Persistent API Usage Ledger

API accounting is stored persistently rather than only in temporary Colab memory.

The usage ledger records:

UTC date;

requests used;

articles requested;

articles returned;

last update timestamp.

Persisting the ledger prevents a Colab runtime restart from silently resetting research-side usage counters.

Every completed connection or acquisition request is entered into the ledger.

Content-Neutral Sampling Design

Article-selection criteria are specified before observing the research corpus.

The governing principle is:

Random by content; selective only by technical usability.

The acquisition process does not select articles according to:

Topic                         False
Sentiment                     False
Publisher preference          False
Political orientation         False
Expected framing              False

The purpose is to reduce researcher-induced selection effects and avoid constructing the corpus around phenomena that the proposed architecture is expected to discover.

Technical Eligibility

The pilot technical criteria are:

Target language           English
Minimum article length    300 words
Maximum article length    None
Full text                 Required
Source information        Required
Exact duplicate           Excluded

The minimum-length criterion is architectural rather than editorial.

Media AI is intended to examine transformations across sequential context. Very short texts provide limited information for analysing how emotional and social states develop through an article.

No maximum article length is imposed during acquisition. Model context-window constraints are handled later during preparation.

Exact and Near-Duplicate Separation

Notebook 01 detects exact duplicates through deterministic textual fingerprints based on conservatively normalised content.

The fingerprint is used only for exact identity.

It does not identify:

syndicated articles with minor changes;

paraphrases;

semantically similar reports;

different reports of the same event.

Near-duplicate detection is deliberately deferred to Notebook 02, where similarity methods can be evaluated explicitly.

Original Source Records

The complete provider record is preserved separately from derived research data.

The observed GNews article schema currently contains:

content
description
id
image
lang
publishedAt
source
title
url

Media AI maps selected fields into a standard source-record schema while retaining the complete provider payload.

The source record contains information such as:

internal Media AI record identifier;

source-record schema version;

provider name;

provider article identifier;

acquisition method;

UTC acquisition timestamp;

publication timestamp;

source metadata;

source URL;

article language;

title;

word count;

textual fingerprint;

technical eligibility result;

acquisition context;

preserved provider payload.

Analytical outputs are excluded.

The source record therefore does not contain:

emotional weight;

factological weight;

emotion classifications;

transformative-state values;

recurrent-state values;

confluent representations;

Social Polarisation Distance;

supervisory labels;

model confidence.

Those belong to subsequently derived datasets.

Source Traceability

The separation between source and derived data establishes the research chain:

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

A later model conclusion should therefore remain traceable to the textual evidence from which it originated.

The original provider record is not overwritten by later preprocessing.

Controlled API Validation

Before acquiring research candidates, Notebook 01 performs a minimal controlled connection test.

The test requires an additional explicit switch and requests only one article.

The first live GNews validation returned:

Test status               SUCCESS
Articles returned         1
Top-level response        articles, totalArticles

The observed article fields were:

content
description
id
image
lang
publishedAt
source
title
url

The test request was recorded in the persistent usage ledger.

The connection-test article was used for integration validation rather than treated as part of the research corpus.

Controlled Candidate Acquisition

The first controlled candidate acquisition requested five articles, corresponding to the configured per-request research limit.

The initial acquisition produced:

Articles requested         5
Articles returned          5
Technically eligible       3
Technically ineligible     2
Stored source records      5

All five returned provider records were preserved.

Technical ineligibility does not result in deletion of the original source record.

This allows the exclusion process itself to remain auditable.

Persistent Original-Source Storage

Each acquisition batch is stored as a timestamped research object containing:

acquisition batch identifier;

UTC acquisition timestamp;

provider;

source-record schema version;

complete raw API response;

canonical Media AI source records.

Existing acquisition batches are not deliberately overwritten.

This permits later reconstruction of the input to the eligibility and preparation stages.

Eligibility Review

The first acquisition batch produced:

Source records reviewed     5
Technical exclusions        2
Exact duplicates            0
Eligible unique records     3
Sampling seed               42

Exact duplicate control was applied after technical eligibility.

The resulting eligible records were placed into a reproducible random order using the fixed project seed.

Near-duplicate analysis remains deferred to Notebook 02.

First Acquisition Audit

At completion of the first acquisition cycle, the persistent usage ledger reported:

API requests recorded       2
Articles requested          6
Articles returned           6

The totals reconcile as:

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

This accounting confirms that connection-test traffic and research acquisition traffic are both represented in the persistent usage record.

Notebook 01 Handover Manifest

Notebook 01 produces:

notebook_01_handover_manifest.json

The manifest records the information required for Notebook 02 to identify and verify its input without relying on temporary Colab state.

It contains:

project metadata;

source provider;

source-record schema version;

acquisition batch identifier;

acquisition batch storage reference;

reviewed candidate file reference;

source-record counts;

technical exclusion count;

exact-duplicate count;

candidate-record count;

language criterion;

article-length criterion;

sampling seed;

content-neutral selection settings;

persistent API usage summary;

relevant research storage references;

planned Notebook 02 processing stage.

The manifest explicitly records that API credentials are not contained in the handover data.

Notebook 01 Result

Notebook 01 establishes a complete reproducible acquisition path:

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

The first controlled pilot run resulted in three technically eligible, unique candidate articles from five acquired research records.

No semantic sanitisation, emotional analysis, framing classification or model inference is performed during Notebook 01.

The original source records remain unchanged.

Handover to Notebook 02

Notebook 02 begins from the persistent acquisition manifest rather than making new API requests.

Its responsibility is to create derived analytical data while preserving the original records established in Notebook 01.

The next stage will therefore begin with:

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

The governing principle for Notebook 02 is:

Sanitise structure, not meaning.

Technical artefacts may be removed or normalised, but wording relevant to factual, emotional, psychological or social interpretation must not be neutralised merely because it may later influence Media AI measurements.

Notebook 02 — Data Sanitisation and Preparation

Notebook 02 transforms the acquisition output from Notebook 01 into a
structurally prepared and auditable research corpus while preserving the
original source records.

The notebook deliberately separates technical preparation from later
semantic supervision and modelling. It does not assign emotional or
factological weights and does not perform model-specific tokenisation.

The preparation pipeline includes:

Handover loading and schema compatibility

Loads the persistent Notebook 01 handover and candidate records.

Verifies record counts, identifiers, fingerprints and source-record
integrity.

Supports historical source-schema versions without rewriting the original
records.

Technical sanitisation

Creates separate sanitised representations while preserving the source
records unchanged.

Removes only defined technical artefacts such as invalid control
characters or markup when present.

Records every detected transformation in the sanitisation audit.

Sanitisation audit

Compares source and sanitised representations.

Treats unchanged records as valid outcomes.

Verifies that technical preparation has not introduced unintended textual
changes.

Near-duplicate assessment

Performs lexical similarity analysis across candidate articles.

Identifies records that may require review without automatically removing
them.

Keeps similarity diagnostics separate from semantic similarity.

Hierarchical article segmentation

Derives traceable article → paragraph → sentence representations.

Preserves the complete sanitised article text.

Verifies reconstruction of the article from the derived hierarchy.

Does not create permanent model-specific tokens.

Dataset quality diagnostics

Evaluates structural characteristics such as article, paragraph and
sentence lengths.

Flags unusual structural conditions for review rather than exclusion.

Consolidates sanitisation, segmentation and near-duplicate warnings.

Keeps quality flags descriptive rather than semantic judgements.

Persistent prepared corpus

Persists the canonical prepared corpus and diagnostic artefacts to Google
Drive.

Stores complete sanitised text together with its traceable structural
hierarchy.

Reloads persisted artefacts to verify that the research handover does not
depend on temporary notebook memory.

Final audit and Notebook 03 handover

Independently verifies persisted counts, identifiers, parent links,
diagnostic artefacts and preservation guarantees.

Reports structural review flags explicitly.

Creates the formal handover to
03_supervision_and_annotation.

Methodological boundary

Notebook 02 operates only on the structural preparation layer.

At the Notebook 02 → Notebook 03 boundary:

complete sanitised article text is preserved;

original source records remain unchanged;

article, paragraph and sentence traceability is preserved;

no permanent model-specific tokenisation has been created;

no emotional tokens have been pre-filtered;

no emotional weights have been assigned;

no factological weights have been assigned;

no recurrent states have been calculated;

no confluent representation has been calculated.

This boundary is intentional. Semantic supervision and annotation are introduced
only in Notebook 03, while trainable transformative, recurrent and confluent
model components are introduced in later stages.

Persisted outputs

Notebook 02 produces the following principal research artefacts:

notebook_02_prepared_corpus.json

notebook_02_sanitisation_audit.json

notebook_02_near_duplicate_review.json

notebook_02_quality_diagnostics.json

notebook_02_handover_manifest.json

The prepared corpus is the canonical input to Notebook 03.

Notebook 03 — Supervision and Annotation

Notebook 03 establishes the supervision and annotation framework used to create
traceable training and evaluation signals for the Media AI architecture.

The notebook receives the persistent prepared corpus from Notebook 02 and
operationalises the emotional and factological constructs required for later
model development.

It deliberately separates supervision from model architecture and
training. No transformative, recurrent or confluent model component is
trained in this notebook.

The supervision pipeline includes:

Prepared-corpus handover and verification

Loads the persistent Notebook 02 handover manifest and prepared corpus.

Reconstructs the research input independently of temporary Notebook 02
objects.

Verifies article, paragraph and sentence counts and preservation
guarantees.

Confirms that no emotional or factological weights have already been
assigned.

Annotation schema

Defines sentence-level supervision with preceding article context.

Operationalises 11 emotional dimensions and 8 factological components.

Separates reported emotion, transformative emotion and immediate emotional
state.

Represents transformative emotion as a signed change on [-1, +1].

Keeps immediate emotional state and supervision weights non-negative.

Leaves activation functions and recurrent-state computation to the model
architecture rather than prescribing them through the annotation schema.

Traceable supervision records

Constructs one supervision context and blank annotation record for every
prepared sentence.

Preserves Media AI article, paragraph and sentence identifiers together
with their structural indices.

Maintains preceding context without exposing future article context.

Keeps annotations separate from the prepared corpus.

Independent LLM supervision protocol

Defines provider-independent supervision instructions and structured output
requirements.

Prevents supervisors from receiving future context, another supervisor's
labels or Media AI model predictions.

Preserves supervisor identity and annotation provenance.

Supports independent comparison across multiple model families.

Controlled supervisor execution

Provides a stable five-slot supervisor registry.

Allows individual supervisors to be enabled or disabled centrally.

Separates provider configuration, credentials, implementation status and
execution readiness.

Retains raw provider responses separately for subsequent validation.

Supports tightly limited pilot execution before corpus-scale supervision.

Keeps external LLM supervision disabled by default.

Supervision validation

Standardises successful supervisor outputs into the Media AI annotation
structure.

Validates schema compliance, allowed ranges, evidence and provenance before
annotations are accepted.

Separates accepted supervision from rejected responses and diagnostic
reasons.

Does not silently repair malformed supervision into valid reference data.

Supervisor agreement and disagreement

Aligns independent annotations through their sentence identifiers.

Supports pairwise comparison when multiple supervisors annotate the same
sentence.

Measures differences in continuous supervision signals.

Separately evaluates the direction of signed transformative emotion.

Compares factological components through exact agreement.

Treats disagreement as a review signal rather than an automatic error or
exclusion criterion.

Does not construct implicit consensus through averaging, voting or
supervisor ranking.

Reference-dataset construction

Provides a versioned layer for converting validated supervision into
downstream research records.

Distinguishes machine_supervision, human_reviewed,
adjudicated_reference and not_reference_ready states.

Separates annotation validity from reference-label status.

Does not automatically treat independent LLM supervision as human-reviewed
or adjudicated ground truth.

Keeps construction and persistent storage under independent execution
controls.

Final audit and Notebook 04 readiness

Independently verifies the prepared input, annotation schema, supervision
contexts, LLM protocol, supervisor registry, validation layer, comparison
layer and reference-dataset infrastructure.

Distinguishes Notebook 03 pipeline completeness from Notebook 04
supervision-data readiness.

Reports explicit readiness blockers when live supervision, reference-data
construction or persistence has not yet occurred.

Prevents the existence of supervision infrastructure from being mistaken
for the existence of a completed reference dataset.

Current pilot state

The Notebook 03 supervision infrastructure has been implemented and passes its
final structural and methodological audit.

The current prepared input contains:

Articles                    3
Paragraphs                  3
Sentences                  54
Supervision contexts       54
LLM supervision payloads   54
Invalid payloads             0
Supervisor slots             5

External LLM supervision remains disabled at the current checkpoint.

Consequently:

Raw supervision responses    0
Accepted annotations          0
Rejected annotations          0
Reference records             0

Notebook 03 pipeline complete True
Notebook 04 data ready        False

This is an intentional pre-supervision state rather than a failed pipeline.

The initial live experiment will begin with a single enabled supervisor and a
strictly limited pilot request before supervision is expanded to the prepared
corpus.

Methodological boundary

Notebook 03 operates only on the supervision and annotation layer.

At the Notebook 03 → Notebook 04 boundary:

complete prepared text remains preserved;

structural traceability remains intact;

annotations remain derived research artefacts;

reported and transformative emotion remain separate constructs;

signed transformation is distinguished from non-negative emotional state;

independent supervisor outputs remain independently identifiable;

disagreement is not automatically converted into consensus;

machine supervision is not automatically treated as adjudicated ground truth;

no permanent model-specific tokenisation has been introduced;

no model training has been performed;

no recurrent state has been calculated;

no confluent representation has been calculated.

Notebook 04 becomes data-ready only after valid supervision has been produced,
the reference dataset has been constructed and the required handover artefacts
have been persisted.

Notebook 04 — Representation and Model Architecture

Objective

Notebook 04 forms the architectural bridge between supervision and experimentation. It receives the prepared corpus and supervision contracts established upstream and defines hierarchical Media AI representations while preserving traceability to the original text.

The notebook formalises the four textual levels:

Token → Sentence → Paragraph → Article

and the three principal representational spaces:

Factual → Psychological → Social

Its purpose is architectural rather than interpretive: it establishes how contextual semantic information can be projected into Media AI-specific spaces without relabelling Transformer attention as emotional or social explanation.

Architectural role

Notebook 04 introduces the representation design subsequently inherited and validated by later notebooks. It establishes the separation between:

contextual backbone state;

global confluent representation;

pathway-specific representation;

downstream transformative state;

recurrent state;

later Transformative-Weight gating.

This separation becomes essential in Notebooks 08–10, where parameter ownership and training scope are audited explicitly.

Notebook 05 — Contextual Representation and Experimental Preparation

Objective

Notebook 05 constructs the contextual sentence representations used by the downstream Media AI architecture and establishes the experimental handover required by later pathway notebooks.

The canonical contextual representation contract uses:

Encoder model       sentence-transformers/all-mpnet-base-v2
Embedding dimension 768

The persisted contextual sentence representations preserve article and sentence identity and become the model input used by the later representation architecture.

The current validated pilot corpus contains:

Articles   3
Sentences 54
Dimension 768

Notebook 05 also establishes the experimental framing for later baseline comparison, controlled ablation, recurrent trajectory analysis and attribution.

Notebook 06 — Factual Representation Path

Objective

Notebook 06 operationalises and validates the factual pathway required by the Media AI representation architecture.

The factual pathway ultimately exposes a 10-dimensional representation and produces the persisted factual supervision contract later recovered by Notebooks 09 and 10:

notebook_06_factual_target_contract.json

This contract is used to align factual current-state supervision to the canonical sentence corpus and to reconstruct signed factual transformation targets.

The factual pathway is kept conceptually distinct from truth probability. It represents operationalised informational characteristics rather than a claim that the model has established whether a statement is true.

Notebook 07 — Social Representation and Architecture Finalisation

Objective

Notebook 07 finalises the inherited representation architecture and the social pathway before transformative recurrence is introduced.

The final restored representation architecture is:

Backbone
Linear(768 → 768)

Global confluent
Linear(768 → 256)
GELU
Dropout(0.10)
LayerNorm(256)

Representation core
Linear(256 → 128)
GELU
Dropout(0.10)
LayerNorm(128)

Psychological output
Linear(128 → 34)

Factual output
Linear(128 → 10)

Social output
Linear(128 → 7)

Parameter accounting:

Module

Parameters

Backbone

590,592

Global confluent

197,376

Psychological path

37,538

Factual path

34,442

Social path

34,055

Total representation architecture

894,003

The accepted social supervision contract contains seven dimensions:

interpersonal_cohesion
economic_cohesion
political_cohesion
religious_cohesion
group_collective_cohesion
institutional_role_relation
public_audience_relation

Notebook 07 persists the architecture required by Notebook 08 and confirms that no transformative state exists at the handover boundary.

Notebook 08 — Transformative Mechanism

Objective

Notebook 08 adds pathway-specific transformative modules to the frozen representation architecture.

For pathway (p), the transformative module receives the current representation and preceding recurrent state:

$$
u_t^{(p)} =
\left[
z_t^{(p)};
r_{t-1}^{(p)}
\right]
$$

and produces a candidate signed transformation:

$$
\Delta_t^{(p)} = T_p\left(u_t^{(p)}\right)
$$

Transformative modules

Pathway

Input

Hidden

Output

Parameters

Factual

20

20

10

670

Psychological

68

68

34

7,174

Social

14

14

7

343

Total







8,187

Each module uses:

Linear
GELU
Dropout(0.10)
LayerNorm
Linear

Controlled forward-path validation confirmed correct shapes, finite outputs and deterministic evaluation behaviour.

The inherited representation architecture remains frozen while the transformative mechanism is constructed and validated.

Notebook 09 — Transformative-Weight and Recurrent Architecture

Objective

Notebook 09 introduces the Transformative-Weight mechanism and formalises causal recurrent state propagation.

Transformative Weight is distinct from:

Transformer attention;

candidate transformation;

pathway loss weighting;

recurrent state itself.

It is a dimension-wise gate determining how strongly each candidate transformation contributes to the recurrent update.

Structural contract

The complete Transformative-Weight space contains 51 structural dimensions:

Factual        10
Psychological  34
Social          7
Total          51

Of these, 49 are activation-eligible and trainable. Two social dimensions remain deferred.

Active latent parameters are mapped through the sigmoid function:

$$
w_d = \sigma(\alpha_d)
$$

At construction:

$$
\alpha_d = 0
\quad\Rightarrow\quad
w_d = 0.5
$$

Deferred dimensions remain exactly zero.

Recurrent contract

The validated recurrent update is additive:

r_{t-1}^{(p)}
+
w^{(p)} \odot \Delta_t^{(p)}
$$

with:

deterministic zero initial state;

reset at every article boundary;

no cross-article propagation;

no cross-pathway recurrence;

no future context;

no supervision supplied as recurrent model input.

Supervision alignment

Notebook 09 recovered:

Pathway

Current-state available

Transformation-target available

Objective-active

Factual

536 / 540

534 / 540

534

Psychological

1,656 / 1,836

1,606 / 1,836

1,606

Social

155 / 378

120 / 378

120

Initial controlled objective

Before Transformative-Weight training:

Pathway

Masked MSE

Factual

0.28182298

Psychological

0.08874305

Social

0.26135439

Equal-pathway total

0.21064013

Controlled Transformative-Weight training

Only the 49 latent Transformative-Weight parameters were supplied to AdamW.

Optimizer             AdamW
Learning rate         0.001
Weight decay          0.0001
Maximum gradient norm 1.0
Epochs                25

After controlled training:

Pathway

Initial MSE

Final MSE

Delta

Factual

0.28182298

0.28011325

-0.00170973

Psychological

0.08874305

0.08706581

-0.00167724

Social

0.26135439

0.25857118

-0.00278321

Total

0.21064013

0.20858341

-0.00205672

All 894,003 representation parameters and all 8,187 transformative parameters remained frozen and unchanged.

Persistent handover

Notebook 09 persists:

notebook_09_final_checkpoint.pt
notebook_09_final_metadata.json

The checkpoint contains the complete 902,239-parameter architecture and is independently read back and validated before Notebook 10 restoration.

Article-specific runtime recurrent states are not persisted as learned model state.

Notebook 10 — Multi-Article Validation and Pilot Completion

Objective

Notebook 10 restores the complete Notebook 09 architecture from persistent artefacts and validates the model as a reproducible multi-article causal system.

It does not silently treat the three-article pilot corpus as an expanded generalisation dataset. Instead, it distinguishes:

multi-article architectural validation;

continued in-sample pilot optimisation;

future expanded-corpus and held-out evaluation.

Persistent restoration

Notebook 10 restores and validates:

Representation parameters       894,003
Transformative parameters         8,187
Transformative Weight params         49
Total model parameters          902,239
Structural TW dimensions             51
Active TW dimensions                 49
Deferred TW dimensions                2

The complete architecture is restored frozen and in evaluation mode before any Notebook 10 execution.

Corpus validation

The validated corpus contains:

Articles                         3
Canonical sentences             54
Contextual representation dim. 768
Representation coverage       100%
Synthetic fallback used       False

The corpus preserves explicit article boundaries, globally unique sentence identities, canonical sequence order and contextual representation alignment.

Multi-article recurrent validation

Notebook 10 traverses all 54 sentences through the frozen architecture.

For every article:

$$
r_0^{(p)} = \mathbf{0}
$$

and recurrent state is discarded before the next article begins.

Validation confirmed:

fresh zero initial states;

independent article reset storage;

no cross-article state propagation;

no future context;

no supervision used as model input;

correct representation, candidate and recurrent-state shapes;

finite candidate transformations and states;

exact deferred-dimension inactivity;

deterministic repeated corpus execution.

Expanded supervision alignment

Notebook 10 independently reconstructs the supervision and transformation-target contract over the canonical corpus.

Current-state availability:

Pathway

Available

Supervised sentences

Supervised articles

Factual

536 / 540

54 / 54

3 / 3

Psychological

1,656 / 1,836

54 / 54

3 / 3

Social

155 / 378

51 / 54

3 / 3

Derived objective-active transformation targets remain:

Factual        534
Psychological 1606
Social         120

Notebook 10 pre-training baseline

The inherited Notebook 09 trained state produces:

Pathway

Masked MSE

Factual

0.28011322

Psychological

0.08706581

Social

0.25857112

Equal-pathway total

0.20858338

The objective is evaluated twice before optimisation and reproduces deterministically.

Continued pilot optimisation

Notebook 10 then performs another controlled 25-epoch optimisation of only the 49 Transformative-Weight parameters.

The result is:

Pathway

Baseline MSE

Final MSE

Delta

Factual

0.28011322

0.27843508

-0.00167814

Psychological

0.08706581

0.08543939

-0.00162642

Social

0.25857112

0.25579539

-0.00277573

Total

0.20858338

0.20655662

-0.00202676

All learned effective weights remain finite and bounded. Deferred social dimensions remain fixed at zero.

Post-training recurrent validation

Notebook 10 independently reproduces the complete post-training objective and recurrent trajectories without further optimisation.

The Block 9 result is reproduced exactly:

0.20655662
$$

against the immutable Block 8 baseline:

0.20858338
$$

so that:

-0.00202676
$$

The architecture remains unchanged during validation and no optimiser, backward pass or parameter update is executed.

Pilot Results

Objective progression

xychart-beta
    title "Controlled pilot objective progression"
    x-axis ["N09 pre-training", "N09 post-training", "N10 baseline", "N10 post-training"]
    y-axis "Equal-pathway masked MSE" 0.20 --> 0.215
    line [0.21064013, 0.20858341, 0.20858338, 0.20655662]

The small difference between the Notebook 09 post-training value and Notebook 10 restored baseline reflects numerical reproduction at the validated runtime boundary, not a new training stage.

Pathway-specific Notebook 10 change

xychart-beta
    title "Notebook 10 pathway objective comparison"
    x-axis ["Factual", "Psychological", "Social"]
    y-axis "Masked MSE" 0 --> 0.30
    bar [0.28011322, 0.08706581, 0.25857112]
    bar [0.27843508, 0.08543939, 0.25579539]

First series: inherited baseline.
Second series: post-training objective.

What the pilot establishes

The completed pilot provides evidence that:

the representation, transformative, gating and recurrent components can be composed into one deterministic architecture;

the complete model can be persisted and restored independently;

causal article-local recurrent trajectories can be executed without future leakage;

missing supervision can be masked without being confused with genuine zero values;

deferred dimensions can remain structurally present without becoming trainable;

optimisation can be restricted exactly to the intended 49 Transformative-Weight parameters;

the controlled objective can be reduced while the 902,190 inherited representation and transformative parameters remain frozen;

repeated post-training evaluation reproduces the same recurrent objective and trajectories.

What the pilot does not establish

The current project does not establish:

out-of-sample generalisation;

cross-source robustness;

cross-topic robustness;

temporal generalisation;

psychological effects on real readers;

factual truth of model-assigned factual representations;

superiority over external state-of-the-art systems;

causal media effects in human populations.

The current result classification is therefore:

controlled_in_sample_pilot_optimisation

not final model performance.

Reproducibility and Execution Boundaries

Persistent state

Later notebooks restore validated state from persistent Google Drive artefacts rather than requiring upstream notebooks to remain in Colab memory.

This is particularly important at the Notebook 09 → Notebook 10 boundary, where the complete architecture is restored from the final checkpoint and metadata contract.

Parameter ownership

The final pilot separates parameter ownership explicitly:

Representation architecture       894,003  frozen
Transformative architecture         8,187  frozen
Transformative Weight                  49  controlled trainable scope
----------------------------------------------------
Total                              902,239

During final post-training validation all parameters are frozen.

Determinism

The project uses:

RANDOM_SEED = 42

where applicable and validates deterministic repeated execution at critical forward and recurrent boundaries.

Causal safeguards

The recurrent architecture enforces:

Sequence order preserved      True
Batch shuffling               False
Future context allowed        False
Article-boundary reset        True
Cross-article propagation     False
Cross-pathway recurrence      False
Supervision as model input    False

Interpretation and Traceability

Media AI is designed so that broad conclusions remain decomposable.

A future inspection interface should allow movement between:

Article
  ↓
Paragraph
  ↓
Sentence
  ↓
Contextual token

and between:

Factual representation
Psychological representation
Social representation
Candidate transformation
Transformative Weight
Weighted transformation
Recurrent state

Colour overlays are intended as a visual projection of these numerical states rather than independent evidence.

Next Research Stage

The completed ten-notebook sequence should be treated as the architectural pilot.

The next research stage requires a genuinely expanded corpus with explicit training, validation and held-out test partitions. The most important next steps are:

acquire and prepare substantially more independent articles without topic-directed sampling;

construct validated supervision for the expanded corpus;

define article-level train/validation/test separation before optimisation;

evaluate out-of-sample recurrent objectives;

compare against simpler backbone and projection baselines;

perform component ablations for transformative, recurrent, confluent and Transformative-Weight mechanisms;

test directional symmetry and controlled counterfactuals;

evaluate cross-source, cross-topic and temporal robustness;

develop hierarchical attribution and colour-based inspection;

report uncertainty and limitations alongside all model estimates.

Only after those stages should claims about generalisation or architectural superiority be considered.

Project Status

Data acquisition and traceability             Complete for pilot
Sanitisation and structural preparation       Complete for pilot
Supervision framework                         Complete for pilot
Representation architecture                   Complete
Factual pathway                               Complete
Social pathway                                Complete
Transformative mechanism                      Complete
Transformative-Weight mechanism               Complete
Causal recurrent architecture                 Complete
Persistent Notebook 09 → 10 restoration       Complete
Multi-article recurrent validation            Complete
Controlled in-sample pilot optimisation       Complete
Post-training deterministic validation        Complete

Expanded independent corpus                   Required
Held-out evaluation                           Required
Generalisation evidence                       Not yet established
Final research conclusions                    Not yet established

Research Position

Media AI should currently be understood as a validated architectural research prototype.

Its central contribution is the explicit computational separation of:

contextual representation → candidate transformation → learned transformation weight → causal recurrent state

across factual, psychological and social spaces, while preserving article boundaries, missing-data semantics, parameter ownership and traceability.

The pilot demonstrates that this architecture can be constructed, trained within a tightly controlled parameter scope, persisted, restored and evaluated deterministically.

The next scientific question is no longer whether the architecture can execute as intended. It is whether the architecture provides reproducible out-of-sample explanatory and predictive value when evaluated on a substantially larger and independently partitioned corpus.
