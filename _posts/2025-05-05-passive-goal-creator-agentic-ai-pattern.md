---
title: "Passive Goal Creator: An Agentic Design Pattern for Turning Vague Wishes into Actionable Plans"
---

_How can an AI agent turn a hazy user intention—“organise my photos” or “plan a weekend getaway”—into a structured sequence of tasks it can actually execute?_  
The **Passive Goal Creator** pattern offers a repeatable architectural answer. It captures partial, and often ambiguous, human intent, enriches it with surrounding context, and hands a clarified goal to downstream planning components without overwhelming the user with follow-up questions.

This article unpacks the pattern’s motivation, internal mechanics, trade-offs, and real-world applications.

----------

### 1. Why “Passive” Matters in Goal Capture

Traditional chatbots work reactively: they wait for a fully-formed instruction before doing anything. In the agentic era, users may provide only fragments of a goal (“finish the quarterly report” or “improve my fitness”), and they expect the system to _infer_ what they mean.

A **Passive Goal Creator** listens to the user’s direct input _and_ quietly pulls in extra signals — recent conversations, files in progress, preferred tools, calendar events, or even environmental data — to refine the objective. Because this happen behind the scenes, the experience feels effortless, lowering the cognitive load on the user.

Researchers label the core design tension _underspecification_: humans rarely supply enough detail for an LLM-powered agent to act unambiguously . A passive component mitigates that gap by:

-   retrieving relevant workspace artefacts (documents, code, design files);
-   adding positive and negative examples from prior tasks;
-   pulling recent tool usage to guess preferred workflows;
-   merging it all into a richer prompt for the planner.
    

If a proactive counterpart is the agentic equivalent of an extrovert — actively probing sensors, sending questionnaires — then the passive version behaves more like an attentive librarian, quietly gathering material already at hand.

----------

### 2. Architectural Fit inside an Agent System

Passive Goal Creator sits directly behind the user dialogue interface.
![Image](https://pbs.twimg.com/media/GqKNRveWQAEXTUl?format=jpg&name=large)



1.  **User Interface** – captures free-form text, voice, or multimodal inputs.
    
2.  **Passive Goal Creator** – enriches the prompt with contextual snippets.
    
3.  **Memory / Knowledge Base** – long-term storage the creator can query.
    
4.  **Prompt/Response Optimiser** – standardises the enriched goal into the format the downstream planner or single-/multi-path plan generator expects.
    

The output is subsequently decomposed by a **Single-Path Plan Generator** or **Multi-Plan Generator**, revised through **Reflection** patterns, and executed via tool calls or external workers.

----------

### 3. Forces and Trade-offs

### Forces and Trade-offs

| Force                     | Why it Matters                                                                 | Passive Strategy                                                                 | Trade-off                                                                 |
|---------------------------|---------------------------------------------------------------------------------|----------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| **Ambiguity vs. Intrusion** | Too many clarifying questions frustrate users; too few lead to wrong plans.    | Mine existing context silently.                                                 | Risk of misinterpretation if context is stale.                            |
| **Context Window Limits** | LLMs cannot absorb arbitrarily large prompts.                                  | Curate only high-yield snippets (recent edits, metadata).                       | Omitted details may be crucial.                                           |
| **Privacy & Governance**  | Sensitive files or conversations may be pulled into the prompt.                | Implement retrieval filters and consent policies.                               | Extra engineering cost; possible compliance overhead.                     |
| **Performance Overhead**  | Wide searches across memory slow responsiveness.                               | Prioritise in-cache or recent artefacts; async retrieval pipelines.             | May deliver partial context in first draft, refine later.                 |


----------

### 4. Implementation Guide


A typical agent pipeline leverages _Retrieval-Augmented Generation_ (RAG). The Passive Goal Creator chooses narrow RAG queries such as:

-   “Documents modified in the past 7 days by this user”
-   “Tool invocations tagged project-X”
-   “Conversation turns where sentiments indicate frustration”
    

Lightweight scoring (BM25, cosine similarity on embeddings) ranks snippets; the top-k are concatenated with the raw user prompt.

LLM experiments show that grouping details under stable headings improves reasoning consistency. The enriched context is wrapped with explicit sections to help the planner reason:

```
## User Goal {original_prompt} 
## Additional Context  
-  Recent Files: {list} 
- Active Tools: {list} 
- Positive Examples: {snippets} 
- Constraints: {detected}
```
After planning and execution, the creator can store feedback—successful tasks, negative corrections—to enrich future rounds, closing a self-improvement loop alongside **Self-Reflection** patterns.


----------

### 5. Example Scenario: Drafting a Research Proposal

1.  **Prompt**: “Help prepare the introduction for my grant application.”
    
2.  **Passive Retrieval** pulls:
    
    -   the latest outline file,
        
    -   reviewer comments on prior drafts,
        
    -   citation list exported from Zotero,
        
    -   meeting notes mentioning key hypotheses.
        
3.  **Enriched Goal** is forwarded to the planner, which decomposes tasks:
    
    -   synthesise context paragraph,
        
    -   insert citations,
        
    -   highlight novelty against prior work.
        
4.  **Self-Reflection** reviews the draft, notices missing funding-agency keywords, and loops back.
    
5.  **Human Reflection** confirms the final draft.
    

The user never had to attach files manually—the passive layer inferred relevance from recent activity.

----------

### 6. Quality and Reliability Considerations

-   **Explainability** – Present users with a summary of which artefacts influenced the goal so they can tweak or remove mis-matched context.
    
-   **Fallback Paths** – If confidence scores on context relevance drop below a threshold, escalate to proactive questioning.
    
-   **Logging** – Store retrieval decisions for audit, supporting accountability when outcomes are challenged.
    

----------

### 7. Case Studies in the Wild

-   **IDE Coding Assistants** such as GitHub Copilot increasingly surface snippets from the open file set or recent error logs to guess intent, embodying a lightweight passive goal capture loop.
    
-   **Email-centric Task Managers** automatically group incoming messages, attached documents, and calendar invites into tasks, then ask a single confirmation before scheduling work.
    
-   In **enterprise knowledge workers**, internal prototypes at consulting firms monitor SharePoint activity to assemble client deliverable outlines before consultants even open the project template.
    

These deployments echo the pattern’s promise: translate messy human workflows into agent-readable goals without a barrage of interrupting questions.

----------

### 8. When _Not_ to Use a Passive Goal Creator


| Anti-Pattern                                | Why It Fails                                                                                   |
|---------------------------------------------|------------------------------------------------------------------------------------------------|
| **High-stakes, zero-tolerance domains**     | Silent assumptions may breach regulation or safety constraints; explicit confirmation is mandatory. |
| **No reliable memory substrate**            | If documents and context are scattered across silos, the retrieval step becomes brittle.       |
| **User privacy overrides**                  | Some organisations prohibit hidden scanning of files; a proactive, consent-based approach is safer. |

----------

### 9. Synergy with Other Patterns

-   **Prompt/Response Optimiser** – Ensures the enriched prompt follows templates that later tools parse correctly.
    
-   **Single-Path Plan Generator** – Benefits from clearer goals, reducing hallucination risk in step decomposition.
    
-   **Self-Reflection & Cross-Reflection** – Detect and repair misinterpretations the passive layer might have introduced.
    
-   **Tool/Agent Registry** – Supplies metadata on available tools, allowing the passive component to include capabilities in the goal description (“Image-diff available”, “PDF-parser ready”).
    

By viewing the Passive Goal Creator as one cog in a larger pattern toolbox, architects can mix and match elements to meet specific reliability or transparency targets.




## Key Takeaways

-   The **Passive Goal Creator** pattern enriches vague user prompts with readily available context, reducing friction while improving plan quality
-   It balances ambiguity resolution against privacy, performance, and explainability concerns.
-   Coupling passive capture with proactive clarification, reflection patterns, and standard prompt optimisation yields robust, user-friendly agentic systems.
    

By embracing this pattern, designers can let users speak naturally — then watch the agent quietly gather what it needs to turn wishes into working plans.
