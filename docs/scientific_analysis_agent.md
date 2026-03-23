
# AI Agent Architecture for Scientific Data Platforms

## Executive Summary

This document summarizes architectural patterns and terminology for building a public-facing conversational system that allows users to:

1. ask scientific questions about complex biological datasets
2. retrieve structured data from databases
3. trigger computational workflows (e.g., single-cell analysis)
4. receive conversational explanations of results

Your NIH data model involves lineage relationships:

organism → anatomical site → sample → subsample → library → sequencing run → sequence files

This type of schema requires more sophisticated reasoning than a typical business database.

The system typically evolves through increasing capability:

chatbot → data agent → workflow agent → scientific analysis agent

OpenAI provides APIs and tools to implement these systems.

---

# Terminology

## Chatbot

A chatbot is a conversational interface powered by an LLM.

Capabilities:

• answers questions  
• summarizes text  
• retrieves information  
• provides explanations  

Examples:

FAQ assistant  
documentation assistant  
help desk bot  

Chatbots may not interact with external systems.

---

## LLM Application

An LLM application is any software that uses a large language model.

Examples:

translation system  
coding assistant  
summarization tool  
search assistant  

---

## Data Agent

A data agent retrieves structured information from databases.

Capabilities:

• interpret user questions  
• determine relevant schema elements  
• generate SQL or API queries  
• execute queries via backend services  
• interpret results  
• present conversational summaries  

Example:

User:
show all sequencing runs containing libraries derived from mouse liver samples

Agent:

identify biological entities  
determine join path  
generate SQL  
retrieve results  
explain results  

---

## Workflow Agent

A workflow agent can trigger actions or pipelines.

Capabilities:

• call APIs  
• transform file formats  
• execute scripts  
• launch pipelines  
• track job progress  
• summarize outputs  

Example:

User:
run single cell analysis on selected datasets

Agent:

identify datasets  
convert formats  
launch analysis pipeline  
retrieve plots  
explain outputs  

---

## Scientific Analysis Agent

A domain-specific workflow agent.

Examples:

run Seurat analysis  
run Scanpy analysis  
generate UMAP plots  
perform clustering  
normalize expression matrices  

Requires carefully defined toolset.

---

# Capability Hierarchy

chatbot
↓
data agent
↓
workflow agent
↓
scientific analysis agent

Each layer adds capability and complexity.

---

# Typical System Architecture

Frontend:

chat interface embedded in website

Backend:

application server

LLM layer:

OpenAI API

Tool layer:

database query functions  
analysis pipeline functions  

Compute layer:

Python or R environment  
job scheduler  

Storage layer:

database  
object storage  

---

# Example Query Workflow

User:

which sequencing runs include libraries derived from colon samples?

Process:

interpret biological meaning  
determine lineage traversal  
generate SQL  
retrieve results  
summarize results  

---

# Example Workflow Operation

User:

run single cell analysis on selected datasets

Process:

identify datasets  
validate formats  
convert files  
launch analysis pipeline  
generate plots  
return results  

---

# OpenAI Products Relevant to This Use Case

## Models

LLMs capable of reasoning and code generation.

Docs:

https://platform.openai.com/docs

---

## Responses API

Primary interface for conversational applications.

Features:

multi-turn conversation  
tool calling  
structured outputs  

Docs:

https://platform.openai.com/docs/api-reference/responses

---

## Function Calling

Allows model to call developer-defined tools.

Examples:

query_database  
run_pipeline  
convert_format  

Docs:

https://platform.openai.com/docs/guides/function-calling

---

## Agents Concepts

Supports multi-step reasoning with tools.

Docs:

https://platform.openai.com/docs/guides/agents

---

## Retrieval Tools

Allows semantic search across documents.

Docs:

https://platform.openai.com/docs/guides/retrieval

---

## Code Execution Patterns

Models generate code executed by backend systems.

Docs:

https://platform.openai.com/docs/guides/code

---

## Enterprise Offerings

Secure organizational deployments.

Docs:

https://openai.com/chatgpt/enterprise

---

# Cost Estimation

Cost depends on:

1. number of user interactions
2. token usage per interaction
3. compute workload for pipelines

---

## Token Usage

1 token ≈ 0.75 words

Typical interaction:

1k–5k tokens

Pricing:

https://openai.com/pricing

---

## Example Monthly Estimate

Assume:

2k tokens per interaction  
1000 interactions per month  

Total:

2 million tokens per month

Typical cost:

tens of dollars per month depending on model.

---

## Workflow Compute Costs

Pipeline compute may dominate cost.

Examples:

single-cell analysis  
large datasets  
plot generation  

Compute cost drivers:

dataset size  
runtime duration  
memory requirements  

---

# Budget Planning Steps

Step 1

estimate number of users

Step 2

estimate questions per user

Step 3

estimate tokens per question

Step 4

estimate number of workflow executions

---

# Suggested Development Path

Phase 1

chatbot answering schema questions

Phase 2

data agent generating SQL

Phase 3

workflow agent launching pipelines

Phase 4

scientific analysis agent

---

# Key Insight

LLM cost is usually smaller than scientific compute cost.

---

# Core Principle

You are building a semantic layer over a scientific provenance graph.

The agent translates natural language into structured operations over that graph.
