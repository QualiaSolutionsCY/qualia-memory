---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/Squarespace-Invoice-Generator
---

# Squarespace-Invoice-Generator

> Automated invoice generation and printing system for Squarespace orders

## Overview

A production-grade automation tool that polls the Squarespace API for new e-commerce orders, generates professional PDF invoices using Puppeteer, and sends them to a configured printer automatically. Includes email notifications, health monitoring, and PM2-based process management.

## Tech Stack

- Node.js 18+
- Puppeteer (PDF generation)
- Squarespace Commerce API
- PM2 (process management)
- SMTP (email notifications)

## Status

- **Language:** JavaScript
- **Last updated:** 2026-01-06
- **Created:** 2025-07-02
- **Visibility:** Private

## Key Features

- Automated polling of Squarespace API for new orders
- Professional PDF invoice generation via Puppeteer
- Automatic printing to configured printers
- Email notifications on errors and failures
- Health monitoring endpoint (`/health`)
- Graceful shutdown and PM2 production deployment
- Automatic retries with exponential backoff
- Structured logging with file rotation

## Related

- [[Qualia Solutions]]
