Read the following files in this order:
1. CLAUDE.md   — project rules, stack, architecture, do not list
2. MEMORY.md   — current context, decisions, where we stopped
3. TASKS.md    — what is in progress and what is next
4. PLAN.md     — current feature approach and steps

If a file does not exist or is empty, note it and continue.

Also read all files in .claude/commands/ and register them as available commands.

## Determine Current State

After reading all files, determine the state of the current task in this order:

1. Is there a plan?
   - PLAN.md empty or missing → Status: no plan

2. Is coding started?
   - No files listed in MEMORY.md as changed → Status: not started

3. Is review done?
   - No ## Review Log in MEMORY.md → Status: review pending
   - ## Review Log exists but any entry has Resolved: no → Status: review failed

4. Are tests done?
   - No test results in MEMORY.md → Status: tests pending
   - Test results exist but failures recorded → Status: tests failed

5. All clear?
   - Review passed + all tests passed → Status: complete

## Report Format

---
Project:     [name and one line goal]
In Progress: [current task from TASKS.md, or "nothing"]
Status:      [no plan / not started / coding / review pending / review failed / tests pending / tests failed / complete]
Issues:      [unresolved review issues or test failures with file and line, or "none"]
Suggest:     [exactly one: /planner / /coder / /reviewer / /tester / /stop / "awaiting new task"]
---

Do not write any code or modify any file.
Wait for the user to run the suggested command or give new instructions.
