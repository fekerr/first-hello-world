# Generalized Closeout Branch Process

This document outlines a suggested process for closing out a development session or task, involving optional archival steps and the creation of a final closeout branch.

## Naming Convention Variable

-   **`BASENAME`**: A string variable representing the core name for this phase, typically in `yymmdd_xxx` format (e.g., `240611_taskA`, `240611_01`). This `BASENAME` will be used for naming branches, folders, and report/summary files.

## Process Steps

1.  **Finalize Primary Work:**
    *   Ensure all development, fixes, and main documentation for the current feature/task on its primary working branch (e.g., `feature/my-feature`, `dev/task-123`, or a numbered branch like `250610`) are complete, tested to satisfaction, and submitted/pushed to the remote.

2.  **Create an Archival Branch/Step (Optional, Recommended for significant tasks):**
    *   **Branch:** Create a new branch named `BASENAME_archive` (e.g., `240611_taskA_archive`) from the latest state of the primary work branch.
    *   **Switch:** Checkout the `BASENAME_archive` branch.
    *   **Folder:** Create a directory named `BASENAME_archive/` at the repository root (e.g., `240611_taskA_archive/`).
    *   **Consolidated Log/Summary:** Create a comprehensive summary document, for example, `BASENAME_archive/BASENAME_archive_summary.md`. This document should:
        *   Provide a high-level overview of the entire session or task that led to this archival point.
        *   Concatenate, or list and reference, all relevant AI (e.g., Jules) log files created during the task.
        *   Explicitly mention any significant issues encountered, decisions made, and their resolutions.
    *   **Archive Individual Raw Logs:** Copy the original individual AI log files (e.g., `Jules/feature_branch_*.md`, `Jules/BASENAME_*.md`) into a dedicated subdirectory, such as `BASENAME_archive/archived_Jules_logs/`.
    *   **New Jules Log for Archival Task:** Create a new AI log entry (e.g., `Jules/BASENAME_archive_001.md`) to document the steps taken during this archival process itself.
    *   **Submit:** Commit all new files (summary document, archived logs, .gitkeep files for directories) and the new Jules log to the `BASENAME_archive` branch. Push this branch to the remote.

3.  **Create the "Closeout" Branch:**
    *   **Branch:** Create the final closeout branch, named `BASENAME_closeout` (e.g., `240611_taskA_closeout`), from the latest state of the `BASENAME_archive` branch (if created) or directly from the primary work branch if no archival step was performed.
    *   **Switch:** Checkout the `BASENAME_closeout` branch.
    *   **Folder (Optional):** A dedicated folder `BASENAME_closeout/` can be created if specific artifacts for this phase are expected. However, often this branch might only contain a final report document at the root or in `Jules/`.
    *   **New Jules Log for Closeout Task:** Start a new AI log file (e.g., `Jules/BASENAME_closeout_001.md`) to document the closeout activities.
    *   **Submit Initial Branch:** Commit the initial Jules log and push the `BASENAME_closeout` branch to the remote. This makes the branch available while final closeout activities are performed.

4.  **Perform Closeout Activities (on `BASENAME_closeout` branch):**
    *   **Generate Final Report/Summary Document:**
        *   Create a final report document, for instance, at `BASENAME_closeout/BASENAME_closeout_report.md` or directly in the root as `BASENAME_closeout_report.md`.
        *   This report should summarize:
            *   The overall goal(s) of the entire task/session.
            *   Key features implemented, tasks completed, and major outcomes.
            *   The final state of key artifacts (e.g., "Feature X is implemented in module Y", "RPi Docker emulation is available in the `rpi_docker/` directory on branches X, Y, Z").
            *   Direct links or clear references to important user-facing documentation (e.g., `rpi_docker/README.md`).
            *   Reference to the consolidated archival log (e.g., `BASENAME_archive/BASENAME_archive_summary.md`), if applicable.
            *   Any known outstanding issues, limitations, or recommendations for future work.
    *   **Cleanup (Informational):** List any temporary or feature branches that were merged and could now be considered for deletion from the remote repository by a human operator (AI typically cannot delete remote branches).
    *   **Final Verification (Optional):** Perform a quick check (e.g., `ls`, read key files) to ensure all expected final artifacts are present on the branch.
    *   **Update Jules Log:** Meticulously document all closeout activities performed in the current Jules log (`Jules/BASENAME_closeout_001.md`).
    *   **Submit Final Closeout Artifacts:** Commit the final report, any other closeout artifacts, and the updated Jules log to the `BASENAME_closeout` branch. Push these final changes to the remote.

This process provides a structured way to conclude a work session, ensuring that work is archived, summarized, and the process itself is documented.
