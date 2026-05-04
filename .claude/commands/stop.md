We are stopping this session. Follow these steps in order.

## Step 1 — Verify Task Completion

Check the following before doing anything else:

1. ## Review Log in MEMORY.md — any Resolved: no entries?
2. ## Test Log in MEMORY.md — any failures?
3. TASKS.md ## In Progress — any tasks not yet complete?

If any check fails, do NOT proceed to archive or changelog.
Instead report the blockers and suggest the appropriate command:

---
Cannot close session.
Blockers:    [list of what is unresolved]
Suggest:     [/coder / /reviewer / /tester — whichever is appropriate]
---

Stop and wait for the user to resolve blockers before continuing.

## Step 2 — All Clear: Ask for Approval

List everything completed this session:

"The following tasks appear complete:
- [task id] — [description]

Review: passed
Tests:  all passed

Approve as done? (yes / no)"

Wait for response before proceeding.

## Step 3a — If Approved as Done

### Archive
Create the following files under .claude/archives/YYYYMMDD/:

- YYYYMMDD_MEMORY.md     — copy of current MEMORY.md
- YYYYMMDD_TASKS.md      — copy of current TASKS.md
- YYYYMMDD_PLAN.md       — copy of current PLAN.md

### Update Files in This Order

1. CHANGELOG.md
   - Append ## [YYYY-MM-DD]
   - Bullet points of what shipped

2. TASKS.md
   - Remove completed tasks entirely
   - Don't move any upcomming task to ## In Progress, it's coder's job
   - Keep ## Up Next accurate

3. MEMORY.md
   - Clear all completed entries
   - Clear ## Review Log entries that are Resolved: yes
   - Clear ## Test Log entries that passed
   - Keep any unresolved entries as-is

4. PLAN.md
   - Clear entirely if the feature is fully done
   - Leave as-is if partially done

### Final Commit
Tell the Commiter to run one final commit:
  Message: "chore: finalize [task headline]"

### Report to User

---
Session closed.
Completed:   [task id and headline]
Archived:    .claude/archives/YYYY-MM-DD/
Changelog:   updated
Memory:      cleared
Branch:      [branch name] — ready for human review and merge
---

"You can now review the branch and merge when ready."

## Step 3b — If NOT Approved as Done

Do NOT archive. Do NOT update CHANGELOG.md. Do NOT clear memory.

Only update:

1. MEMORY.md
   - What was completed this session (partial progress)
   - Exactly where it stopped (file, function, line if relevant)
   - Any decisions made this session
   - Exact next step to resume next session
   - Preserve ## Review Log and ## Test Log as-is

2. TASKS.md
   - Do not remove or check off anything
   - Keep ## Up Next accurate

Then report:

---
Session saved.
Completed:   [what finished, or "nothing fully completed"]
Stopped at:  [exact point, file and function if relevant]
Resume:      [first thing to do next session]
Suggest:     [/coder / /reviewer / /tester — whichever is next]
---
