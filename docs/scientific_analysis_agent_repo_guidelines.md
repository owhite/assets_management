
# Scientific Analysis Agent

A **scientific analysis agent** is an AI-powered system that can interpret natural language requests and orchestrate scientific data analysis workflows using structured tools and code. Unlike a basic chatbot, a scientific analysis agent can:

- understand domain-specific scientific terminology
- retrieve datasets from databases or repositories
- determine appropriate analysis pipelines
- execute approved computational workflows
- generate plots and summary statistics
- explain results in scientific language
- enforce constraints on appropriate use of tools

In practice, a scientific analysis agent acts as an orchestration layer between users, datasets, and analysis software. The agent does not replace validated scientific pipelines, but instead provides an intelligent interface that helps users discover data, run appropriate workflows, and interpret outputs reproducibly.

---

# How to Make a GitHub Repository Readable for a Scientific Analysis Agent

Yes. The most effective way to help an agent “digest” a GitHub repo is to make the repo readable at three levels at once:

1. what the project does
2. how to run it
3. which parts are stable building blocks vs. internal implementation

The core principle:

An agent performs best when a repository clearly communicates:

- scientific purpose
- expected inputs
- produced outputs
- valid use cases
- invalid use cases
- stable entry points
- reusable APIs
- assumptions and limitations

---

# Recommended Repository Components

## 1. Strong README

Include:

- scientific purpose
- supported data types
- supported workflows
- unsupported workflows
- quickstart instructions
- reproducible example
- API description
- when to use / not use

README sections:

Purpose  
Inputs  
Outputs  
Quickstart  
Examples  
Appropriate Use  
Not Appropriate Use  
API Overview  

---

## 2. Separate Execution Modes

Explicitly distinguish:

CLI usage  
Python API usage  
workflow usage (Snakemake / Nextflow)  
internal developer code  

Agents should not have to infer intended entry points.

---

## 3. Stable Public API

Expose intentional reusable functions.

Example:

analyze_sample()  
build_reference_index()  
call_variants()  
generate_qc_report()  

Document:

inputs  
outputs  
assumptions  
exceptions  

---

## 4. Documentation Directory

Include:

scientific rationale  
pipeline stages  
assumptions  
file format specifications  
algorithm choices  
performance expectations  
examples  
troubleshooting  

Suggested location:

docs/

---

## 5. Machine-readable citation metadata

Add:

CITATION.cff

Provides:

software identity  
authors  
version  
citation instructions  

---

## 6. Versioned Releases

Use tagged releases with notes.

Communicates:

stable versions  
pipeline changes  
reproducibility targets  

---

## 7. Repository Topics

Add GitHub topics describing domain.

Examples:

bioinformatics  
genomics  
dna-analysis  
variant-calling  
single-cell  
fastq  
snakemake  
nextflow  

Improves discoverability and classification.

---

## 8. Security and Use Boundaries

Explicitly document:

allowed data types  
prohibited use cases  
clinical limitations  
data sensitivity constraints  
external dependencies  

Consider:

SECURITY.md

---

# Suggested Repository Structure

repo/

README.md  
LICENSE  
CITATION.cff  
SECURITY.md  
CONTRIBUTING.md  

docs/

overview.md  
scientific-background.md  
input-formats.md  
output-formats.md  
cli.md  
python-api.md  
workflow-integration.md  
limitations.md  

src/

package/

api.py  
cli.py  
io.py  
analysis.py  
qc.py  
plotting.py  

examples/

small_test_dataset  
quickstart.ipynb  
run_example.sh  

tests/

workflows/

snakemake  
nextflow  

---

# Appropriate Use Section Example

Appropriate Use:

bulk DNA sequencing  
paired-end FASTQ  
reference-guided analysis  

Not Appropriate Use:

RNA-seq  
single-cell workflows  
metagenomic assembly  
clinical diagnostics  

---

# Quickstart Example

conda env create -f environment.yml  
conda activate dna-tool  

dna-tool run   --input examples/small_test_dataset   --reference refs/genome.fasta   --outdir results/

Document:

runtime expectations  
output structure  
log locations  

---

# Python API Example

from your_package.api import analyze_sample

results = analyze_sample(
    reads="reads.fastq",
    reference="genome.fasta"
)

Document:

inputs  
outputs  
assumptions  

---

# Include Small Realistic Examples

Include:

toy dataset  
notebook example  
CLI example  
API example  

Agents learn effectively from working examples.

---

# Tests as Documentation

Tests clarify:

expected behavior  
edge cases  
stable interfaces  

Readable tests improve machine understanding.

---

# Avoid Implicit Assumptions

Explicitly document:

coordinate conventions  
genome builds  
normalization assumptions  
file naming rules  
reference expectations  

Scientific ambiguity creates major downstream errors.

---

# Optional Agent Guide

docs/agent-guide.md

Contents:

plain-language project description  
stable entry points  
supported workflows  
unsupported workflows  
code module map  
scientific glossary  

---

# Core Principle

Design repositories so an agent can quickly determine:

what the tool does  
when to use it  
how to run it  
how to reuse it  
what not to do  

---

# Highest-Value Improvements

1. Strong README
2. CITATION.cff
3. versioned releases
4. repository topics
5. structured documentation directory

---

# Summary

A scientific analysis agent benefits from repositories that clearly describe scientific purpose, stable interfaces, and reproducible workflows.

Clarity of intent is as important as code correctness.
