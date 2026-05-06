---
description: Export presentation to WPS Office .dps format alongside .pptx
---

# Export to DPS Workflow

> **Purpose**: Generate both `.dps` (WPS Office) and `.pptx` (Microsoft PowerPoint) files from a completed project.

## Overview

DPS is the native presentation format of WPS Office (金山办公), widely used in China. This workflow extends the standard PPTX export to produce both formats simultaneously, ensuring compatibility with both Microsoft Office and WPS Office ecosystems.

## When to Use

- User explicitly requests `.dps` format output
- Target audience uses WPS Office as primary presentation software
- Need to share with users in Chinese enterprise/government environments where WPS is standard

## Prerequisites

- Step 6 (Executor) complete with all SVGs in `svg_output/`
- Step 7.1 and 7.2 (post-processing) complete
- `svg_final/` directory ready

---

## Step 1: Standard PPTX Export

First, run the standard export pipeline (Step 7.3):

```bash
python3 ${SKILL_DIR}/scripts/svg_to_pptx.py <project_path> -s final
```

By default, this now generates **both formats**:
- `exports/<project_name>_<timestamp>.dps` — WPS Office format
- `exports/<project_name>_<timestamp>.pptx` — Microsoft PowerPoint format

---

## Step 2: Single Format Export (Optional)

If only one format is needed:

**DPS only:**
```bash
python3 ${SKILL_DIR}/scripts/svg_to_pptx.py <project_path> -s final --only-format dps
```

**PPTX only:**
```bash
python3 ${SKILL_DIR}/scripts/svg_to_pptx.py <project_path> -s final --only-format pptx
```

---

## Output Files

| Format | Location | Compatible Software |
|--------|----------|---------------------|
| `.dps` | `exports/<name>_<timestamp>.dps` | WPS Office (Windows/Mac/Linux) |
| `.pptx` | `exports/<name>_<timestamp>.pptx` | Microsoft PowerPoint, Keynote, Google Slides, WPS Office |

---

## Technical Notes

### Format Equivalence

Both `.pptx` and `.dps` are based on the Office Open XML (OOXML) standard. The internal structure (XML schemas, content types, relationships) is identical. The only difference is the file extension:

- `.pptx` — Standard Office Open XML Presentation
- `.dps` — WPS Office Presentation (same OOXML content, different extension)

### Compatibility

| Software | Opens .pptx | Opens .dps |
|----------|-------------|------------|
| WPS Office | ✅ Full support | ✅ Native support |
| Microsoft PowerPoint | ✅ Full support | ⚠️ May require renaming to .pptx |
| Keynote | ✅ Import support | ⚠️ May require renaming to .pptx |
| Google Slides | ✅ Import support | ⚠️ May require renaming to .pptx |

### Best Practices

1. **Generate both formats** when sharing with mixed audiences
2. **Use `.dps` for WPS-centric environments** (Chinese enterprises, government)
3. **Use `.pptx` for international or cross-platform sharing**
4. **Keep both files** — they're small and ensure maximum compatibility

---

## Troubleshooting

### File won't open in WPS Office

1. Verify WPS Office version is 2019 or later
2. Try opening the `.pptx` version instead — WPS fully supports PPTX

### Microsoft PowerPoint won't open .dps

Rename the file extension from `.dps` to `.pptx`:
```bash
mv output.dps output.pptx
```

### Want to convert existing PPTX to DPS

Use the reverse conversion workflow:

```bash
# Step 1: PPTX to SVG
python3 ${SKILL_DIR}/scripts/pptx_to_svg.py <pptx_file> -o <output_dir>

# Step 2: SVG to DPS
python3 ${SKILL_DIR}/scripts/svg_to_pptx.py <output_dir> -s svg --only-format dps
```

---

## CLI Reference

```bash
# Default: generate both .dps and .pptx
python3 svg_to_pptx.py <project_path>

# Options
--only-format {dps,pptx}   # Generate only specified format
-o <path.dps>              # Explicit output path (extension determines format)
--only native              # Only native shapes version (editable)
--only legacy              # Only SVG image version (preview)
```

---

## Integration with Main Pipeline

This workflow integrates seamlessly with Step 7 of the main PPT Master pipeline:

```
Step 7.1: total_md_split.py    → Split speaker notes
Step 7.2: finalize_svg.py      → SVG post-processing
Step 7.3: svg_to_pptx.py       → Export to DPS + PPTX (default)
```

No additional steps required — the standard export now produces both formats by default.
