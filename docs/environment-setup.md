# Cloud environment

This repo's sessions run on a personal cloud environment named **`biotech`** (created 2026-11-22),
not on the Default. This file records its configuration so it can be audited, kept in sync, or
rebuilt if lost.

The step-by-step clicking to create it is deliberately not written down here — it was created from a
session via tooling, and UI instructions rot. What matters is the content below.

## Why not the Default environment

Cloud sessions run with a network allowlist. The Default environment's list is empty — only package
registries and MCP servers are reachable — so fetching an ordinary web host fails at DNS resolution.
The Default is shared by every repository, so a host list that only this project needs does not
belong on it.

## Allowed hosts

Verified reachable after creation. Narrow on purpose: when something is missing, the error names the
host and it takes seconds to add.

| Host                         | Why                                                                              |
| ---------------------------- | -------------------------------------------------------------------------------- |
| `huggingface.co`             | Phase 3 publishes the dataset and weights here; also `from_pretrained` for ESM-2 |
| `cdn-lfs.huggingface.co`     | the CDN that actually serves Hugging Face model and dataset files                |
| `www.iedb.org`               | source of the raw binding data, for refreshing or re-exporting it                |
| `services.healthtech.dtu.dk` | DTU Health Tech — NetMHCpan and the MHC motif atlas, the Phase 1 reference       |
| `rest.uniprot.org`           | Swiss-Prot amino-acid background frequencies, used to generate decoys            |
| `www.ncbi.nlm.nih.gov`       | PubMed, and the free full text of *Molecular Biology of the Cell*                |
| `pmc.ncbi.nlm.nih.gov`       | full-text papers                                                                 |
| `www.meetup.com`             | the January reminder reads the Health AI Munich schedule                         |
| `amzn.eu`                    | book lookups                                                                     |

Package managers and MCP server hosts are allowed without being listed. GitHub works through the
session's git proxy and needs no entry.

## Setup script

Runs as root each time a container is built fresh, after the repo is cloned and before Claude
starts. **Keep this in sync with the copy stored on the environment** — editing one does not change
the other.

```bash
set -eu
VENV="$HOME/.venvs/biotech"
[ -x "$VENV/bin/pip" ] || python3 -m venv "$VENV"
if [ -f BioTech/requirements-dev.lock ]; then
  "$VENV/bin/pip" install --no-input -q -r BioTech/requirements-dev.lock
fi
LINE="export PATH=\"$VENV/bin:\$PATH\""
grep -qxF "$LINE" "$HOME/.bashrc" 2>/dev/null || echo "$LINE" >> "$HOME/.bashrc"
```

Why it is shaped this way — each point was established by trying it, not assumed:

- Installs the dev lock so `pyink`, `mdformat`, `nbqa` and `pylint` are present and the CI checks
  run without installing anything first.
- **Virtualenv rather than system-wide.** A plain `pip install -r requirements-dev.lock` *fails* on
  this base image — `Cannot uninstall pyparsing 3.1.1, RECORD file not found`, because the Debian
  package manager owns it. Both paths were tried.
- Guards on `BioTech/` existing, so it cannot break a session with a different repository attached.
  A failing setup script stops the session from starting at all.
- Idempotent: two consecutive runs are clean and the `.bashrc` line is added once.

The script was verified by running it, but **it has not yet run as a real setup script** — that only
happens on the first fresh session on this environment, which is what the validation prompt below is
for.

## Validation prompt

Paste into the first session started on the `biotech` environment. Also worth re-running after
changing the host list or the script.

```text
Validate this environment before we start work. Report each check as pass or fail with the
actual evidence, and do not fix anything yet — I want to see what is broken first.

1. Confirm CLAUDE.md loaded: without re-reading it, tell me what the session-start checklist
   says and what the Slack write rule is.
2. Confirm the repo is present and on the expected branch; show the last commit.
3. Confirm the setup script ran: `which mdformat pylint pyink nbqa` and print their versions,
   without installing anything. If they are missing, the script failed — show me why.
4. Run the CI checks from .github/workflows/ci.yml against the repo and report the result.
5. Network: fetch huggingface.co, services.healthtech.dtu.dk and www.meetup.com. For each, say
   whether it resolved. If one is blocked, name the host rather than guessing a cause.
6. Slack: is the Slack MCP server connected? If yes, give me the session-start Slack digest for
   Bits in Bio, and say "none" if there is nothing worth my attention. If it failed to connect,
   say so and quote the error.
7. Tell me which step of docs/TRACK.md is next, read from the file rather than assumed.

Then stop and wait. Do not start Phase 1 work in this session.
```

Point 3 is the real test — the setup script is unproven until a fresh session proves it. Point 1
matters most in substance, since the teaching protocol loading is what everything else depends on.
