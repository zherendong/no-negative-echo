# Install no-negative-echo

Install only the runtime skill from
<https://github.com/LB623/no-negative-echo>. Do not inspect, modify, or install
unrelated skills, packages, repositories, or host configuration.

## Default installation

If the host provides an official skill installer, use it to install the
repository's `no-negative-echo/` subdirectory. On Codex, prefer
`$skill-installer` and verify discovery in a new session.

Otherwise, use the bundled installer:

1. Select the preset for the current local host:

   | Host | Preset | Default destination |
   |---|---|---|
   | Codex / ChatGPT desktop | `codex` | `~/.agents/skills` |
   | Claude Code | `claude` | `~/.claude/skills` |
   | Cursor | `cursor` | `~/.cursor/skills` |
   | Gemini CLI | `gemini` | `~/.gemini/skills` |
   | GitHub Copilot CLI | `copilot` | `~/.copilot/skills` |

   These presets apply to local hosts with persistent filesystems. Do not guess
   a destination for agents running in cloud, review, web, or temporary
   environments.

2. Clone the repository into a new temporary directory, then run from its root.
   Use the installer, runtime directory, and expected digest from the same
   revision:

   ```bash
   python3 -I -B scripts/install_skill.py \
     --expected-provenance-sha256 b0f8e6df199bdd335d18aa3213b2c180d9e9d0d4bcba276c6dc5b9c1cf6833bf \
     --agent HOST_PRESET
   ```

   Replace `HOST_PRESET` with one value from the table. Python 3.10+ and Git
   must already be installed. Do not install dependencies, use elevation, or
   request credentials.

3. Treat exit status 0 plus the printed `Installed no-negative-echo to ...`
   path as successful file installation. This does not establish host
   activation. If the host cannot reload skills in the current session, report
   that discovery needs a new session; do not run unrelated work merely to
   prove activation.

4. Remove only the temporary clone created for this installation.

The bundled installer performs the package checks itself: it validates the
provenance manifest and file hashes, checks known conflicting installation
locations, and verifies the destination, coordination locks, staged copy, and
installed files. For a normal release installation, do **not** duplicate that
work by reading every repository Python file, running the repository test
suite, enumerating unrelated skill directories, or inspecting unrelated
`package.json` files.

## Custom destinations and audits

For a custom destination, use only a documented skill root for that host:

```bash
python3 -I -B scripts/install_skill.py \
  --expected-provenance-sha256 b0f8e6df199bdd335d18aa3213b2c180d9e9d0d4bcba276c6dc5b9c1cf6833bf \
  --skills-dir /absolute/path/to/skills \
  --discovery-root /absolute/path/to/skills
```

Add another `--discovery-root` only for a discovery root documented for that
host and relevant workspace. Do not search the whole machine for possible roots.

The provenance digest provides byte integrity, not publisher authentication.
Use a signed immutable release and a separately trusted digest when publisher
identity must be authenticated.

Run the repository's complete test suite or perform source review only when the
user explicitly requests an audit or the installation is part of a
high-assurance release process. If a local source has been modified or its
provenance is unrecognized, let the installer fail closed; audit that source as
a separate task instead of silently expanding a normal installation.

## Report

Return only the information needed to act on the result:

- file installation: succeeded or failed;
- installed path, or `none`;
- source commit;
- host discovery: verified or requires a new session;
- exact warning or recovery path printed by the installer, if any.

Do not claim current-session activation without host-native evidence.
