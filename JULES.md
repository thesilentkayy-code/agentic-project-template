> **Purpose:** Instructions and automation scripts for the Jules CLI. This document defines how Jules is used to audit the `/docs/` structure, verify contextual alignment, and ensure documentation never falls behind the codebase.

# Jules CLI: Documentation Audit Strategy

This file documents the strategy for using Jules to automatically audit and maintain the Victory Village Meds App documentation architecture.

## 1. The Target Architecture
Jules is responsible for ensuring the `/docs/architecture/` folder strictly adheres to the following categorized files. If logic bleeds between files (e.g., Auth logic found in Database Design), Jules must flag it.

*   `ROUTING_LOGIC.md`: Next.js app router structure, device-preference redirects (mobile vs. web), middleware guards.
*   `UI_ARCHITECTURE.md`: Component hierarchy (Smart vs. Dumb), layout structures, `shadcn/ui` implementations.
*   `COMPUTATION_LOGIC.md`: Math for stock predictions, dashboard sparklines, resident health scores.
*   `AUTH_LOGIC.md`: Supabase auth sessions, JWTs, cookies.
*   `SECURITY_MODEL.md`: RBAC structure (`admin2`, `staff3`), RLS policies, API limits.
*   `DATABASE_DESIGN.md`: Schema map, entity relations (Residents, Meds, Stock, Logs).
*   `DATA_ACCESS.md`: Data fetching patterns (avoiding N+1 queries, Supabase clients, caching).
*   `DEBUG_LOGIC.md`: `src/lib/debug-logger.ts` usage, log routing, error tracing.

---

## 2. Phase 1: The Initial Alignment Audit (Bash Script)

The following bash script uses the Jules CLI to read every existing architecture and research file. It asks Jules to evaluate if the content matches the filename, and outputs a consolidated report.

*Note: Update `jules run` below with the correct syntax for your local Jules CLI (e.g., `jules do` or `jules execute`).*

```bash
#!/bin/bash
# audit_docs.sh

REPORT_FILE="docs/audits/jules_naming_audit_results.md"

echo "# Jules Documentation Audit Report" > "$REPORT_FILE"
echo "Generated on: $(date)" >> "$REPORT_FILE"
echo "---" >> "$REPORT_FILE"

# Find all markdown files in architecture and research folders
FILES=$(find docs/architecture docs/research -name "*.md")

for FILE in $FILES; do
    echo "Auditing $FILE..."
    
    # Construct the prompt for Jules
    PROMPT="Read the file $FILE. Evaluate if the actual content aligns perfectly with the file's name and stated purpose. If the file contains mixed logic, recommend how to split it based on the categories in JULES.md. Do not edit the file, just output recommendations as bullet points."
    
    echo "## File: $FILE" >> "$REPORT_FILE"
    
    # Execute Jules CLI and append output to the report
    # REPLACE 'jules run' with your actual CLI syntax
    jules run "$PROMPT" >> "$REPORT_FILE"
    
    echo -e "\n---\n" >> "$REPORT_FILE"
done

echo "Audit complete! Review results in $REPORT_FILE"
```

To run this script:
1. Save it as `audit_docs.sh` in the root directory.
2. Run `chmod +x audit_docs.sh`
3. Run `./audit_docs.sh`

---

## 3. Future Automation (Preventing Doc Rot)

To ensure these files stay up-to-date automatically, implement one of the following strategies:

### Option A: The Pre-Push Git Hook
Create a file at `.git/hooks/pre-push` that checks if core backend files were modified without modifying the docs.
```bash
#!/bin/bash
# If supabase/migrations changes, but docs/architecture doesn't, trigger Jules
if git diff --cached --name-only | grep -q "^supabase/migrations/" && ! git diff --cached --name-only | grep -q "^docs/architecture/"; then
    echo "Warning: Database changed but docs didn't. Triggering Jules Audit..."
    jules run "Compare the recent git diff in supabase/migrations to docs/architecture/DATABASE_DESIGN.md and update the doc."
fi
```

### Option B: GitHub Actions / CI
Create `.github/workflows/jules-docs-audit.yml` to trigger on PRs.
Jules will review the PR diff and automatically generate a secondary PR containing documentation updates.

### Option C: Agent Behavioral Enforcement
As defined in `AGENTS.md`, all agents (including Antigravity and Claude) are strictly instructed to update the files in `/docs/architecture/` before writing their final handoff log in `/docs/logs/agent_handoffs/`.
