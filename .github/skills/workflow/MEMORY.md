# Workflow skill maintenance

Before committing, establish which directory is the real Git checkout. Installed skill paths can
be symlinks or snapshots; an inaccessible parent directory is not proof that the project lacks Git.
The repository is `johnviklund/agent-skills`, with skill source at `.github/skills/workflow/`.
Preserve local changes when reconciling against a remote checkout.

The v1 baseline is the `workflow-v1.0.0` tag. V2 replaces the fixed phase chain with optional
assessment and outcome-based execution. Historical evaluation cases test review judgment; they
do not require the old workflow mechanics.
