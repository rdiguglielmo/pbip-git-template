# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this project is

This is a **Power BI Project (PBIP)** — Microsoft's open, source-control-friendly format for Power BI. Instead of a binary `.pbix` file, the project is stored as a folder of human-readable JSON and TMDL files that can be versioned with git and edited with a text editor.

The primary tool for building and previewing reports is **Power BI Desktop**, which reads and writes the PBIP format natively. Text editing in VS Code is used for targeted changes (DAX, themes, layout tweaks) and for AI-assisted work.

## Project structure

```
*.pbip                          — project entry point, open this in Power BI Desktop
*.Report/
  definition/
    report.json                 — report-level settings and theme references
    pages/<id>/
      page.json                 — page metadata (display name, size)
      visuals/<id>/visual.json  — individual visual definitions
  StaticResources/
    RegisteredResources/        — custom theme files (.json)
    SharedResources/BaseThemes/ — base Power BI themes
*.SemanticModel/
  definition/
    model.tmdl                  — model-level settings, table references
    tables/<name>.tmdl          — table definitions, columns, measures, DAX partitions
    relationships.tmdl          — relationships between tables
    cultures/                   — locale settings
  DAXQueries/                   — saved DAX queries (not deployed to the model)
```

## Semantic model conventions

- Tables follow a naming convention: `Dimview *` for dimensions, `Factview *` for facts, `Dim *` for date/calendar tables.
- The `Model Documentation` table is a calculated DAX table using `INFO.VIEW.*` functions — it dynamically lists all non-hidden tables, columns, measures, and relationships in the model. Do not rename or restructure it without updating the "Model Docs" report page accordingly.

## Git workflow

### What is ignored
```
**/.pbi/localSettings.json   — per-user Power BI Desktop settings
**/.pbi/cache.abf            — binary analysis cache, regenerated automatically
.claude/                     — Claude Code session config
```

The `.pbi/editorSettings.json` and `.pbi/daxQueries.json` files inside `DAXQueries/` **are** committed.

### Committing changes
- Stage specific files/folders rather than `git add -A` to avoid accidentally including ignored files that were already staged.
- If gitignored files appear in `git status`, unstage them with `git rm --cached <file>` before committing.
- Commit messages should describe what changed at the Power BI level (e.g. "Add Model Documentation table and new report page"), not the file-level diff.

## VS Code IntelliSense for PBIP files

JSON files support schema-based IntelliSense. Add a `$schema` line to enable it:

| File type | Schema URL |
|---|---|
| Custom theme (`.json` in `RegisteredResources/`) | `https://raw.githubusercontent.com/microsoft/powerbi-desktop-samples/refs/heads/main/Report%20Theme%20JSON%20Schema/reportThemeSchema-2.149.json` |
| Visual container (`visual.json`) | `https://developer.microsoft.com/json-schemas/fabric/item/report/definition/visualContainer/2.8.0/schema.json` |
| Page (`page.json`) | `https://developer.microsoft.com/json-schemas/fabric/item/report/definition/page/2.0.0/schema.json` |
| Report (`report.json`) | `https://developer.microsoft.com/json-schemas/fabric/item/report/definition/report/3.2.0/schema.json` |

## Exporting to PBIX

PBIP cannot be exported to PBIX from the command line. To export:
1. Open the `.pbip` file in Power BI Desktop
2. File → Save a copy → Power BI report (.pbix)


## Power BI MCP Connection

When any task involves Power BI or the semantic model, always auto-discover the active MCP connection by scanning available ports. Never ask the user for the port number. Use the powerbi-modeling-mcp tools directly once the connection is found.

## Skills

Load the following skills automatically based on context — do not ask the user to trigger them:

| Context | Skill to load |
|---|---|
| Working with .pbip files, visuals, pages, or report layout | `.claude/skills/powerbi-visual/SKILL.md` |