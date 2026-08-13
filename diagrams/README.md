[Back to README](../README.md)

# Diagrams

This folder contains editable Mermaid source files for the case-study diagrams. GitHub renders Mermaid diagrams embedded directly in Markdown pages; standalone `.mmd` files are included here so the diagrams can also be exported to PNG/SVG for slides or a portfolio site.

| Mermaid Source | Optional Export | Purpose |
| --- | --- | --- |
| [`architecture.mmd`](architecture.mmd) | `architecture.png` | System components and data flow |
| [`outreach-state-machine.mmd`](outreach-state-machine.mmd) | `outreach-state-machine.png` | The full outreach status graph and legal transitions |
| [`draft-generation-sequence.mmd`](draft-generation-sequence.mmd) | `draft-generation-sequence.png` | AI draft request, validation, and template fallback |
| [`send-safety-sequence.mmd`](send-safety-sequence.mmd) | `send-safety-sequence.png` | Double identity verification during an attended send |
| [`import-workflow.mmd`](import-workflow.mmd) | `import-workflow.png` | CSV import and live-capture paths into the queue |
| [`data-model.mmd`](data-model.mmd) | `data-model.png` | Campaigns, Contacts, Outreach, and Activity Log relationships |

## Export Guidance

Use the Mermaid CLI or a Mermaid-compatible editor if image exports are needed:

```bash
mmdc -i diagrams/architecture.mmd -o diagrams/architecture.png
```

Do not add real spreadsheet IDs, service-account emails, LinkedIn profile URLs, or real contact data to exported diagrams.
