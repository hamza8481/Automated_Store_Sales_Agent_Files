Automated Store Sales Agent (WooCommerce Integration)

An advanced conversational AI agent built in n8n designed specifically for e-commerce platforms like WooCommerce. This agent handles the entire customer journey: answering pre-sales FAQs using RAG, searching for products directly in the store database, checking real-time inventory, capturing leads, generating payment links, and intelligently escalating complex issues to human agents.

Business and Operational Value
1. 24/7 Automated Support: Immediately respond to product inquiries and policy questions, driving conversion outside of regular business hours.
2. Intelligent Intent Parsing: Instead of forcing the LLM to guess, an upstream extraction node identifies exactly what the customer wants (FAQ, Product Search, Checkout, Human Help) and ensures the Agent utilizes the correct tools.
3. Real-Time Data Access: Tools natively connect to the WooCommerce API to fetch actual live pricing and stock status, eliminating hallucinations about out-of-stock items.
4. Human-in-the-Loop Escalation: Automatically routes frustrated or complex queries to a live Telegram channel for human intervention.

Tech Stack and Key Nodes
1. Framework: n8n Langchain Nodes (Agent, Memory, Info Extractor).
2. LLM Engine: OpenAI gpt-4o-mini (for Agent logic and Intent Extraction). Google Gemini for the RAG engine fallback.
3. Database and RAG: Qdrant Vector Store.
4. Store Platform: WooCommerce (Native Nodes).
5. Communication: Telegram (for escalations).
6. Knowledge Intake: Google Drive (Dynamic document ingestion pipeline).

Execution Flow
Flow 1: Knowledge Base Sync (Top flow - Manual Trigger)
1. Trigger: User clicks Execute.
2. Clear DB: An HTTP request wipes the Qdrant sales_docs collection to prevent duplicates.
3. Ingest and Embed: N8n fetches all policy PDFs and documents from a designated Google Drive folder, splits them into tokens, generates OpenAI embeddings, and inserts them into Qdrant.

Flow 2: Live Customer Chat (Bottom flow - Chat Trigger)
1. Intake: Customer sends a message via the web chat widget.
2. Extraction: An LLM reads the message and outputs a strict JSON schema marking true/false for intents (product, faq, payment, lead) and extracting entity parameters (sku, keyword, max_price).
3. Agent Routing: The core AI Agent receives the extracted context and chooses tools based on intent:
   a. RAG_FAQ: Looks up policies in Qdrant.
   b. PRODUCT_SEARCH_WOO and INVENTORY_DETAIL_WOO: Searches WooCommerce using the extracted keywords/prices.
   c. CRM_LEAD and PAYMENT_LINK: Delegates to dedicated sub-workflows.
   d. HUMAN_ESCALATION: Sends a Telegram message to store staff.
4. Output: The agent formulates a professional response and presents clear next-step options to the customer.
