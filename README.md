# agents.prompts

Reusable prompts for AI coding agents.

| Prompt | Use it for |
| --- | --- |
| [Multi-repository documentation](prompts/multi-repository-documentation.md) | Establishing documentation ownership across a design repository and implementation repositories, removing duplication, and reviewing or consolidating design branches. |
| [AI-agent documentation](prompts/ai-agent-documentation.md) | Creating repository documentation that helps agents use, integrate, maintain, and troubleshoot a project, including API contracts, recipes, limitations, and validation. |
| [Android library publishing](prompts/android-library-publishing.md) | Building signed Maven Central releases with automatic versioning, a runner-free 15-minute environment wait, up to 40-minute public verification, race-safe manual finalization, and generated IMPORT.md. |

## Usage

1. Open the appropriate prompt and copy its contents into your agent conversation.
2. Supply the target repository URL or URLs and the branch to use. Replace bracketed inputs where provided.
3. Specify whether the agent should leave local changes, open pull requests, or commit to the target branch.

For a system spanning several repositories, use the multi-repository prompt first to establish ownership. Then apply the AI-agent documentation prompt to each implementation repository, retaining links to the canonical system design.

The prompt files are reusable task text; they are not instructions to execute against this prompt collection merely because an agent reads them.

The Android publishing prompt follows the current YOLO release design: publication → `delayed-docs` environment gate → shared automatic/manual finalization. The two documentation prompts carry the same confirmed-release and recovery rules; they do not authorize a real publication.
