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
2. Sequence of Operations
A step-by-step view of a single request lifecycle.

Code snippet
sequenceDiagram
    participant U as User
    participant G as Gateway (Streamlit)
    participant P as Privacy Engine
    participant AI as Groq API
    
    U->>G: "Draft email to John Doe regarding Project Apollo"
    G->>P: Analyze & Anonymize
    P-->>G: "Draft email to <PERSON> regarding <CUSTOM_JARGON>"
    Note right of G: Original PII is stored locally in memory map
    G->>AI: Send Masked Prompt
    AI-->>G: "Here is a draft for <PERSON> about <CUSTOM_JARGON>..."
    G->>G: Unmask (Restore "John Doe", "Project Apollo")
    G-->>U: Final Safe Response
⚙️ How It Works
Interception: The user inputs text or uploads a document (PDF/DOCX).

Masking: The Bodyguard (Microsoft Presidio + Custom Regex) scans the text.

Input: "My SSN is 123-45-6789."

Masked: "My SSN is <US_SSN>."

Processing: The masked text is sent to the Groq API (Llama 3.3).

Re-Identification: The AI's response is intercepted, and the placeholders (<US_SSN>) are swapped back to the original values locally.

🚀 How to Test on Your Own
Follow these steps to run the gateway on your local machine.

Prerequisites
Python 3.8 or higher.

A free API Key from Groq Console.

Installation
Clone the repository:

Bash
git clone [https://github.com/YOUR-USERNAME/secure-ai-gateway.git](https://github.com/YOUR-USERNAME/secure-ai-gateway.git)
cd secure-ai-gateway
Install dependencies:

Bash
pip install streamlit groq presidio-analyzer presidio-anonymizer python-dotenv pypdf python-docx pandas
python -m spacy download en_core_web_lg
Configure Security:

Create a file named .env in the main folder.

Add your API key inside:

Code snippet
GROQ_API_KEY=gsk_your_key_here
Note: Never share this file!

Run the App:

Bash
streamlit run gateway.py
⚠️ Disclaimer & Known Issues
Current Status: Alpha / Proof of Concept

This tool is a prototype designed to demonstrate Data Loss Prevention (DLP) concepts. It is not yet production-ready.

Bugs: You may encounter issues with specific file formats or edge-case text inputs.

Context Loss: The AI may occasionally struggle with context if too much data is redacted (e.g., gender pronouns might be mismatched).

False Positives: The rigid Regex patterns for SSNs/Credit Cards may sometimes flag non-sensitive numbers.

Use at your own risk. Do not use with critical production data without further testing.

🔮 Future Roadmap
I am actively developing this tool to include:

[ ] Synthetic Data Replacement: Using Faker to replace placeholders with realistic fake data for better AI context.

[ ] OCR Support: Redacting sensitive text from images and screenshots.

[ ] Chat History: Enabling multi-turn conversations with memory.

[ ] Docker Support: Full containerization for easy deployment.

🤝 Contributing
Constructive feedback and Pull Requests are welcome! If you find a bug, please open an issue.
