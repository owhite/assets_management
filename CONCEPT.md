
# Translating Natural Language Questions to Queries over a Complex Scientific Provenance Schema

## Overview

This document explains how to enable Codex (or any LLM-based code generator) to translate human-readable scientific questions into SQL queries over a complex biological workflow database.

The challenge differs significantly from traditional business schemas such as customer/order databases because:

- Data is connected through lineage (provenance)
- Relationships are often many-to-many
- Entities are derived from other entities
- Metadata may live upstream in the workflow
- The correct query often requires traversing a multi-step path
- Scientific terminology may not be standardized across laboratories
- Legacy database table names may not be human-readable

This document describes a strategy for building a translation layer between:

human questions → scientific concepts → database schema → SQL queries

The goal is to make Codex capable of reliably generating correct queries even when:

- schema names are obscure
- relationships are complex
- provenance must be respected
- output grain matters
- biological semantics must be preserved

---

# Core Idea

Codex should not be expected to infer schema meaning from cryptic table names.

Instead, you provide a structured semantic mapping that explains:

1. what entities exist
2. what the entities mean scientifically
3. how entities are related
4. how provenance flows
5. where attributes originate
6. what level of granularity results should have
7. how common scientific questions translate into database paths

---

# Why this is more complex than a customer database

A typical customer database contains relatively flat relationships:

customer → address → order → purchase

In contrast, biological workflow data contains lineage:

organism
→ anatomical site
→ source sample
→ subsample
→ sequencing library
→ sequencing run
→ sequence files

Each step represents transformation of material.

Runs may include libraries derived from multiple samples, anatomical sites, organisms, and laboratories.

As a result:

queries often require traversing multiple steps upstream or downstream.

---

# Common Scientific Questions

Examples:

Find all sequencing runs containing libraries derived from liver samples.

List all samples from mouse collected by Lab A that contributed to run X.

Find runs that include data from more than one lab.

Show all subsamples derived from a particular originating sample.

Identify libraries prepared from samples taken from a specific anatomical site.

These require path traversal, not simple lookup.

---

# Required Components of the Translation Layer

## 1. Domain Ontology

Define scientific concepts clearly.

Example:

Organism
Biological source individual or specimen.

Anatomical Site
Body location where sample was collected.

Source Sample
Primary collected biological material.

Subsample
Material derived from a source sample.

Library
Sequencing library prepared from biological material.

Sequencing Run
Execution event processing multiple libraries.

Sequence File
Output data generated from sequencing run.

Laboratory
Funded research lab responsible for generating data.

---

## 2. Entity Mapping

Map scientific concepts to database tables.

Example YAML:

entities:

  organism:
    table: ORG_01
    primary_key: organism_id

  anatomical_site:
    table: ANAT_07
    primary_key: site_id

  source_sample:
    table: SMP_10
    primary_key: sample_id

  subsample:
    table: SMP_20
    primary_key: subsample_id

  library:
    table: LIB_30
    primary_key: library_id

  sequencing_run:
    table: RUN_40
    primary_key: run_id

  sequence_file:
    table: FILE_50
    primary_key: file_id

  laboratory:
    table: LAB_60
    primary_key: lab_id

---

## 3. Relationship Mapping

Define joins explicitly.

relationships:

  source_sample → organism

  source_sample → anatomical_site

  source_sample → laboratory

  subsample → source_sample

  library → subsample

  run_library bridge → library

  run_library bridge → sequencing_run

  sequence_file → sequencing_run

Explicit join definitions prevent incorrect SQL generation.

---

## 4. Provenance Rules

Describe inheritance of biological meaning.

Example:

anatomical_site is defined at source sample level

subsamples inherit organism and anatomical site

libraries inherit biological provenance from input sample

sequencing runs do not have a single organism or site

runs may contain libraries from multiple laboratories

questions about runs require tracing through libraries to upstream samples

Without these rules Codex may generate scientifically incorrect queries.

---

## 5. Authoritative Attribute Definitions

Define canonical source for each property.

Example:

organism:
  defined at source_sample

anatomical_site:
  defined at source_sample

collecting_lab:
  defined at source_sample

library_prep_lab:
  defined at library

sequencing_platform:
  defined at sequencing_run

Codex must know which table contains authoritative truth.

---

## 6. Query Path Templates

Define common traversal paths.

Example:

run_to_sample:

sequencing_run
→ run_library
→ library
→ subsample
→ source_sample

sample_to_run:

source_sample
→ subsample
→ library
→ run_library
→ sequencing_run

sample_to_site:

source_sample
→ anatomical_site

sample_to_organism:

source_sample
→ organism

These help Codex reason about directionality.

---

## 7. Result Grain Definitions

Queries may produce different output levels.

Possible grains:

run
one row per sequencing run

library
one row per sequencing library

sample
one row per biological sample

file
one row per sequence file

Example ambiguity:

"show runs involving mouse liver samples"

Expected grain:
run

Codex should deduplicate runs.

---

# Example Query Patterns

Example 1

Find runs containing libraries derived from colon samples

Traversal:

run → library → subsample → source_sample → anatomical_site

Grain:
run

---

Example 2

List source samples from mouse contributing to run R123

Traversal:

run → library → subsample → source_sample → organism

Grain:
source sample

---

Example 3

Find runs that include more than one laboratory

Traversal:

run → library → sample → laboratory

Group by run

Count distinct lab_id > 1

---

Example 4

Show descendant libraries for a source sample

Traversal:

source_sample → subsample → library

Grain:
library

---

# Prompt Structure for Codex

Example template:

You are translating scientific questions into SQL.

Use the schema definitions below.

Use only listed joins.

Respect provenance rules.

Use correct result grain.

If ambiguous, explain assumption.

Schema:
[ontology + relationships + provenance]

Examples:
[example queries]

User question:
[natural language question]

---

# Semantic Views Strategy

Recommended approach for production systems.

Create views exposing human-readable structure.

Example:

vw_library_provenance

vw_run_provenance

vw_sample_lineage

These views flatten lineage relationships.

Example simplified query:

SELECT DISTINCT run_id
FROM vw_run_provenance
WHERE organism = 'mouse'
AND anatomical_site = 'liver'
AND lab = 'Lab A'

This avoids requiring Codex to discover complex joins repeatedly.

---

# Four Stage Implementation Strategy

Stage 1
Create domain glossary

define scientific terms clearly

Stage 2
Define lineage graph

document provenance chain

Stage 3
Map ontology to schema

connect concepts to tables

Stage 4
Provide example question patterns

teach Codex reasoning structure

---

# Key Insight

Customer databases focus on transactions.

Scientific workflow databases focus on provenance.

Therefore the translation layer must encode lineage semantics.

Codex must understand:

attributes often originate upstream

queries frequently require multi-step traversal

runs aggregate material from multiple biological origins

scientific meaning is often stored indirectly

---

# Mental Model

Human asks:

"runs involving mouse liver samples"

Translation layer resolves:

mouse = organism

liver = anatomical site

site stored at source sample

runs linked through libraries

Codex builds correct path traversal query.

---

# Final Principle

You are building a scientific semantic layer over a provenance graph.

Codex then becomes a query generator operating over that semantic layer.
