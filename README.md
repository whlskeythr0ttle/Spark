Texas Legal Compliance AI Agent

An autonomous reasoning framework built on Google Gemini to verify legal documents against Texas State Law (TRCP, Codified Statutes, Constitution) and Federal Case Law.

This repository is designed for Agentic Access. It provides specialized toolsets and an optimized orchestrator that allows a Large Language Model (LLM) to perform iterative, multi-step legal research without hallucinating case law.

🧠 Core Philosophy: Agentic Chaining

This system relies on the ReAct (Reasoning + Acting) framework. Agents interacting with this repository should use the provided tools to create "chain effects" rather than attempting to answer legal queries from static weights.

The Expected Reasoning Chain:

Extraction: Scan the input document for explicit citations (e.g., "410 U.S. 113") or procedural claims (e.g., "Dismissal under TRCP 21").

Case Law Discovery (search_courtlistener_cases): Search the CourtListener Free Law Project database to retrieve the foundational cluster_id of a cited case.

Critical Link / Deep Reading (get_case_opinion_text): Immediately feed the retrieved cluster_id into the opinion extraction tool. Do not skip this step. You must read the actual holding to verify the document's claim.

Statutory Verification (query_texas_statutes): Cross-reference procedural mechanisms mentioned in the document against the local Texas Statutes and TRCP database.

Synthesis: Generate a final, Markdown-formatted compliance report detailing factual discrepancies, procedural defects, and verified citations.

📂 Repository Architecture

agent_orchestrator.py: The core reasoning engine. Contains the heavily engineered XML-based system prompt that binds the tools to Gemini 1.5 Pro and enforces the strict zero-hallucination persona.

courtlistener_tools.py: Contains robust, resilient API clients for the Free Law Project. Features automatic retry logic for rate limits and HTML-stripping to optimize LLM context windows.

texas_law_tools.py: A simulated local RAG (Retrieval-Augmented Generation) system utilizing fuzzy matching (difflib) to retrieve exact text from Texas codified law and the Texas Rules of Civil Procedure.

main.py: The CLI entry point for executing document reviews.

⚙️ Setup and Installation

Install Dependencies:

pip install -r requirements.txt


Set Environment Variables:
The agent requires access to Google Gemini and the CourtListener API.

export GEMINI_API_KEY="your_google_gemini_api_key_here"
export COURTLISTENER_API_TOKEN="your_courtlistener_token_here"


(Note: Free tier CourtListener accounts may be subject to strict rate limits. The tools in this repo have built-in retry logic to handle standard 429 Too Many Requests errors.)

🚀 Usage

You can run the agent directly from the command line.

Run with the default sample document:

python main.py


Run with a custom legal document:

python main.py --file path/to/your/legal_document.txt


🛡️ Guardrails and Output

The orchestrator enforces strict operational constraints:

Zero Hallucination: The agent is explicitly forbidden from guessing. If a tool fails to retrieve a case or statute, the agent must report the claim as unverified.

Structured Output: All outputs will be structured into four distinct sections: Executive Summary, Procedural & Statutory Audit, Case Law Verification, and Defects & Recommendations.
