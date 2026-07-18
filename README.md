# GHEC EMU Customer Self-Setup Pack

This repository contains customer-facing onboarding materials for **GitHub Enterprise Cloud** with **Enterprise Managed Users (EMU)** and **Data Residency**.

## Which document should customers use?

Start with **`customer-self-setup-tutorial.md`**.

It is the primary step-by-step guide for customers completing self-service setup.

## Repository contents

### `customer-self-setup-tutorial.md`
The main customer tutorial. Use this document for the full guided setup flow, including:
- preflight requirements
- enterprise creation
- authentication setup
- SCIM provisioning
- Azure subscription connection
- optional GitHub Copilot enablement
- validation and go-live checks
- troubleshooting guidance

### `customer-self-setup-runbook.md`
A companion tracking document for customer teams and delivery teams. Use it to capture:
- customer profile details
- setup progress by step
- validation evidence
- screenshot checkpoints
- blockers and follow-up actions

### `github-enterprise-customer-self-setup-guide.md`
The editable Markdown source for the PDF handout. This is the file to edit when the handout needs updating. It is a concise, customer-facing summary of the setup process, structured for sharing or presentation. After editing this file, export it to PDF using the instructions below.

### `github-enterprise-customer-self-setup-guide.pdf`
The compiled PDF handout. This file was generated from the Markdown source above. Do not edit the PDF directly — edit the Markdown source and regenerate the PDF instead.

## Recommended usage

1. Review `customer-self-setup-tutorial.md` before starting setup.
2. Use `customer-self-setup-runbook.md` to track progress and evidence during implementation.
3. Share `github-enterprise-customer-self-setup-guide.pdf` when a polished handout is needed.

## Regenerating the PDF handout

The PDF handout is generated from `github-enterprise-customer-self-setup-guide.md`. To regenerate it after editing the Markdown source, use one of the following methods:

### Option 1 — Pandoc (recommended for automation)

```bash
pandoc github-enterprise-customer-self-setup-guide.md \
  --from markdown \
  --to pdf \
  --output github-enterprise-customer-self-setup-guide.pdf \
  --pdf-engine=xelatex \
  -V geometry:margin=1in \
  -V fontsize=11pt \
  -V colorlinks=true
```

Install Pandoc from <https://pandoc.org/installing.html> and a LaTeX engine such as [TeX Live](https://tug.org/texlive/) or [MiKTeX](https://miktex.org/).

### Option 2 — VS Code with Markdown PDF extension

1. Open `github-enterprise-customer-self-setup-guide.md` in VS Code.
2. Install the [Markdown PDF](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf) extension.
3. Right-click in the editor and choose **Markdown PDF: Export (pdf)**.

### Option 3 — Marp (for slide-style output)

If you want presentation-quality slide output instead of a document PDF, see [Marp](https://marp.app/) for converting Markdown to slides.

## Notes

- `github-enterprise-customer-self-setup-guide.md` is the editable source for the PDF handout.
- Edit the Markdown source and regenerate the PDF rather than editing the PDF directly.
- `customer-self-setup-tutorial.md` is the primary source of truth for all setup content. Keep the handout source consistent with the tutorial when either file is updated.
