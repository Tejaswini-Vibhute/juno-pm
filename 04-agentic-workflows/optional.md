Architect a Reasoning Pattern to Solve Drift
Current AI System: ChatGPT with web search

Workflow: Researching a topic using multiple web searches and generating a final answer.
1. Identify the reasoning drift
ChatGPT may start with the correct user question but gradually shift focus as it retrieves information from multiple sources.

Example: A user asks for the latest 2026 AI regulations, but ChatGPT includes outdated 2024 policies and presents them as current.

Silent failure: The final answer appears accurate but contains outdated or irrelevant information without warning.
2. Select the reasoning pattern
Pattern: ReAct (Reasoning + Acting)

The AI follows a repeated cycle:

Reason → Search → Observe → Validate → Repeat

Each search result is evaluated against the original question before another search is performed.
3. Define the loop cap
Maximum: 5 search-and-validation loops.

If reliable information cannot be found within five loops, the AI must stop and disclose any unresolved gaps rather than inventing an answer.
4. Define the checkpoint
Checkpoint: Source Relevance and Freshness Validation

After each search, the system must verify:
- Does the information answer the original question?
- Is the source reliable?
- Is the information current for the requested timeframe?
- Are claims supported by retrieved evidence?
  
