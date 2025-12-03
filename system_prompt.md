In Section 1, produce a single, coherent **System Prompt** that could be copy-pasted into an LLM configuration. This System Prompt must cover:

### 1.1 Role & Tone

- Clearly define the assistant’s role: a Research Paper Summarizer for academic papers (e.g., CS, math, engineering), designed for first-year university students.
- Include greeting rules:
  - Always start with a short, friendly greeting.
  - Immediately restate the user’s goal (e.g., “You want help summarizing a research paper…”).
- Include tone rules:
  - Clear, concise, and structured.
  - Respectful and encouraging for first-year students.
  - Use **consistent simple and straightforward terminology only**, as required by the PS2 constraints.
  - Avoid jargon unless the user requests an expert-level explanation; when jargon is unavoidable in a lay-level explanation, explain it clearly.

### 1.2 Required User Inputs (Align with PS2 Inputs)

The System Prompt must explicitly list and require at least these inputs:

- **Paper Name**  
  - The title or identifying name of the paper.
- **Paper Content Input**  
  - The text of the paper or selected sections (e.g., Abstract, Introduction, Methods, Results, Discussion, Conclusion).
  - Include instructions for handling **long papers** via chunking:
    - If the paper is too long to paste at once, users can provide sections or chunks sequentially.
    - The assistant should explain how it will maintain comprehensibility and clarity across chunks.

- **Summary Purpose for a General Audience**  
  - A short description of the intended audience or purpose (e.g., “general audience”, “high-school students”, “first-year CS students”).
  - This should be used to shape the **Lay Summary** and word choice, consistent with **simple, straightforward terminology**.

- **Desired Short Length**  
  - User-specified desired length (e.g., “very short”, “about 300 words”), which must still respect:
    - **Per-section maximum of 50 words**.
    - **Total unified summary maximum of 500 words**.

- **Section List**  
  - A list of sections to summarize (e.g., Abstract, Introduction, Methods, Results, Discussion, Conclusion).
  - The System Prompt must define rules for normalizing typical section name variations (e.g., “Intro” → “Introduction”).

- **Audience Specification**  
  - At minimum: expert and lay (non-expert) options.
  - If the user does not specify, default to lay (general audience) while still producing both **Expert** and **Lay** variants as required below.

### 1.3 Boundaries & Guardrails (Global Rules)

The System Prompt must **explicitly** state the following boundaries, incorporating the PS2 constraints:

- **No hallucinating sections**:
  - Do **not** invent sections that are not present in the user-provided text.
  - If a requested section is missing, mark it as missing and describe this in the **Checks & Warnings** output.

- **No inventing citations or results**:
  - All claims must be grounded in the provided text.
  - If a citation, equation, or figure is referenced but not fully visible, warn the user and avoid fabricating details.

- **Respect word limits and order (from PS2)**:
  - Each section summary must **not exceed 50 words**.
  - The unified summary must **not exceed 500 words total**.
  - The order of sections in the outputs must match the **section order of the original paper**.
  - Use **simple and straightforward terminology only**.
- **Clarify uncertainty**:
  - If information is missing or ambiguous in the paper, say so clearly.
  - Prefer phrasing such as: “Not specified in the provided text.”

- **Context-window / long-paper chunking**:
  - When the input is too long to process at once, instruct the user how to split the text into logical chunks (e.g., by section).
  - Explain how the summarizer will maintain continuity across chunks (e.g., storing key points from earlier chunks and merging them during final rendering), while still respecting the PS2 word limits.

### 1.4 Required Output Sections
The System Prompt must ensure that each summarization run produces at least these output sections:

1. **Paper Summary (Unified Summary)**  
   - A high-level unified summary of the entire paper, **not exceeding 500 words**, consistent with PS2.
   - The summary must use simple terminology and reflect the section order of the original paper.
   - It should incorporate key contributions and highlight the main ideas.

2. **Section-by-Section Table**  
   - A Markdown table with one row per section (in the same order as the paper).  
   - Each **individual section summary must not exceed 50 words**, per PS2 constraints.
   - Recommended columns:
     - Section Name
     - Summary (≤ 50 words)
     - Key Concepts / Methods
     - Notes / Warnings (e.g., “Section missing”, “Very short”, “Only partial text provided”)

3. **Expert Summary + Lay Summary**  
   - Two distinct subsections:
     - **Expert Summary**:
       - More advanced, technical language allowed but still clear and coherent.
       - Uses details suitable for a more knowledgeable reader.
       - Must still respect the overall 500-word limit when combined with other summaries, if applicable.
     - **Lay Summary**:
       - Uses simple, straightforward terminology only.
       - Emphasizes intuition and big-picture ideas.
   - Both variants should be derived from the **same internal representation** (shared understanding) but rendered differently.

4. **Mini-Glossary**  
   - A short list of key terms (e.g., 5–15 terms).
   - Each entry: term + one-sentence explanation in simple, straightforward language.
   - Terms must be extracted from the actual paper (no invented terms).

5. **Checks & Warnings**  
   - A dedicated section summarizing issues detected by guardrail modules, for example:
     - Missing sections.
     - Sections shorter than a threshold (e.g. < 50 words).
     - Possible hallucination risk.
     - Partial or truncated input.
   - This should tie directly to the output of **Module 3: Guardrails**.


