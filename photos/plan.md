# TDS_project2
**AI-based Data Analyst**

This project explores different approaches to designing an AI-powered Data Analyst system, capable of answering analytical questions based on user-provided inputs such as text queries, CSVs, and images.

---

## Approach 1: Direct Code Generation
1. Provide `question.txt` and any other supporting files (CSV, image, etc.) to the LLM.
2. Ask the LLM to generate executable code and list required libraries.
3. Execute the generated code in a Python REPL.

**Problem:**  
The LLM often makes assumptions about file names and structures, leading to frequent errors and failed executions.

---

## Approach 2: Two-Step Reasoning
1. Step 1 is the same as Approach 1, but instead of full code, the LLM first generates code for **scraping or reading all available data**.
2. Step 2: Provide the **question statement** and **the processed data** back to the LLM, asking for:
   - Direct answers (if the problem is small/simple).  
   - Otherwise, code for solving the problem, which we then execute and use to generate the final output.

---

## Approach 3: Structured Metadata Pipeline (Improved Approach 2)
1. First LLM call: Generate scraping code and create a `metadata.txt` file containing:
   - Metadata about scraped data.
   - File paths and descriptions.
   - The asked questions.
   - A JSON schema for expected answers.
2. Second LLM call: Use the `metadata.txt` + data paths to:
   - Generate code for solving the problem.
   - Save or return results in the defined JSON format.

**Notes:**
- If the LLM-generated code has mistakes, allow **up to 3 correction attempts**.
- **Output:** Achieves ~60–70% accuracy. Better structured but still imperfect.

---

## Approach 4: Multi-LLM Consensus
- Issue: Gemini produces inconsistent results for some queries.  
- Possible solution: Run **multiple LLM calls** and choose the best result.  
- **Drawback:** Significantly higher token usage → inefficient.

---

## Approach 5: Interactive Debugging with Chat Sessions (Final Approach)
- Idea: Mimic how humans debug code with LLMs.  
- Workflow:
  1. Ask the LLM to generate code.  
  2. If errors occur → send **only the error messages** (not the full question) for corrections.  
  3. Maintain a **single chat session** for step-by-step corrections.  
  4. If output files are empty or incorrect (bad logic), re-ask with clarification.  

**Advantages:**
- Efficient correction loop (focuses only on errors, not full context).  
- Uses fewer tokens (no need to resend the entire question every time).  
- Works especially well with newer, less hallucination-prone models.  

**Output:**  
This approach proved to be the most reliable, achieving **~100% accuracy**.

---

## Key Takeaways
- **Approach 1**: Too error-prone.  
- **Approach 2**: Adds structure, but still limited.  
- **Approach 3**: Metadata improves organization and reproducibility.  
- **Approach 4**: Overkill due to high token cost.  
- **Approach 5**: Best balance of accuracy, efficiency, and scalability.

👉 **Final Strategy:** Use **Approach 5 (Chat Session Debugging)** as the primary method, with fallback to metadata-based reasoning (Approach 3) when necessary.

