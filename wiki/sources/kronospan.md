---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia, client-project]
source_url: https://github.com/Qualiasolutions/kronospan
---

# kronospan

> AI Financial Data Assistant for Kronospan — offline natural language queries against corporate financial data on NVIDIA DGX Spark hardware.

## Overview

An internal AI assistant for Kronospan (global wood-based panel manufacturer, Cyprus office) that allows employees to query corporate financial data using natural language. The system ingests structured data from Excel spreadsheets and PDF reports into a database, then uses a local LLM to translate questions into SQL queries. Designed to run entirely offline on Kronospan's NVIDIA DGX Spark hardware (128GB unified memory, Blackwell GPU).

## Tech Stack

- Python
- Local LLM (on-premise, NVIDIA DGX Spark)
- SQL database for ingested financial data
- Excel/PDF data ingestion pipeline

## Status

- **Language:** Python
- **Last updated:** 2026-04-14
- **Visibility:** Private

## Key Features

- Natural language to SQL translation for financial queries
- Ingests 6 data sources: daily deposits, group companies, working capital, long-term loans, 3rd party facilities, financial statements
- Handles Excel (up to 77K rows) and PDF financial statements
- Runs entirely offline on NVIDIA DGX Spark (128GB, Blackwell GPU)
- No cloud dependency — fully on-premise for data security

## Related

- [[Qualia ERP]]
