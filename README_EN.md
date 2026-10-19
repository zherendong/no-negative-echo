<p align="center">
  <img src="./no-negative-echo/assets/icon.png" width="168" alt="No Negative Echo icon">
</p>

<h1 align="center">No Negative Echo</h1>

<p align="center"><strong>Ship the accepted result, not the rejected proposal.</strong></p>

<p align="center"><em>Ship the result, not the conversation.</em></p>

<p align="center">
  <a href="./README.md">中文</a> · <strong>English</strong>
</p>

<p align="center">
  <a href="https://github.com/LB623/no-negative-echo/actions/workflows/test.yml"><img src="https://github.com/LB623/no-negative-echo/actions/workflows/test.yml/badge.svg" alt="Tests"></a>
  <a href="https://github.com/LB623/no-negative-echo/stargazers"><img src="https://img.shields.io/github/stars/LB623/no-negative-echo?style=flat&amp;logo=github" alt="GitHub stars"></a>
  <a href="./LICENSE"><img src="https://img.shields.io/github/license/LB623/no-negative-echo" alt="MIT License"></a>
  <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&amp;logoColor=white" alt="Python 3.10+">
</p>

## The problem

An agent corrects its implementation, then lets the rejected proposal leak into the final title, comment, commit, PR, or handoff:

```diff
- Title: Tomato and Egg Stir-fry (without braised pork)
+ Title: Tomato and Egg Stir-fry
```

`no-negative-echo` is a skill in the [Agent Skills](https://agentskills.io/specification) format. It helps agents write self-contained deliverables from the accepted, verified result and review every place where unnecessary references to the working session could remain.

A **negative echo** is an unnecessary reference to a discarded idea or revision instruction, often disguised as a disclaimer. A **surface** is any user-visible part of a deliverable or its accompanying text, including filenames, metadata, commit messages, and the final reply. The skill reviews each surface according to what its readers need.

Use it to:

- Write commit messages, PR titles, and descriptions from the final diff for the task
- Rewrite article titles, openings, UI copy, or handoffs
- Finalize work after long conversations, collaboration, or multiple revisions

## Ways to use it

Install the skill when you need its decision workflow, text scanner, and additional checks for sensitive information or actions that are difficult to reverse. Add the compact instructions to `AGENTS.md` to make the core rule part of the project's standing instructions. You can use both approaches together.

### Option 1: Install the skill

Ask an agent with network, terminal, and file permissions to follow the installation instructions:

```text
Install the no-negative-echo skill:
https://raw.githubusercontent.com/LB623/no-negative-echo/main/INSTALL.md
Report the installation result and whether a new session or restart is needed.
If verification is unavailable, do not claim that installation succeeded.
```

Local Codex example:

```bash
git clone https://github.com/LB623/no-negative-echo.git
cd no-negative-echo
python3 -I -B scripts/install_skill.py \
  --expected-provenance-sha256 b0f8e6df199bdd335d18aa3213b2c180d9e9d0d4bcba276c6dc5b9c1cf6833bf \
  --agent codex
```

Use the installer, runtime files, and expected digest from the same revision. The installer checks the provenance manifest, file hashes, destination conflicts, and installed files. These checks establish byte integrity; they do not authenticate the publisher or prove that the host has activated the skill. A normal installation does not require reading every Python file, running the full test suite, or listing unrelated skills. See [INSTALL.md](INSTALL.md) for other hosts, custom destinations, and installation audits.

Explicitly invoke the skill before an important delivery:

```text
Use the no-negative-echo skill.
Based on the final diff, write the commit subject, PR title, PR body, and handoff.
```

For editorial work:

```text
Use the no-negative-echo skill.
Rewrite the title and opening to reflect the final article body.
```

### Option 2: Add it to the project's AGENTS.md

If you only need the core behavior, append the following instructions to `AGENTS.md` at the project root. Create the file if it does not exist, and do not overwrite existing project instructions.

```markdown
## No Negative Echo

When producing final deliverables and accompanying text, including titles,
filenames, body text, comments, labels, metadata, commit messages, PRs, and
handoffs, describe the accepted result and the facts readers need. Assume
the reader did not see the working session.

- Use rejected proposals, intermediate attempts, and wording corrections to guide the work. Omit them from the deliverable unless its readers need them.
- Evaluate each surface separately: Would its readers need this information? Would omission make it inaccurate, unsafe, misleading, incompatible, or noncompliant? Does it need to explain a real change from the relevant committed, released, or user-approved baseline?
- An instruction to omit a subject does not justify an assurance that it is absent: "Do not mention X" is not a reason to write "X-free." Rewrite titles, filenames, openings, and labels from the requested outcome when they still revolve around a discarded idea.
- Preserve real changes, external actions that actually occurred, and necessary technical names, constraints, diagnostics, tests, and snapshots. Pre-existing user edits are not rejected drafts, even if uncommitted.
- Base commit messages, PRs, and handoffs on changes attributable to this task and results actually inspected. Keep comparisons, quotations, audits, and migration explanations when requested or needed for that surface.
- Review all user-visible text and metadata, including actual results after hooks or external tools modify them. Recheck after changes. Report material failures and verification limits, but do not add assurances such as "no references to X remain."
```

This option depends on the agent supporting `AGENTS.md`. Codex reads project instructions at the start of each run; after changing the file, start a new run or session to load the latest version. See the [official OpenAI documentation](https://learn.chatgpt.com/docs/agent-configuration/agents-md) for discovery details.

The `AGENTS.md` instructions alone do not install the scanner or include the extended verification workflow. When both approaches are present, `AGENTS.md` supplies the standing core rule and the skill provides the detailed review process.

## Decision rule

| Content | Treatment |
|---|---|
| A discarded proposal or intermediate attempt from the working session that readers do not need | Omit |
| A revision instruction repeated as a disclaimer or an assurance that it was followed | Omit |
| Actual removals of released APIs, migrations, and completed external actions | Describe accurately where relevant |
| Facts required for safety, law, compatibility, or audit | Keep |
| Comparisons, quotations, or decision records explicitly requested by the user | Keep |
| Necessary technical constraints or requirements expressed as prohibitions | Keep |
| User changes that existed before the task | Preserve attribution; do not claim them as task output |

The **baseline** is the appropriate comparison point for the task, such as a PR merge base, starting committed state, released version, or user-approved document. A temporary assistant draft is not automatically a baseline. Uncommitted user work is not automatically a discarded draft.

For every surface, ask:

1. Would a reader who never saw the session need this information?
2. Would omission make the result inaccurate, unsafe, misleading, incompatible, or noncompliant?
3. Is it a real change from the relevant baseline that this surface needs to explain?

Something having been discussed or rejected is not, by itself, a reason to mention it.

## Boundaries

This is a prompt-level mitigation, not a deterministic filter:

- Being listed as an available skill does not prove activation; explicitly invoke it for important delivery work.
- It cannot erase context already read by a model or control tool logs and host UI.
- Its bundled scanner checks explicitly supplied text files and filenames for normalized substring matches and certain invisible or bidirectional Unicode characters. `--root` adds checks of relative directory names; it does not scan a directory recursively. `PASS` does not establish semantic correctness or preservation of required facts.
- Do not change APIs, migrations, tests, snapshots, or pre-existing user work merely to satisfy this skill.
- Use dedicated tools for credentials, privacy, and compliance checks.

See [SKILL.md](no-negative-echo/SKILL.md) for the core workflow and a complete scanner command. Read [high-assurance-finalization.md](no-negative-echo/references/high-assurance-finalization.md) for sensitive information, public sharing, changes that are difficult to reverse, delegated work, long or compacted conversations, inaccessible final surfaces, or explicitly requested strict validation.

## Development and evaluation

```bash
python3 -I -m unittest discover -s tests -p 'test_*.py'
```

The evaluation protocol and public development cases are in [`evals/`](evals/). CI tests the package, installer, scanner, and evaluation scorer. These tests check software behavior; they do not measure how effectively a model follows the skill. Behavioral evaluation requires isolated model runs and independent judgments, and reports the available host activation evidence separately under the documented protocol.

## Further reading

- [Installation instructions](INSTALL.md)
- [Background](BACKGROUND.md)
- [Evaluation protocol](evals/evaluation-protocol.md)
- [License](LICENSE)

## Feedback

When opening an issue, include the original request, actual output, expected output, location, and whether invocation was explicit or implicit. Remove credentials, personal data, and internal project names first.

Licensed under the [MIT License](LICENSE).
