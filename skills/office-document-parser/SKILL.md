---
name: office-document-parser
description: "Parses Office documents (Word, Excel, PowerPoint) into structured Markdown text."
---

# Office Document Parser Skill

Use this skill when the user asks you to read, analyze, or extract information from an Office document, specifically files with the following extensions: `.docx`, `.xlsx`, `.xls`, `.pptx`.

This skill provides a Python script that will read the Office file, extract the structured data (including text, headings, tables, and slide notes), and output it as Markdown. The script will automatically handle installing its required Python dependencies (`pandas`, `openpyxl`, `python-docx`, `python-pptx`, `tabulate`) if they are missing.

## Instructions

When the user provides a path to an Office document, follow these steps:

1. **Check Python Environment**: This skill requires Python. When executing the script, if it fails with an error indicating `python` was not found, you MUST proactively inform the user that their environment lacks Python. Ask them to either install Python or provide the specific absolute path to their Python executable (e.g., `D:\Software\Python\Python313\python.exe`) if it is installed but not in PATH. Do not assume the script will work across different user environments without a valid Python setup.
2. **Locate the parser script**: The parser script is located at `scripts/parse_office.py` relative to this `SKILL.md` file (its absolute path is `C:\Users\ZY.Li\.gemini\config\plugins\my-custom-plugin\skills\office-document-parser\scripts\parse_office.py`).
3. **Execute the script**: Use the `run_command` tool to execute the script using the Python interpreter (or the custom path provided by the user), passing the absolute path of the target Office file as an argument.
   ```bash
   python "C:\Users\ZY.Li\.gemini\config\plugins\my-custom-plugin\skills\office-document-parser\scripts\parse_office.py" "PATH_TO_OFFICE_FILE"
   ```
4. **Wait for output**: The script will print the Markdown representation of the file to the standard output. If the script was sent to the background as a task, wait for it to complete and read its output message.
5. **Handle Output**:
   - For small files, read the output directly from the terminal.
   - For large files (especially Excel files which can generate huge markdown), redirect the output to a temporary file. For example, run: `... | Out-File -Encoding utf8 "temp\temp_parsed.md"`, and then read it using `view_file`.
6. **Analyze the Markdown**: Read the Markdown output to understand the contents and structure of the document, and proceed to answer the user's request.
7. **Cleanup Temporary Files (CRITICAL)**: Once finished reading, you MUST delete the temporary file (e.g., `rm temp\temp_parsed.md`) to save disk space and maintain a clean workspace.
8. **Handle Errors**: 
   - If the script fails due to missing dependencies, try running `pip install pandas openpyxl python-docx python-pptx tabulate xlrd` manually, then retry.
   - For Excel files, if pandas fails with C-extension errors (like `pandas._libs`), you can fallback to writing a quick Python script using only `openpyxl` to extract the data to Markdown.
