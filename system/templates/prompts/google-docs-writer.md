## Task for Google Docs Writer Agent

**Spawned By**: {spawned_by}

### Context

Read the following knowledge files for context:
{knowledge_files}

### Task

Convert the markdown report to a formatted Google Docs document.

### Input

- **Markdown Report**: `{markdown_path}`
- **Screenshots Folder**: `{screenshots_path}` (if exists)
- **Document Title**: {doc_title}
- **Target Folder**: {folder_id} (optional)

### Requirements

1. **Read the markdown report** at the specified path
2. **Parse all markdown elements**: headings, lists, tables, bold/italic, code blocks
3. **Create a new Google Doc** with the specified title
4. **Apply formatting**:
   - H1/H2/H3 headings with proper Google Docs heading styles
   - Bold and italic text
   - Bullet and numbered lists
   - Tables with header row styling
   - Code blocks with monospace font
5. **Embed screenshots** from the screenshots folder (if present)
6. **Apply status colors**: PASS=green (#34a853), FAIL=red (#ea4335), PARTIAL=orange (#fbbc04)
7. **Return the Google Docs URL**

### Styling Requirements

- Professional, clean appearance
- Consistent spacing (12pt after paragraphs, 18pt after headings)
- Table header row: bold, light gray (#f3f3f3) background
- Images: centered, max width 6 inches
- Executive Summary: slightly larger font or highlighted
- Code/monospace: Courier New, light gray background

### Expected Output

```
## Google Docs Report Created

**URL**: https://docs.google.com/document/d/{doc_id}
**Document ID**: {doc_id}
**Title**: {title}

### Content Migrated
- Sections: {count}
- Tables: {count}
- Images: {count} embedded, {count} failed
- Total paragraphs: {count}

### Notes
{any issues or warnings}
```

### Session Log

As your LAST action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_google-docs-writer_<task-slug>.md`

**Additional fields**: Include Google Docs URL in Outcome.
