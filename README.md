# 🛡️ Secure GenAI Gateway (Data Loss Prevention)

A privacy-preserving interface that allows secure usage of public Large Language Models (LLMs) by sanitizing sensitive data locally before it leaves the network.

**Built for:** Security Analysts, SOC Teams, and Privacy-Conscious Developers.

![Security Shield](https://img.shields.io/badge/Security-Data%20Loss%20Prevention-blue) ![Python](https://img.shields.io/badge/Python-3.9%2B-green) ![Status](https://img.shields.io/badge/Status-Prototype-orange)

## 🕵️‍♂️ What It Does

This tool acts as a **"Man-in-the-Middle" security proxy** between the user and the AI provider (Groq/Llama 3). It automatically detects, redacts, and replaces sensitive information (PII/SPII) so that the AI model never sees the real data.

### Key Features
* **Local Sanitization:** SSNs, Credit Cards, Names, and Emails are masked on your device.
* **Custom "Jargon" Filters:** Define and block company-specific secret terms (e.g., "Project Apollo").
* **Audit Logging:** Logs all masking events to a JSON file for compliance (GDPR/NIST) without storing the raw secrets.
* **Input Validation:** "The Bouncer" module blocks Prompt Injection attacks and DoS attempts.
* **Secure Architecture:** Decouples the Data Plane (your secrets) from the Intelligence Plane (the AI).

---

## 🏗️ Technical Architecture

### 1. Data Flow Diagram
This flowchart illustrates how sensitive data is intercepted and sanitized before reaching the cloud.

```mermaid
graph TD
    User[👤 User Input] -->|Raw Text| Bouncer[🛑 Input Validation]
    Bouncer -->|Valid Text| Bodyguard[🕵️ Presidio Analyzer]
    Bodyguard -->|Detects PII| Anonymizer[🎭 Anonymizer Engine]
    Anonymizer -->|Masked Text e.g. <PERSON>| Cloud[☁️ Groq API / Llama 3]
    Cloud -->|AI Response with Placeholders| Unmasker[🔓 Re-Identification]
    Unmasker -->|Restores Original PII| Final[✅ Secure Output]
    
    subgraph "Local Machine (Secure Zone)"
    Bouncer
    Bodyguard
    Anonymizer
    Unmasker
    end
    
    subgraph "External Cloud"
    Cloud
    end
