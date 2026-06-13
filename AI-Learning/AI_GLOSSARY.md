# AI Developer Glossary

When designing your AI infrastructure and working with Large Language Models (LLMs) like GPT-4, Gemini, or Llama, there is a specific vocabulary used by AI engineers. This document outlines the core terminology you will use when building AI features into your ERP.

---

## 1. Core Terminology: Talking to the Model

Providing information, instructions, or background data to an LLM such as GPT or Gemini is commonly called **prompting** or **providing context**. Depending on the technique, there are more specific terms:

*   **Prompt:** The raw text you send to the AI model.
*   **Context:** Additional information or data included alongside the prompt to help the model answer accurately without hallucinating.
*   **System Prompt:** High-level, underlying instructions that define the model's core behavior, personality, and rules (e.g., *"You are a polite virtual assistant for Kaksha+ ERP. Never invent data."*).
*   **User Prompt:** The actual request or question typed by the end-user (e.g., *"Generate homework for Class 8 Science."*).
*   **Context Window:** The total amount of text (instructions, conversation history, documents, etc.) the model can "hold in its brain" and consider at one time. If you exceed the context window, the model forgets the beginning of the conversation.

---

## 2. Advanced Prompting Techniques

*   **In-Context Learning (ICL):** Teaching the model how to perform a task simply by giving it examples inside the prompt itself, rather than re-training the model's neural network.
*   **Zero-Shot Prompting:** Asking the model to perform a task without giving it any prior examples of how to do it.
*   **Few-Shot Prompting:** Providing a few examples of the desired input/output format before asking the model to perform the task.
*   **Retrieval-Augmented Generation (RAG):** The process of automatically searching a database (or a vector database of documents), retrieving the relevant information, and injecting it into the prompt *before* sending it to the LLM.
*   **Grounding:** Providing trusted, external factual information so the model's answer is based on specific, verifiable facts rather than just the generic knowledge it was trained on.
*   **Prompt Engineering:** The technical practice of structuring and designing effective prompts, context, and instructions to get the highest quality, most reliable output from the LLM.

---

## 3. Enterprise Architecture Terminology

For AI systems in your ERP or AI layer architecture, the data sent to the LLM is often referred to as:
*   **Prompt Context**
*   **Grounding Context**

In enterprise AI architecture, the backend process of collecting syllabus data, student records, ERP data, and school policies, and dynamically attaching it to the user's prompt is known as:
*   **Context Enrichment**
*   **Prompt Augmentation**

These are the precise terms you will commonly use when designing your AI Infrastructure Platform.

### Example: Context Enrichment in Action

When a user asks: *"Generate homework for Class 8 Science"*, your backend automatically enriches the prompt with the student's specific ERP context before sending it to the LLM.

```json
{
  "systemPrompt": "You are a school ERP assistant. Generate homework based strictly on the provided context.",
  "userQuestion": "Generate homework for Class 8 Science.",
  "context": {
    "board": "CBSE",
    "standard": "8",
    "subject": "Science",
    "chapter": "Force and Pressure",
    "learningObjectives": [
      "Understand pressure",
      "Differentiate force and pressure"
    ]
  }
}
```
By performing **Context Enrichment**, the LLM generates a perfectly tailored homework assignment based on the CBSE syllabus for that specific school, rather than a generic answer.
