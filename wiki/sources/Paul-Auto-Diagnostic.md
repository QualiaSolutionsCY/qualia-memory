---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/Paul-Auto-Diagnostic
---

# Paul-Auto-Diagnostic

> Professional OBD2 automotive diagnostic tool with AI-powered analysis

## Overview

OBD2 Diagnostic Pro is a web-based automotive diagnostic platform that connects directly to vehicles via USB/Bluetooth OBD2 adapters. It provides real-time sensor monitoring, DTC code analysis, and APS calibration tools, enhanced by AI-powered diagnostics via Anthropic Claude and OpenAI GPT-4 for repair recommendations and cost estimation.

## Tech Stack

- Python 3.11+ (Flask)
- SQLAlchemy (PostgreSQL/SQLite)
- PySerial / Python-OBD (hardware communication)
- Anthropic Claude API (primary AI diagnostics)
- OpenAI GPT-4 (fallback AI provider)
- Puppeteer (HTML frontend)
- PyPDF2, Pillow, PyMuPDF

## Status

- **Language:** HTML
- **Last updated:** 2026-01-06
- **Created:** 2025-07-22
- **Visibility:** Private

## Key Features

- Direct OBD2 connection via USB or Bluetooth (ELM327 adapters)
- Read, analyze, and clear Diagnostic Trouble Codes (DTCs)
- Real-time live sensor data monitoring with interactive charts
- AI-powered diagnostic analysis and repair recommendations
- APS (Accelerator Pedal Position Sensor) calibration tools
- Multi-vehicle database with session history
- Simulation mode for development without hardware
- Cost estimation for DIY and professional repairs

## Related

- [[Qualia Solutions]]
