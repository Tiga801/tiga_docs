---
description: Intelligently merge multiple Markdown documents using the first as base
argument-hint: base.md supp1.md [supp2.md ...]
allowed-tools: Read, Write, Glob, Grep, Bash
---

# Supplement Docs — Intelligent Markdown Merge

You are performing an intelligent merge of multiple Markdown documents. The first file is the **base document** (authoritative), and all subsequent files are **supplement documents** whose new content should be added to the base. The output is a new file — the originals are never modified.

## Input

The arguments are provided in `$ARGUMENTS`. They are space-separated file paths. If a path contains spaces, it will be quoted.

## Procedure

### 1. Validate Input

- Parse `$ARGUMENTS` into a list of file paths.
- There must be **at least 2** paths. If fewer, print an error message explaining usage and stop.
- Verify every file exists and is readable (use the `Read` tool). If any file is missing, report which file(s) and stop.
- If any supplement file is empty (no meaningful content), note it in the final report and skip it.

### 2. Analyze the Base Document

Read the base document thoroughly and extract:

- **Heading hierarchy**: all headings with their levels (`#`, `##`, `###`, etc.) and the line ranges they span.
- **Section structure**: a tree of sections so you know where each section starts and ends.
- **Format conventions**: list bullet style (`-` vs `*` vs `+`), numbered list style, code block fence style (backtick vs tilde), indentation (spaces vs tabs, width), blank-line patterns between sections.
- **Language**: detect the primary language (Chinese, English, mixed, etc.) so new content matches.

### 3. Analyze Each Supplement Document

For every supplement document, read it and for **each discrete piece of content** (a section, a paragraph, a list item cluster, a code block, etc.) determine:

- Its **topic / subject**.
- Whether **equivalent content already exists** in the base document (semantic deduplication — not simple string matching). Two passages are equivalent if they convey the same information even if worded differently.
- Which base-document section it is most closely related to (if any).

### 4. Create a Merge Plan

For each piece of supplement content, assign exactly one action:

| Action | When to use |
|--------|------------|
| **SKIP** | The base document already contains equivalent information. |
| **APPEND** | The content adds new information to an existing section. Insert it at the end of that section, before the next heading. |
| **ENHANCE** | The content enriches or extends an existing item (e.g., adds a sub-bullet, an example, a note). Insert it inline right after the related item. |
| **CREATE** | The content introduces an entirely new topic. Create a new subsection placed next to the most related existing section. If no section is related, append at the end of the document. |

If two supplement documents contribute content to the same section, merge them in the order the supplement files were listed.

### 5. Execute the Merge

Apply the merge plan while strictly following these rules:

- **Additive only**: never delete, rewrite, reorder, or rephrase any content from the base document. The base document's text must appear verbatim in the output.
- **Format matching**: new content must use the same list-bullet style, heading-level conventions, indentation, blank-line patterns, and language as the base document.
- **Natural insertion points**: append content at section boundaries (just before the next heading of equal or higher level). Do not insert in the middle of a paragraph or list unless performing an ENHANCE.
- **Heading levels**: if creating a new section, choose a heading level consistent with the surrounding structure. A new peer section gets the same level; a new sub-topic gets one level deeper.
- **No trailing whitespace or extra blank lines** beyond what the base document's style uses.

### 6. Write the Output File

- Derive the output filename from the base document's name by inserting `_merge` before the `.md` extension.
  - `guide.md` → `guide_merge.md`
  - `CLAUDE.md` → `CLAUDE_merge.md`
  - `my.notes.md` → `my.notes_merge.md`
- Write the output file to the **same directory** as the base document.
- Use the `Write` tool to create the file. Do **not** modify the base or any supplement file.

### 7. Report Results

After writing the file, print a clear summary report in this format:

```
## Merge Report

**Base document**: <path>
**Output file**: <path>

### Per-supplement summary

#### <supplement file 1>
- Merged: N items
- Skipped (duplicate): N items
- Sections affected: <list>
- New sections created: <list or "none">

#### <supplement file 2>
...

### Potential conflicts
- <list any cases where supplement content partially overlapped or contradicted base content, or "None detected">
```

## Edge Cases

- **No new content in any supplement**: write the output file as an exact copy of the base and note "No new content found" in the report.
- **Conflicting information**: if a supplement states something that contradicts the base, keep the base version unchanged, add the supplement version in the appropriate location with a note comment `<!-- MERGE NOTE: This content from <supplement file> may conflict with existing content above -->`, and flag it in the report.
- **Deeply nested headings**: respect the base document's maximum heading depth. Do not create headings deeper than the base uses unless absolutely necessary.
- **Non-Markdown content in files**: treat the files as Markdown regardless. If they contain non-standard syntax (HTML blocks, front matter, etc.), preserve it as-is.

## Important

- Think carefully about semantic equivalence. Two items describing the same feature in different words should be treated as duplicates.
- When in doubt about where to place content, prefer appending at the end of the most related section over creating a new section.
- Keep the merge deterministic: given the same inputs, the output should be the same.