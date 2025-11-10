#  **Professional RAG Document Q&A Agent Starter Kit**

This project provides a single-page, production-ready **Retrieval-Augmented Generation (RAG) Agent** designed for students to learn how to build and secure a **Level 1: Connected Problem-Solver**.  
It demonstrates how to integrate a Large Language Model (LLM) with proprietary data using **Firebase/Firestore** for memory.

---

##  **Architectural Breakdown: The Agentic Loop**

The application is structured around the core components of an AI Agent (**Model**, **Tools**, **Orchestration**) that work in a continuous **Think → Act → Observe** loop to answer a user's question *only from the provided document*.

---

###  **1. The Model (The "Brain")**

This layer provides the agent's core intelligence and reasoning.

| Component | Technology | Role in the Agent |
|------------|-------------|------------------|
| **Reasoning Model** | Gemini 2.5 Flash | Handles summarization, synthesis, and tone control. Follows the strict System Prompt to generate concise, grounded answers. |
| **Embedding Model** | models/embedding-001 | Converts text (chunks and queries) into numerical vectors, enabling semantic search for RAG retrieval. |

---

###  **2. The Tools (The "Hands")**

The tools connect the Model's intelligence to the external world (the document and the database).

| Tool | Action & Purpose | Why It's Needed |
|------|------------------|----------------|
| **Document Loaders** | pdf.js & mammoth.js | Extracts raw text from complex file formats (PDF, DOCX). Enables the agent to process external data. |
| **Vector Store** | Firestore | Acts as long-term memory. Stores text chunks and vectors for persistent semantic retrieval. |

---

###  **3. The Orchestration Layer (The "Nervous System")**

This is the JavaScript logic that manages the workflow, state, and security boundaries.

**Responsibilities:**
- **State Management:** Tracks `userId`, `isIndexed`, and `currentDocumentId`.  
- **API Management:** Uses `fetchWithRetry` with exponential backoff for network reliability.  

**Critical Guardrails:**
- **Relevance Check:** Filters chunks by cosine similarity > 0.7 to prevent irrelevant answers.  
- **Refusal Command:** Forces the LLM to output a refusal phrase if no valid context exists.  

---

##  **RAG Processing Pipeline: The Two Phases**

---

### **Phase A: Ingestion (When the user clicks “Index Document”)**

Transforms the uploaded document into a searchable format for Firestore.

| Step | Component | Action |
|------|------------|--------|
| **1. Load & Split** | File Libraries + Simple Splitter | Extracts text and splits it into chunks (1000 chars with 200 overlap). |
| **2. Embed** | Gemini Embedding API | Converts each chunk into a high-dimensional vector. |
| **3. Store** | Firestore `setDoc` | Saves Text + Vector pairs into Firestore under the user’s path. |

---

### **Phase B: Query (When the user clicks “Send”)**

Executes the **Retrieve → Augment → Generate (RAG)** loop.

| Step | Component | Action |
|------|------------|--------|
| **1. Query Vectorization** | Gemini Embedding API | Converts the user's question into a query vector. |
| **2. Retrieval (Search)** | Firestore + Cosine Similarity | Finds the top 5 most relevant chunks. |
| **3. Guardrail / Context Check** | Orchestration Logic | Stops if no relevant chunks found. |
| **4. Context Augmentation** | JavaScript Logic | Combines query with retrieved chunks and system prompt. |
| **5. Generation** | Gemini 2.5 Flash | Produces the grounded answer using retrieved context. |

---

##  **Setup Instructions **

Follow these steps to set up and run the project locally.

---

### **Prerequisites**
- **VS Code:** With the *Live Server* extension installed.  
- **Firebase Project:** Firestore enabled + Anonymous Sign-in active under *Authentication*.  
- **Gemini API Key:** Required for embedding and generation.

---

### **Step 1: Insert Secrets**

Open `index.html` and update the constants inside the first `<script>` tag.  
 *Do not upload your secrets to GitHub!*

```javascript
// --- 1. SECRETS PLACEHOLDERS ( MUST UPDATE THIS) ---
window.__generative_api_key = "ADD_YOUR_GEMINI_API_KEY_HERE"; 
        
const FIREBASE_CONFIG = {
    apiKey: "YOUR_FIREBASE_API_KEY_HERE", 
    authDomain: "documentqa-8b6e3.firebaseapp.com",
    projectId: "documentqa-8b6e3",
    // ... complete the rest of your Firebase config fields
};
```

---

### **Main page**
![Main Page Screenshot](https://github.com/user-attachments/assets/8127982e-ad37-4e0e-aa61-23a401fb478c)


