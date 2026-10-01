# AI Customer Support Email Triage & Automation

An n8n workflow that automatically classifies incoming customer support emails, validates AI-generated results, retrieves order information, generates appropriate responses, and escalates refund requests to a human support representative.

## Overview

This project demonstrates how AI can be integrated into a business automation workflow without giving the AI direct control over business-critical decisions.

The workflow follows the principle:

> **AI interprets. n8n validates, decides, and acts.**

Incoming Gmail messages are analyzed using an LLM and converted into structured data. The structured output is then validated before the workflow performs any customer-facing actions.

## Problem

Customer support teams often receive emails covering different types of requests:

- Order status questions
- Shipping questions
- Refund requests
- Product questions
- General questions
- Non-support emails

Manually reviewing and routing every email can be time-consuming.

The goal of this automation is to:

1. Understand incoming customer emails
2. Identify the customer's intent
3. Extract relevant order information
4. Verify orders against an order database
5. Generate an appropriate response
6. Escalate refund requests to a human
7. Detect and handle invalid AI output
8. Log important events for review

---

## Workflow Architecture

```text
Gmail Trigger
      ↓
Get Email
      ↓
Get Thread
      ↓
Prepare Thread Context
      ↓
Prepare Email
      ↓
Classify Email with AI
      ↓
Structured Output Parser
      ↓
Validate AI Output
      ↓
Is AI Output Valid?
      │
      ├── FALSE
      │     ↓
      │  Handle Invalid AI Output
      │     ↓
      │  Alert Admin
      │     ↓
      │  Log Error
      │
      └── TRUE
            ↓
       Route by Intent
            │
       ┌────┼──────────────┐
       │    │              │
   Order  Refund       Shipping
   Issue  Request      Question
       │    │              │
       └────┼──────────────┘
            ↓
       Order Lookup
            ↓
       Is Order Found?
          │       │
        NO        YES
        │          ↓
   Order Not     Is Refund?
     Found       │       │
        │       YES      NO
        │        │        │
        │    Human      Generate
        │   Escalation   Order
        │        │       Response
        │        │          │
        │    Support       │
        │     Email        │
        │        │          │
        │     Log          │
        │        │          │
        └────────┴──────────┘

Product Question / General Question
              ↓
   Generate Non-Order Response
              ↓
        Send Email

OTHER
  ↓
Ignore