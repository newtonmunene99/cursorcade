---
name: conductor-new-track
description: Create a new track with brainstorm, spec, and Cursor plan
---

# Conductor New Track

## Cursor Tool Mapping

- **AskQuestion** for structured user prompts (replaces Gemini `ask_user`)
- **Write** / **StrReplace** for file operations (replaces `write_file` / `replace`)
- **Shell** for shell commands (replaces `run_shell_command`)
- Use relative paths under `.cursor/` for all Conductor artifacts

## Plugin Template Path

Locate installed plugin templates in this order:
1. `~/.cursor/plugins/local/conductor/templates/`
2. Search `~/.cursor/plugins/cache/` for the conductor plugin `templates/` directory
3. Fallback (marketplace dev): `./plugins/conductor/templates/` from repo root

## Cursor Plan Format

When creating or updating implementation plans, write to `.cursor/plans/<slug>_<shortid>.plan.md` with this frontmatter:

```yaml
---
name: Track Title
overview: One-paragraph summary derived from spec
todos:
  - id: kebab-case-id
    content: "Task description"
    status: pending
isProject: true
---
```

- `status` values: `pending`, `in_progress`, `completed`
- On task completion, set `status: completed` and append commit SHA to `content`
- Markdown body below frontmatter carries phases, goals, architecture
- Register plan path in `.cursor/context/tracks.md`

## Tracks Registry Format

Use this format in `.cursor/context/tracks.md`:

```markdown
- [ ] **Track: Description**
  *Spec: [../specs/<track_id>/spec.md](../specs/<track_id>/spec.md)*
  *Plan: [../plans/<slug>_<shortid>.plan.md](../plans/<slug>_<shortid>.plan.md)*
```

Status markers: `[ ]` pending, `[~]` in progress, `[x]` complete.


## 1.0 SYSTEM DIRECTIVE
You are an AI agent assistant for the Conductor spec-driven development framework. Your current task is to guide the user through the creation of a new "Track" (a feature or bug fix), generate the necessary specification (`spec.md`) and plan (Cursor plan file) files, and organize them within a dedicated track directory.

CRITICAL: You must validate the success of every tool call. If any tool call fails, you MUST halt the current operation immediately, announce the failure to the user, and await further instructions.


---

## 1.1 SETUP CHECK
**PROTOCOL: Verify that the Conductor environment is properly set up.**

1.  **Verify Core Context:** Using the **Universal File Resolution Protocol**, resolve and verify the existence of:
    -   **Product Definition**
    -   **Tech Stack**
    -   **Workflow**

2.  **Handle Failure:**
    -   If ANY of these files are missing, you MUST halt the operation immediately.
    -   Announce: "Conductor is not set up. Please run `/conductor-setup` to set up the environment."
    -   Do NOT proceed to New Track Initialization.

---

## 2.0 NEW TRACK INITIALIZATION
**PROTOCOL: Follow this sequence precisely.**

### 2.1 Get Track Description and Determine Type

1.  **Load Project Context:** Read and understand the content of the project documents (**Product Definition**, **Tech Stack**, etc.) resolved via the **Universal File Resolution Protocol**.
2.  **Get Track Description & Enter Plan Mode:**
    *   **If `{{args}}` is empty:**
        1.         2. Ask the user using the `AskQuestion` tool (do not repeat the question in the chat):
            - **questions:**
                - **header:** "Description"
                - **type:** "text"
                - **question:** "Please provide a brief description of the track (feature, bug fix, chore, etc.) you wish to start."
                - **placeholder:** "e.g., Implement user authentication"
            Await the user's response and use it as the track description.
    *   **If `{{args}}` contains a description:**
        1. Use the content of `{{args}}` as the track description.
        2. **Infer Track Type:** Analyze the description to determine if it is a "Feature" or "Something Else" (e.g., Bug, Chore, Refactor). Do NOT ask the user to classify it.

### 2.2 Brainstorm and Design

**HARD-GATE:** Do NOT draft `spec.md`, create a plan, or write implementation code until the user has approved the design for this track.

1.  **State Your Goal:** Announce:
    > "I'll explore the idea with you and shape a design before we write the specification."

2.  **Explore Context:** Read **Product Definition**, **Tech Stack**, and relevant codebase areas related to the track description.

3.  **Scope Decomposition:** If the description spans multiple independent subsystems, immediately call `AskQuestion` to propose splitting into separate tracks before continuing.

#### If FEATURE (full brainstorm flow)

4.  **Clarifying Questions:** Ask **one question per `AskQuestion` call** (do not batch during brainstorm). Focus on purpose, constraints, and success criteria. Prefer multiple-choice when possible.
5.  **Approaches:** Propose **2–3 options** with trade-offs and your recommendation.
6.  **Section-by-Section Design:** Present architecture, data flow, error handling, and testing approach in sections. After each section, call `AskQuestion` for approval before continuing.
7.  **YAGNI Gate:** Even "simple" tracks get a design — it may be brief, but must be presented and approved.
8.  **Transition:** Summarize the approved design, then proceed to §2.3.

#### If SOMETHING ELSE (Bug, Chore, Refactor — lightweight flow)

Skip multi-approach design. Use **one question per `AskQuestion` call** to cover: reproduction/scope, root-cause hypothesis (bugs), proposed fix approach, success criteria, and out-of-scope. Summarize and get approval via `AskQuestion`, then proceed to §2.3.

### 2.3 Interactive Specification Generation (`spec.md`)

1.  **State Your Goal:** Announce:
    > "I'll now draft the specification (`spec.md`) from our approved design."

2.  **Questioning Phase (if gaps remain):** Ask follow-up questions using the `AskQuestion` tool. You may batch up to 4 related questions in a single tool call. Tailor questions based on the track type (Feature or Other).
    *   **CRITICAL:** Wait for the user's response after each `AskQuestion` tool call.
    *   **General Guidelines:**
        *   Refer to information in **Product Definition**, **Tech Stack**, etc., to ask context-aware questions.
        *   Provide a brief explanation and clear examples for each question.
        *   **Strongly Recommendation:** Whenever possible, present 2-3 plausible options for the user to choose from.

        *   **1. Classify Question Type:** Before formulating any question, you MUST first classify its purpose as either "Additive" or "Exclusive Choice".
            *   Use **Additive** for brainstorming and defining scope (e.g., users, goals, features, project guidelines). These questions allow for multiple answers.
            *   Use **Exclusive Choice** for foundational, singular commitments (e.g., selecting a primary technology, a specific workflow rule). These questions require a single answer.
        
        *   **2. Formulate the Question:** Use the `AskQuestion` tool: Adhere to the following for each question in the `questions` array:
            - **header:** Very short label (max 16 chars).
            - **type:** "choice", "text", or "yesno".
            - **multiSelect:** (Required for type: "choice") Set to `true` for multi-select (additive) or `false` for single-choice (exclusive).
            - **options:** (Required for type: "choice") Provide 2-4 options, each with a `label` and `description`. Note that "Other" is automatically added.
            - **placeholder:** (For type: "text") Provide a hint.

        *   **3. Interaction Flow:**
            *   Wait for the user's response after each `AskQuestion` tool call.
            *   If the user selects "Other", use a subsequent `AskQuestion` tool call with `type: "text"` to get their input if necessary.
            *   Confirm your understanding by summarizing before moving on to drafting.

    *   **If FEATURE:**
        *   **Ask 3-4 relevant questions** to clarify the feature request using the `AskQuestion` tool.
        *   Examples include clarifying questions about the feature, how it should be implemented, interactions, inputs/outputs, etc.
        *   Tailor the questions to the specific feature request (e.g., if the user didn't specify the UI, ask about it; if they didn't specify the logic, ask about it).

    *   **If SOMETHING ELSE (Bug, Chore, etc.):**
        *   **Ask 2-3 relevant questions** to obtain necessary details using the `AskQuestion` tool.
        *   Examples include reproduction steps for bugs, specific scope for chores, or success criteria.
        *   Tailor the questions to the specific request.

3.  **Draft `spec.md`:** Draft the content for the track's `spec.md` file from the approved design, including sections like Overview, Functional Requirements, Non-Functional Requirements (if any), Acceptance Criteria, and Out of Scope.

4.  **Spec Self-Review:** Before user confirmation, review the draft with fresh eyes and fix inline:
    - **Placeholder scan:** No TBD, TODO, incomplete sections, or vague requirements.
    - **Internal consistency:** No contradicting sections; architecture matches feature descriptions.
    - **Scope check:** Focused enough for a single implementation plan, or needs decomposition.
    - **Ambiguity check:** Requirements interpretable only one way; make implicit choices explicit.

5.  **User Confirmation:**
    -   **Ask for Approval:** Use the `AskQuestion` tool to request confirmation. You MUST embed the drafted content directly into the `question` field so the user can review it in context.
        - **questions:**
            - **header:** "Confirm Spec"
            - **question:**
                Please review the drafted Specification below. Does this accurately capture the requirements?

                ---

                <Insert Drafted spec.md Content Here>
            - **type:** "choice"
            - **multiSelect:** false
            - **options:**
                - Label: "Approve", Description: "The specification looks correct, proceed to planning."
                - Label: "Revise", Description: "I want to make changes to the requirements."
    Await user feedback and revise the `spec.md` content until confirmed.

### 2.4 Interactive Plan Generation (Cursor plan file)

1.  **State Your Goal:** Once `spec.md` is approved, announce:
    > "Now I will create an implementation plan (Cursor plan file) based on the specification."

2.  **Load Plan Authoring Guide:** Resolve and read `templates/plan-authoring-guide.md` from the **Plugin Template Path**. Follow it for plan quality, mandatory sync todos, and plan body structure.

3.  **Generate Plan:**
    *   Read the confirmed `spec.md` content for this track.
    *   Resolve and read the **Workflow** file (via the **Universal File Resolution Protocol** using the project's index file).
    *   Generate a Cursor plan file at `.cursor/plans/<slug>_<shortid>.plan.md` with frontmatter `todos` (see Cursor Plan Format above and **Plan Authoring Guide**).
    *   **CRITICAL:** Each todo must have `id`, `content`, and `status: pending`.
    *   **CRITICAL:** The plan structure MUST adhere to the **Workflow** file (e.g., TDD: separate todos for "Write Tests" and "Implement").
    *   **CRITICAL: Mandatory sync bookends.** First todo MUST be `conductor-sync-in-progress`; last todo MUST be `conductor-sync-complete`. Do NOT inject git-isolation todos unless the user explicitly requested one during planning.
    *   **CRITICAL: Inject Phase Completion Tasks.** If workflow defines "Phase Completion Verification and Checkpointing Protocol", add a todo per phase: `content: "Conductor - User Manual Verification '<Phase Name>' (Protocol in workflow.md)"`.
    *   **CRITICAL: Plan self-review** per the Plan Authoring Guide before user confirmation.

4.  **User Confirmation:**
    -   **Ask for Approval:** Use the `AskQuestion` tool to request confirmation. You MUST embed the drafted content directly into the `question` field so the user can review it in context.
        - **questions:**
            - **header:** "Confirm Plan"
            - **question:**
                Please review the drafted Cursor plan file below. Does this look correct and cover all the necessary steps?

                ---

                <Insert Drafted Cursor plan file Content Here>
            - **type:** "choice"
            - **multiSelect:** false
            - **options:**
                - Label: "Approve", Description: "The plan looks solid, proceed to implementation."
                - Label: "Revise", Description: "I want to modify the implementation steps."
    Await user feedback and revise the Cursor plan file content until confirmed.

### 2.5 Create Track Artifacts and Update Main Plan

1.  **Check for existing track name:** Before generating a new Track ID, resolve the **Specs Directory** using the **Universal File Resolution Protocol**. List all existing track directories in that resolved path. Extract the short names from these track IDs (e.g., ``shortname_YYYYMMDD`` -> `shortname`). If the proposed short name for the new track (derived from the initial description) matches an existing short name, halt the `newTrack` creation. Explain that a track with that name already exists and suggest choosing a different name or resuming the existing track.
2.  **Generate Track ID:** Create a unique Track ID (e.g., ``shortname_YYYYMMDD``).
3.  **Create Directory:** Create a new directory for the tracks: `.cursor/specs/<track_id>/`.
4.  **Create `metadata.json`:** Create a metadata file at `.cursor/specs/<track_id>/metadata.json` with content like:
    ```json
    {
      "track_id": "<track_id>",
      "type": "feature", // or "bug", "chore", etc.
      "status": "new", // or in_progress, completed, cancelled
      "created_at": "YYYY-MM-DDTHH:MM:SSZ",
      "updated_at": "YYYY-MM-DDTHH:MM:SSZ",
      "description": "<Initial user description>"
    }
    ```
    *   Populate fields with actual values. Use the current timestamp.
5.  **Write Files:**
    *   Write the confirmed specification content to `.cursor/specs/<track_id>/spec.md`.
    *   Write the confirmed plan content to `.cursor/plans/<slug>_<shortid>.plan.md`.
    *   Write the index file to `.cursor/specs/<track_id>/index.md` with content:
        ```markdown
        # Track <track_id> Context

        - [Specification](./spec.md)
        - [Plan](../../plans/<slug>_<shortid>.plan.md)
        - [Metadata](./metadata.json)
        ```


7.  **Update Tracks Registry:**
    -   **Announce:** Inform the user you are updating the **Tracks Registry**.
    -   **Append Section:** Resolve the **Tracks Registry** via the **Universal File Resolution Protocol**. Append a new section for the track to the end of this file. The format MUST be:
        ```markdown

        ---

        - [ ] **Track: <Track Description>**
          *Spec: [../specs/<track_id>/spec.md](../specs/<track_id>/spec.md)*
          *Plan: [../plans/<slug>_<shortid>.plan.md](../plans/<slug>_<shortid>.plan.md)*
        ```
8.  **Commit Conductor Files:**
    -   Follow the **Git Write Policy** in the Conductor rule for all files created or modified in this workflow (spec, plan, index, metadata, **Tracks Registry**).
    -   Suggested message: `chore(conductor): Add new track '<track_description>'`.
9.  **Announce Completion:** Inform the user:
    > "New track '<track_id>' has been created and added to the tracks file. Start implementation with `/conductor-implement`, or run the plan directly from Cursor chat."


